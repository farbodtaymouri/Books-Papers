## Table of Contents
- [GLUE A MULTI-TASK BENCHMARK AND ANALYSIS](#glue-a-multi-task-benchmark-and-analysis)
  - [Sub Header 1](#sub-header-1)
  - [Sub Header 2](#sub-header-2)



# GLUE A MULTI-TASK BENCHMARK AND ANALYSIS

## Evaluation

In order to evaluate the Q/A system, we need to define several benchmarks to understand how good the model can perform different tasks. The model, whether it is fine-tuned or using RAG needs to be evaluation based on different benchmarks. Similar to GLUE that examines any LLM on 9 tasks:

* __Single-sentence task__: 

    * Datasets contains dataset containing a set of sentences labelled as grammatically correct or incorrect (similar to CoLa):

      * Sentence: "She enjoys reading books." Label: Correct

      * Sentence: "Him likes to play soccer." Label: Incorrect

   * Dataset containing sentences with sentiments, i.e., positive or negative (like SST2):

     * Sentence: "The movie was fantastic and I thoroughly enjoyed it." Label: Positive

     * Sentence: "I found the plot boring and the characters unrelatable." Label: Negative

* __Similarity and Paraphrasing Tasks__ :

    * Datasets contain a pair of sentences annotate with a label that shows how similar two sentences are or labeled with ‘paraphrase’ ‘not a paraphrase’ like MRPC, QQP SSTB, datasets. 

      * Paraphrase
      
          :
          
          Sentence 1: "The scientists discovered a new species of frog in the rainforest."
          
          Sentence 2: "In the rainforest, a new type of frog was identified by researchers."

      * Not a Paraphrase

          :
          
          Sentence 1: "The football team won their match last night."
          
          Sentence 2: "The orchestra delivered a stunning performance yesterday evening."

    __Note that, since it is kind of classification or regression task, it can not be used directly to measure the performance of a Q/A system.__ It is not very relevant to use it for a           Q/A system, The trick is to feed one sentence of the pair to the system and ask the question like ‘question: "Can you rephrase this sentence: ‘The cat sat on the mat'?"’, then the         generated sentence can be compared with the second sentence in the pair. For comparing the generated sentence and the second sentence, semantic similarity methods such as BERT can be       used. One can then measure the model’s performance and try to fine-tune the model for weaknesses. Note that, this task only measure the paraphrasing capability of the Q/A system.

* __Inference Tasks__:
  * Dataset containing a sentence pair with textual entailment annotations. Given a premise sentence and a hypothesis sentence, the task is to predict the rleatioship between the paired sentences, i.e., _entailment_, _contradition_, and _neutral_. MNLI is such a dataset as follow:
      * Premise: The cat sat on the mat.
        
          Hypothesis: There is a cat on the mat.
        
          Label: Entailment
      
      * Premise: It was raining heavily last night.
        
        Hypothesis: The weather was clear last night.
        
        Label: Contradiction
      
      * Premise: Sarah went to the store to buy some fruits.
        
        Hypothesis: Sarah likes apples.
        
        Label: Neutral
      
      * Premise: The concert was incredibly loud and people were excited.
        
        Hypothesis: The event was a quiet reading session.
        
        Label: Contradiction
      
      * Premise: I placed my books in the bag.
        
        Hypothesis: I have a bag.
        
        Label: Entailment

  # Note On Span Detection and Text Generation
  
Span detection tackles the challenge of extracting pertinent information from a given context in response to a question. Essentially, it involves identifying the relevant segment within the context that answers the question. Notably, models used for span detection don't create new text. Instead, based on the question posed, they pinpoint the start and end points of the relevant information in the context. __This makes them highly effective for applications where precision in information retrieval is crucial, and there's a need for exact extraction__.

To illustrate, consider this example:

Context: 'The cat ate cheese'
Question: "What did the cat eat?"


The input sequence - '[CLS], 'What', 'did', 'the', 'cat', 'eat', '[SEP]', 'The', 'cat', 'ate', 'cheese', '[SEP]' - is processed by the model. The model then outputs two probabilities for each token in the context - StartScore and EndScore - which signify the span that pertains to the question. The highest values of these scores determine the start and end of the answer.
It's important to understand that for effective span detection, the model should use context-aware tokens. Therefore, models from the BERT family (autoencoders), such as BERT, RoBERTA, ALBERT, and DeBERTa, are more suitable for this task than autoregressive models like GPT, which rely on masked tokens.


