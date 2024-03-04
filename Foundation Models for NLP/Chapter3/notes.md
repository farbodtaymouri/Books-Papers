

# Introduction 

Pre-trained language models performance can be improved in different ways:
 
   + _Changing the objective function : Defining new objective loss functions_
   + -Increasing the input size_
   + _Multilingual training_
   + _Adding extra knowledge_: Adding a knowledge base (KB) to provide strict rules to govern what PLM generates
   + _Changing the model size_
   + _Fine-tuning for specific application_

## Modifying pre-training objectives for Masked Language Models (MLM)
### ROBERTA (#ROBERTA)
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

### DeBERTa
DeBERTa (Decoding enhanced BERT with disentangled attention) improved BERT and state of the art of PML by introducting two new novel technique.
 + __Disentangled attention__ : Normally the attention vector of a word is the sum of the word embedding and the corresponding position embedding. However, Disentangled attention, represents each word using two separate vectors representing both content and the position, respectively. Attention weights among the words are computed using dientangled matrices based on their contents and __relative positions__.
 + __Enhanaced Masked Decoder__:  In MLM, the languege model predicts the masked token based on the surrounding contents and their positions (releative). Disentangled attention considers the RELATIVE postion of the context words. However, the ABSOLUTE position of the words is important. __For example, the statement 'A new store opened beside the mall' where the two words 'mall' and 'store' are masked for the prediction. Although, 'store' and 'mall' have similar local context but one is the subject and the other one is the object. Thus, it is importnat to account the absolute position of words in the language modeling process__. Note that, the disentangled attention only consider the relative psoition of the context words and not the absolute position. To achieve this goal, DeBERTa, incorporates the absolute word position embeddings right efore the softmax layer where the model decodes (precits) the masked words. Note that, BERT add the positional embedding of a token at the begining to the contextual embedding, DeBERTa add positional embedding before the softmax layer and call it Enhanced Masked Decoder (EMD). 
 + This method calculates the attenion output from two sets matrixes that represent the content and position embeddings of each token. Three matrices consider the content-to-content, content-to-position and position-to-content relationship amongs the tokens.
 + Also the paper uses Virtually Adversrial Training (VAT) method to improve the model's performance on downstream tasks. VAT in general has 4 steps to provide a robust model
   +  Get the training dataset
   +  initially train the model
   +  select some samples from the training data and perturbed it to maximize the error of the trained model (optimization)
   +  given the adversary examples and the previous training data, train the model.

   Note that, in NLP, the perturbation is done on embeddings rather than on words or tokens directly.
$$\text{given }  \hat{y} = f(\mathbf{X}), \text{find } \mathbf{X} \text{ such that } \max(y - \hat{y})$$

### SpanBERT
Many NLP applications require reasoning on two or more spans of a text. SpanBERT intorduces __span-level pre-training__ that is good so tasks such as correference resolution, relation extraction and question answering. This method differes in two ways compared to BERT
+ The masking mechanism is done for contigous spans of texts rather than for tokens.
+ A new objective function is defined where a token in the masked span is predicted using the boundary tokens of the span and it's relative position in the span. For example, for a sentece like "Farbod drives [s] 4 wheel drive [e] cars in Australia, then each token in the span part ' 4 wheel drive' is predicted using boundary tokens 'drives' and 'cars'. This new objective is called span boundary objective (SPO).
  ![SBO](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/SBO.png)
  More specifically if $\mathbf{x}_1,...\mathbf{x}_n$ is the output encoder of BERT and if ${x_s,....,x_e} shows the text span. Then for each token $x_i$ in the span text the prediction $\mathbf{y}_i$ is calculated as follow.:
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/CodeCogsEqn.png)
  where $\mathbf{p}_i$ is the reletavie position embeddings in span text. This is implemented as a two layer Feed Forward neural net. The paper calculate to total loss as follow:
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/SBO_loss.png)
  In this equation, MLM is the loss function for predicting the masked token. Similar to what BERT uses.

+ __Pre_Training__ is done using the intorduced masking and the objective function defined above on two datasets BookCorpus and English Wikipedia. Note that this is only __pre_training__.
+ The __evaluation on downstream__ tasks such as Q/A, Coreference Resolution, Relation Extractioon, and GLUE require fine-tuning as explained in the paper. Evaluation is done on several datasets for question answering such as SQuAD1,2 and datasets for relation extraction (TACRD) and coreference resolution (OntoNotes), and language understanding dataset (GLUE).

