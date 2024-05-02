## Serving LLMs efficiently by text Generation Optimization 

### KV Caching
+ The autoregressive models generate one token at a time and then append it to the input and repeat the process. In this process, whenever we append the generated token to the input,
the __K_ and _V__ matrices needs to compute for the next token. However, all part of these matrices except the last row were computed already in the previous iteration. Thus we can reuse it to avoid this calculation
and speed up the __inference__ process.
  + Note that, some of the models in huggingFace use such caching automatically when generating tokens. __KV caching__ is one of the optimization method for text generation.
  +  __Also note that, it comes at the cost of keeping two large matrices K and V in the memory and passing to functions__.
 
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/KV_caching.png)

## Batching for improving throughput
+ The whole idea of batching in LLMs is to avoid generating tokens one at the time and we pad all input sequences togather and pad them and then send them to the LLM for processing. This way increase the throughput of the system.
+ __Note that, batching improves the throughput (number of request per minutes) but might affect the latancy. The reason tha the latency increases (geeting response slower) is that in batching even short sequences needs to wait to larger ones to finish their token generations and then the batch results are sent back__.

  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/batch_llm.png)
+ An example of batching for topic detection in inputs. Note that the __prompts__ paramter in the following code is responsbile to take the input batch as a list of sequences. 

```python
      def batch_inference(prompts):
          responses = openai.Completion.create(
              engine="text-davinci-003",  # or another version of GPT-3.5
              prompts=prompts,   #The input text or texts you provide to the model. You can provide a single string or a list of strings for batch processing.
              max_tokens=150,
              n=1,  # Number of completions generated per prompt
              stop=None  # Stop sequence, if applicable
          )
          return responses
      
      # Helper function to make the messages in the required ChatCompletion format
      def make_messages(system_prompt: str, user_prompt: str) -> str:
          messages = []
          messages.append({"role" : "system", "content" : system_prompt})
          messages.append({"role": "user", "content": ',,'.join(user_prompt) })
          return messages
      ########################
      # Example prompts
      user_message = [
          "ID_1234: Lower prices - Customer are not happy with your billion dollar profits.",
          "ID_1534: More checkouts open so you don&#39;t have to done their work by scanning and packing your own groceries",
          "ID_1236: The freezer section &amp; toilet paper section of the store looks and feels like they&#39;ve been neglected compared to other areas of the store."
      ]
      
      system_prompt = "Find three topics in each text ,, in no more than 4 words. Keep the Ids in the output"
      response = call_gpt(make_messages(system_prompt, user_message), temperature=0.1)
      print(response)
      >>
      ID_1234: Lower prices, Customer dissatisfaction, Billion dollar profits
      ID_1534: More checkouts, Scan and pack, Open checkouts
      ID_1236: Freezer section, Toilet paper section, Neglected areas
```
  ## Quantization to Improve Inference Speed
  
  + Neural networks and LLMs have a large number of parameters including lots of floating point numbers. most floating point numbers are FP32 meaning that they have one bit for the sign, 8 bit for the exponent (i.e., $2^{(bias-exponent)}$) and 23 bit for mantissa (i.e., the floating point numbers). As one can see using BF16 we can reduce the number of floating points or mantissa but do not change the range of covering numbers. Meaning that we can work with lower precision numbers.
  
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/Floating_point.png)

+ The purpose of quantization in a neural network is to decrease the precision of the model's weights. This reduction in precision helps to __lower the memory required__ to load the model and __speeds up the computation time__ during both the forward and backward passes. Note that, in some situation, we can dequantize some layers if we want to decrease the error incurred by quantization.
+ Note that the quantization is a lossy compression, meaning that we can not recover the original weights of the model completely.
+ the __zero one__ quantization technique is a method that maps the weights into 0-255 range number, indeed, each weight is first transfer to 0,1 and then scale to 0-255
  $$z  = \frac{x-min}{max-min} * 255$$

  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/zero-one.png)
