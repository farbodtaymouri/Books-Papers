# Introduction

	Pre-trained language models performance can be improved in different ways:
 
   + _Changing the objective function : Defining new objective loss functions_
   + -Increasing the input size_
   + _Multilingual training_
   + _Adding extra knowledge_: Adding a knowledge base (KB) to provide strict rules to govern what PLM generates
   + _Changing the model size_
   + _Fine-tuning for specific application_

## Modifying pre-training objectives
### ROBERTA
Autoencoder models such as BERT can undergo various training methods to enhance their effectiveness in downstream tasks. For instance, ROBERTA (Robustly Optimized BERT Approach) alters BERT's training objective in the following way (__indicating the design choice and decisions are important__):

 + Increasing the batch size (seeing the data more frequently)
 + Employing different text encoding BPE
 + Dynamic masking after data processing
 + Removing Next Sentence Prediction (NSP)

### ELECTRA
Masked Language Modeling (MLM) approaches, like BERT, typically select a portion of the input sequences (about 15%) and replace certain tokens with a 'MASK' token. The model's task during pre-training is to accurately predict these masked tokens. However, this method has a notable limitation for downstream tasks requiring fine-tuning, as there are no 'mask' tokens in the input sequences during such tasks.

In contrast, ELECTRA (Efficiently Learning an Encoder that Classifies Token Replacement Accurately) introduces a different strategy. In this approach, for any given input sequence, a smaller MLM model replaces some tokens with other plausible alternatives. Then, a separate network, known as the discriminator, whcih is the encoder of transformer architecture examines each token to determine whether it is a replaced token or not.


