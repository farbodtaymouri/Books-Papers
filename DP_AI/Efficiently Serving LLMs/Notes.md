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