+ In summary the trained model is quantized first and then it will go into the production. Note that, when we quantize the model we can dequantize some of its layer or all of it using the inverse of the above formula. In some situations, some layers of the model needs to be dequantized to improve the error encountered by compression.
### Implementation
+  Although one can change the dtype of model's weight to quantize them, Pytorch has an interesting library, https://pytorch.org/tutorials/recipes/recipes/dynamic_quantization.html,  where you can use it easily to quantize the whole model, or specific layer types such as liners ones. See the following example where I used DeBERTa for sentiment analysis both in origirnal format and in compressed( quantized one).
```python
    
    from transformers import DebertaV2Tokenizer, DebertaV2ForSequenceClassification
    import torch
    
    # Load the model and tokenizer
    model_name = 'microsoft/deberta-v2-xlarge'
    tokenizer = DebertaV2Tokenizer.from_pretrained(model_name)
    model = DebertaV2ForSequenceClassification.from_pretrained(model_name)
    
    # Set the model to evaluation mode
    model.eval()

    # Defining a function to wrap the model for classification
    def classify_sentiment(text, model, tokenizer):
      # Encode the text using the tokenizer
      encoding = tokenizer(text, return_tensors='pt', padding=True, truncation=True, max_length=512)
  
      # Get predictions from the model
      with torch.no_grad():
          outputs = model(**encoding)
  
      # Process the output to get the predicted class (e.g., positive or negative)
      probabilities = torch.nn.functional.softmax(outputs.logits, dim=-1)
      predicted_class = torch.argmax(probabilities, dim=-1)
  
      return predicted_class
    ##########################################################
     # running the model to get some numbers
      text = "I love this product!"
      %timeit prediction = classify_sentiment(text, model, tokenizer)
      print(' Initial model size in GB:', model.get_memory_footprint()/(1024*1024*1024))
      print(f'Prediction: {prediction}')

      >> 3.53 s ± 964 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
      >> Initial model size in GB: 3.304176338016987
      >> Prediction: tensor([0]

```
+ As you can see from the above code the original model has __3.53 GB__ size and the __latency time__ for classifiying an example is __3.53 s__. Now, lets quantize it by compressing only the linear layersffrom FP32 to int8 and serve the compressed model using the same example:
```python

    #Quantizing the model
    from torch.quantization import quantize_dynamic
    
    # Specify the layers you want to dynamically quantize
    # Typically, quantizing the Linear layers yields significant performance improvements
    model_quantized = quantize_dynamic(
        model, 
        {torch.nn.Linear},  # Targeting linear layers for quantization
        dtype=torch.qint8    # Using 8-bit integers
    )
   ######################################
  # Test the function
    print(' Compressed model size in GB:', model_quantized.get_memory_footprint()/(1024*1024*1024))
    text = "I love this product!"
    %timeit prediction = classify_sentiment(text, model_quantized, tokenizer)
    print(f'Prediction: {prediction}')

    >> Compressed model size in GB: 0.7628841400146484
    >> 1.67 s ± 269 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
    >> Prediction: tensor([0])
```
+ From the compressed model you can see that the size is reduced 5 times, and the latency time improved arouind 2.5 times and at the same time the model classified it correctly.
+ Note that, there is always a trade-off between the quantization level and the accuracy level accordingly.

## Low Rank Adaptation (LoRA) for Efficient Fine-Tunning 
+ For fine-tuning any large language model (LLM), the standard procedure involves preparing a training set and processing the instances through the model. This involves generating output via a forward pass, followed by calculating gradients and updating the model's weights during a backward pass. If the LLM has billions of parameters, updating each one during every backward pass—which is necessary for computing gradients—becomes costly and inefficient. To tackle this challenge, one can introduce a new set of parameters that approximate the original weight matrix 
$W$ using lower-rank matrices, effectively 
$𝑊≈𝐴𝐵$. This approach involves reparameterizing the model by adding new learnable parameters while keeping the original ones fixed.
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/LoRa.png)
+ There some important things to note, from the above figure note that, for fine-tuning, we add new __learnable__ paramters $A,B$ (low-ranked matrics) to the model, and in the backward pass we only update $A,B$ and $W$ is not changing. When the fin-tuning is finished, we deploy the whole model, including the new paramters $A,B$ into production. The pros and cons of this approach:
  + Pros:
    + Allows for efficient fine-tuning with improved speed and lower expenses by only updating the low-rank matrices A and  B.
    + Rapidly adaptable to new scenarios that require fine-tuning.
  + Cons:
    + Increases memory consumption due to the addition of extra variables.
    + Raises the serving costs because of additional computational requirements for new parameters.
    + Potentially heightens latency as a result of the new variables, though this can be mitigated with adequate parallelization resources.
    + It might require larger GPU to mitigate the drop in latency and serving the larger model
+ Indeed, LoRA, is an __affordable__ and __quick fine-tuning method__, however, in long-term fine-tuning the original model might be more beneficial.
+ Note that, this low-rank adaption can be achieved for only a single layer of the model, or all layers.
### Implementation
+ The following code shows how LoRA can be implemented for a specific layer in BERT for fine-tuning.
  + Initally we set the gradient of the model's paramter to zero as we do not update them. 
```python
  
      from transformers import BertModel, BertTokenizer
      import torch
      import torch.nn as nn
  
      model_name = 'bert-base-uncased'
      model = BertModel.from_pretrained(model_name)
      tokenizer = BertTokenizer.from_pretrained(model_name
  
      # Freeze all original parameters in the model (as we don't update them in LoRA
      for param in model.parameters():
          param.requires_grad = False
```
+ We define a custom class that takes as input the original weights of that layer and create two random (differentiable) matrices and add them togather to create the new LoRA layer and replace it with the original layer in the model. Note that, this class can be re-used for different layers of the model.
  + Note that, the number of extra paramters are __rank*weight.dim[0]*2__. So the larger the rank of $A$ or $B$ the more extra variables added to the model.   
