## Text Generation Optimization

### KV Caching
+ The autoregressive models generate one token at a time and then append it to the input and repeat the process. In this process, whenever we append the generated token to the input,
the __K_ and _V__ matrices needs to compute for the next token. However, all part of these matrices except the last row were computed already in the previous iteration. Thus we can reuse it to avoid this calculation
and speed up the __inference__ process.
  + Note that, some of the models in huggingFace use such caching automatically when generating tokens. __KV caching__ is one of the optimization method for text generation.
  +  __Also note that, it comes at the cost of keeping two large matrices K and V in the memory and passing to functions__.
 
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/KV_caching.png)

## Batching for improvinh throughput
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
+ The following code shows how LoRA can be implemented for a specific layer in BERT for fine-tuning.
        
