## Text Generation Optimization

### KV Caching
+ The autoregressive models generate one token at a time and then append it to the input and repeat the process. In this process, whenever we append the generated token to the input,
the __K_ and _V__ matrices needs to compute for the next token. However, all part of these matrices except the last row were computed already in the previous iteration. Thus we can reuse it to avoid this calculation
and speed up the __inference__ process.
  + Note that, some of the models in huggingFace use such caching automatically when generating tokens. __KV caching__ is one of the optimization method for text generation.
  +  __Also note that, it comes at the cost of keeping two large matrices K and V in the memory and passing to functions__.
 
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/DP_AI/Efficiently%20Serving%20LLMs/image/KV_caching.png)

## Batching for improvinh throughput

```python
def batch_inference(prompts):
    responses = openai.Completion.create(
        engine="text-davinci-003",  # or another version of GPT-3.5
        prompts=prompts,
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