### DeBERTaV3
Scaling Pre-trained Languagne Models (PLM) to billion paratmers increase their ability in different NLP, NLU tasks. However, training PLMs efficiently with less data can surpass the state of the art as well. Some examples for efficient pre-training of large language models are ROBERTA (increasing the batch size) and ELECTRA (replacing MLM with Replaced Token Detection RTD and also sharing embeddings between the generator and discriminator). The followings are the Loss functions for the generator and the discriminator.

![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/electra_loss.png)

+ DEBERTA increases the pre-training efficiency by introducing disentagled attention mechanism and Enhanced Masked Decoder. DeBERTaV3 introduces __two new approaches__ for efficient pre-training of large language models
  + Using __RTD__ where the LLM is the discriminator and the generator replce the some input tokens with their synonyms to confuse the dicsrimintor and the goal of discriminator is to detect whether a token is replaced or no. So the generator uses L_MLM to produce vector embeddings close to each other whereas discrimintor uses L_RTD to generate vector embeddings that are far from each other to make the task of discrimination easy. This results in __tug of war__ issue and affects the training efficiency.
  + Via RTD two netwrks i.e., the generator and the discriminaotr are trained together.
     + If they share the same embeedgins (ES) which is multitask learning (embeddings that are used in both gen and disc) then  we calculate the sum of L_MLM and L_RTD at each step and backpropagate the errors to uopdate the weights. Resulting in inefficient training (Fig 1(a)) but it avoids the large number of paramters.
     + In contrast, each netwroks can have their own vector embeddings (NES) and we generated currupted X, then calculate L_MLM first update the generator's paramter. After that, the currputed input is fed to discriminator to claculate L_RTD then the discriminator's paramters are updated, see Fi. 1(b). It increases the efficiency of training but, it doesn't improve the performance on __down stream NLI,NLU tasks__
     + DeBERTaV3 introduces __Gradient-Disentangled Embeddin Sharing (GDES). In this approach, the discriminator's embedgins matix E_D is __reparametarized__ as $E_D = StopGradient(E_g) +\Delta E$ where delta E is initialized with zero. First, the gen provides an input for the disc and we calculate L_MLM and updated both $E_D, E_G$. Next, the disc provides the output and calculate L_RTD. However, we only update $E_D$ via $\Delta E$ meaning that only $\Delta E$ gets affected. We then sum $E_G$ and $\Delta E$ to construct $E_D$. This way of reparameterizing helps the dic to get valueable embedding information from the gen and do not interfere with it after calculating L_RTD.
    ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/ES_NES.png)
+ The model is pre-trained on English Wikipedia and BookCorpus datasets.
+ After pre-training the model is fine-tuned on 8 GLUE tasks for most sentence classification tasks by plugging a classification head on top of the hidden states of the [CLS] token at the last layer.
+ The model also evaluated on NLU datasets such as Question Answering (SQuAD v2), RACE, and NLI tasks such as MNLI and CoNLL.

## Autoregressive Language Models 


## Mutlitask Question Answering Network (MQAN)

+ The traditional NLP examples have inputs $x$ and outputs $y$ and the underlying task $t$, e.g., sentiment analysis, summerization, semantic role labeling, etc., that is provided through __explicit__ modeling.  Meta-learning, Mutli-tasking includes $t$ as a part of inputs. In MQAN, different NLP tasks such as sentiment analysis, summerization, Question answering, and etc, will be represented/formulated using natural language questions. This way, a single model can learn can leverage multitask learning and be able to generalize to completely new tasks.
+ ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/MQAN.png)
+  decaNLP dataset for multitask learning contains the following tasks where all of them are represented and formulated as question answering:
  ![](https://github.com/farbodtaymouri/Books-Papers/blob/main/Foundation%20Models%20for%20NLP/image/decaNLP.png)




### GPT2
+ Traditional Machine Learning systems excell at tasks they are trained for by using a combination of large datasets, high-capacity models, and supervised learning. However, such models are sensitive to slight changes in the data distribution and task specification. These systems are called __narrow experts__.
+  Single task training on single domain datasets is the major contributor of the lack of generalization observed in the current ML systems. Hence Multitask learning is promosing framework for improving general performance.
+  Multi-task learning from a meta-learning perspective needs several paris where a pair contains a single traiing example from the data distribution and objectives. However, current ML systems still need hudreds to thousands examples tp induce a function and generalize well.
+  Language models can be shown to have good performance on down-stream tasks in a zero-shot setting (without any parameter or architecture modification). 
