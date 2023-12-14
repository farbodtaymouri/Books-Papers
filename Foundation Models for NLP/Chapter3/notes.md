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
+ Masked Language Modeling (MLM) approaches, like BERT, typically select a portion of the input sequences (about 15%) and replace certain tokens with a 'MASK' token. The model's task during pre-training is to accurately predict these masked tokens. However, this method has a notable limitation for downstream tasks requiring fine-tuning, as there are no 'mask' tokens in the input sequences during such tasks.

 + In contrast, ELECTRA (Efficiently Learning an Encoder that Classifies Token Replacement Accurately) introduces a different strategy. In this approach, for any given input sequence, a smaller MLM model replaces some tokens with other plausible alternatives. Then, a separate network, known as the discriminator, whcih is the encoder of transformer architecture examines each token to determine whether it is a replaced token or not. For example, the input sequence 'The chef cooked the meal" will be trasnformed to "The chef ate the meal" using the generator (some words are replaced by their synonyms. Next, the discrimintaro tag each word with "original" or "replaced". 

 + Both the generator and the discriminator models are trained jointly using MLE method. Note that, the others found that adversarial training performs worse than jointly training.

 + Note that, to compare the complexity for training and inferencing between ELECTRA and basedlines such as BERT, the authors report FLOPS (Floating Point Operations). Note that, FLOPS is not operations at the hardware level, and only mathematical operations count. For example, esp() is considered as a floating point operation.

### ALBERT
Two main challenges in pre-training large models are the memory consumption and the training speed. In practice, however, distill laege models to small ones for downstream applications.
+ One of the way to make memory consumption more efficient is model parallelization where each layer can be trained in a single GPU. Note that, it doesn't solve the training speed.
  
+ ALBERT (A Light BERT), whihc is the encoder part of the transfomer architecture like BERT, addresses the issue with memory and training time associated with BERT. In short:
  + It uses a factorized embedding parameterization. Meaning, in decomposes the large embedding matrix of size V*H (V, the vocab size, and H the embedding size) into V*E, and E*H. This way, albert is able to create larger models woth much fewer paramteres compared to BERT (ALBERTxxlarge has 235M paramter with H=4096, BERT large has 334M paramter with H=1024). The rationale behind such seperation is the size of V is large (30K in BERT) thus H needs to be large. However, during training only a few paramteres of H are updates and this is the inefficiency part.
    
  + Cross-layer parameter sharing where the paramters of the attention layers and fully connected layers in each layer of the encoder with other layers. This way, the model's paramters won't increase by increaing the depth of network.
 
  + ALBERT uses two loss function. One is MLM simlar to what BERT does. The second, change the training objective of Next Sentence Prediction (NSP) which is binary classification task, to Sentence Oorder Predictin (SOP) which focuses on inter-sentence coherence. In SOP, the system gets two segment (could be a set of sentences) and it classifies whether they are consecutive in the document or no. The postive examples, indicate two segments are consecutive and the negative examples are created by reversing the order of two consecutive segments.
 
+ ALBERT is pre-trained on BOOKCORPUS and English Wekipedia datasets where the inputs are formed as [CLS]x1[SEP]x2[SEP] where x1 and x2 are input segments (more than one sentence).  