```python

      # Defining our custom class to implement LoRA
      class LoRALayer(nn.Module):
          def __init__(self, original_weight, rank=10):
              super(LoRALayer, self).__init__()
              self.original_weight = original_weight
              self.A = nn.Parameter(torch.randn(original_weight.size(1), rank))  #Prameter class allows the variable to be differentiable
              self.B = nn.Parameter(torch.randn(rank , original_weight.size(0)))
      
          def lora_weight(self):
              # Calculate the low-rank update
              lora_update = torch.matmul(self.A, self.B)

              # Add the low-rank update to the original weights
              # The primary purpose of torch.nn.parameter.Parameter is to differentiate regular 
              #tensors from parameters that should be considered part of the model's trainable weights.
              new_weight = torch.nn.Parameter( self.original_weight + lora_update)
              return new_weight
      
          
          def forward(self, x):
              new_weight = self.lora_weight()
              return torch.nn.functional.linear(x, new_weight)
      #---------------------------------------------------------------------------
      layer_id = 0  # Modify the first layer to be fine-tuaned using LoRA
      attention_layer = model.encoder.layer[layer_id].attention.self
      
      # Replace the original 'query' weight with LoRALayer
      original_query_weight = attention_layer.query.weight
      lora_query = LoRALayer(original_query_weight)
      attention_layer.query.weight = lora_query.lora_weight()    #Replacing the Original weight with the new one based on LoRA class defined
    #---------------------------------------------------------------------------
```
+ We can check which paramters of the model will be updated if we train (fine-tune) the model with the modified layers
```python
    # Freeze all original parameters in the model
    for name, param in model.named_parameters():
        if(param.requires_grad):
          print(name , param.requires_grad)
    >> encoder.layer.0.attention.self.query.weight True
```
+ Finally, we will update the model's paramter in the fine-tuning step as follow
```python
    # Assume 'model' is already defined and parameters have been appropriately set for requires_grad
    trainable_params = [p for p in model.parameters() if p.requires_grad]
    optimizer = optim.Adam(trainable_params, lr=1e-5)
```

## Multi-LoRA Serving Architecture
+LoRA is an effective technique for efficiently fine-tuning large LLMs by training a new set of small, trainable parameters (i.e., low-ranked matrices). The benefits of using LoRA are particularly apparent in enterprise settings in the following scenarios:
  +An enterprise aims to adapt a base LLM for various use-cases or business divisions.
  + An enterprise seeks to minimize the costs of fine-tuned models by maintaining a base LLM and only modifying some configurable components.
+ In the scenarios outlined above, the LoRA architecture becomes crucial for deploying an LLM across different applications. Refer to the diagram below:
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/LoRA_enterprise.png)
+ As illustrated, imagine two hypothetical business units, one focused on pricing and the other on supply chain management. Each unit requires a separate chatbot tailored to respond to specific queries relevant to their operations. By employing LoRA, we can start with a base model and fine-tune it using documents pertinent to each business unit, saving only the LoRA layers for each unit. Upon receiving a request, the relevant LoRA layer is retrieved and integrated into the model to generate a response. This approach allows the use of a single base model supplemented by specific LoRA layers, significantly reducing the complexity and overhead associated with managing separate LLMs for different applications.
+ Please note that one can also employ batch serving or continuous batch serving in parallel with this method.

## LoRAX (A framework for efficiently serving fine-tined LLMs)
+ Usually, at the enteprise level it is difficutl to deploy a multiobillion paramter LLMs for each task as it is costly and not efficent in terms of serving. LoRAX is a framework to efficently serving several LLMs or fine-tined version of a base LLM for different purposes such as text summarization and sentiment analysis.
import time
import openai
import asyncio

# Initialize duration lists
durations_s = [[] for _ in range(3)]

# List of questions
questions = [
    "If you could have dinner with any historical figure, who would it be and why?",
    "What is one technological innovation you believe will have the most impact on society in the next decade?",
    "If you had to live in another country for a year, which one would you choose and what would you most look forward to experiencing there?"
]

async def run(max_new_tokens, i):
    start_time = time.time()
    for q in questions:
        # Await the response directly, as create returns a single response
        response = await async_client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {
                    "role": "system",
                    "content": "You are a friendly chatbot who always responds in the style of a pirate",
                },
                {"role": "user", "content": q},
            ],
            max_tokens=max_new_tokens
        )
        
        # Calculate the time taken and print the response
        durations_s[i].append(time.time() - start_time)
        print("Question:",q, "\n response:", response.choices[0].message.content)

# Execution
async def main():
    start_time = time.time()
    max_tokens_list = [100, 10, 10]
    await asyncio.gather(*[run(max_new_tokens, i) for i, max_new_tokens in enumerate(max_tokens_list)])

# Run the main async function
await main()
