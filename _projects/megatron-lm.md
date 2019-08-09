---
title: "MegatronLM: Training Billion+ Parameter Language Models Using GPU Model Parallelism"
collection: projects
author: Jared Casper, Patrick Legresley, Mohammad Shoeybi, Mostofa Patwary, Raul Puri, Bryan Catanzaro
permalink: /MegatronLM
excerpt: 'We train an 8.3 billion parameter transformer language model with 8-way model parallelism and 64-way data parallelism on 512 GPUs, making it the **largest transformer based language model ever trained at 24x the size of BERT and 5.6x the size of GPT-2**'
date: 2019-08-13
---

Larger language models are dramatically more useful for NLP tasks such as article completion, question answering, and dialog systems. Training the largest neural language model has recently been the best way to advance the state of the art in NLP applications. Two recent papers, [BERT](https://arxiv.org/abs/1810.04805) and [GPT-2](https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf), demonstrate the benefits of large scale language modeling. Both papers leverage advances in compute and available text corpora to significantly surpass state of the art performance in natural language understanding, modeling, and generation. Training these models requires hundreds of exaflops of compute and [clever memory management](https://arxiv.org/abs/1604.06174) to trade recomputation for a reduced memory footprint. However, for very large models beyond a billion parameters, the memory on a single GPU is not enough to fit the model along with the parameters needed for training, requiring model parallelism to split the parameters across multiple GPUs. Several approaches to model parallelism exist, but they are difficult to use, either because they rely on custom compilers, or because they scale poorly or require changes to the optimizer.

In this work, we implement a simple and efficient model parallel approach by making only a few targeted modifications to existing [PyTorch](https://openreview.net/pdf?id=BJJsrmfCZ) transformer implementations. [Our code](https://github.com/nvidia/megatron-lm) is written in native Python, leverages mixed precision training, and utilizes the [NCCL library](https://developer.nvidia.com/nccl) for communication between GPUs. We showcase this approach by training an 8.3 billion parameter transformer language model with 8-way model parallelism and 64-way data parallelism on 512 GPUs, making it the **largest transformer based language model ever trained at 24x the size of BERT and 5.6x the size of GPT-2**. We have published the code that implements this approach at [our GitHub repository](https://github.com/NVIDIA/Megatron-LM). 

Our experiments are conducted on NVIDIA’s [DGX SuperPOD](https://devblogs.nvidia.com/dgx-superpod-world-record-supercomputing-enterprise/). Without model parallelism, we can fit a baseline model of 1.2B parameters on a single V100 32GB GPU, and sustain 39 TeraFLOPS during the overall training process, which is 30% of the theoretical peak FLOPS for a single GPU in a DGX2-H server. Scaling the model to 8.3 billion parameters on 512 GPUs with 8-way model parallelism, we achieved up to **15.1 PetaFLOPS sustained performance** over the entire application and reached 76% scaling efficiency compared to the single GPU case. Figure 1 shows more detailed scaling results.

<figure>
<center><img src="images/megatronlm/flops_scaling.jpg" height="500" width="500"></center>
<center><b>Figure 1:</b> Model (blue) and model+data (green) parallel FLOPS as a function of number of GPUs. Model parallel (blue): up to 8-way model parallel weak scaling with approximately 1 billion parameters per GPU (e.g. 2 billion for 2 GPUs and 4 billion for 4 GPUs). Model+data parallel (green): similar configuration as model parallel combined with 64-way data parallel.</center>
</figure>


**Multi-GPU Parallelism**

The typical paradigm for training models has made use of weak scaling approaches and distributed data parallelism to scale training batch size with number of GPUs. This approach allows the model to train on larger datasets, but has the constraint that all parameters must fit on a single GPU. Model parallel training can overcome this limitation by partitioning the model across multiple GPUs. Several general purpose model parallel frameworks such as [gPipe](https://arxiv.org/abs/1811.06965) and [Mesh-TensorFlow](https://arxiv.org/abs/1811.02084) have been proposed recently. gPipe divides groups of layers across different processors while Mesh-TensorFlow employs inter-layer model parallelism. Our approach is conceptually similar to Mesh-TensorFlow, we focus on inter-layer parallelism and fuse GEMMs to reduce synchronization. However, we only make a few targeted modifications to existing PyTorch transformer implementations to employ model parallelism for training large transformers. Our approach is simple, does not require any new compiler or code re-wiring for model parallelism, and can be fully implemented with insertion of few simple primitives (**_f_** and **_g_** operators in Figure 2) as described in the remainder of this section.

We take advantage of the structure of transformer networks to create a simple model parallel implementation by adding a few synchronization primitives. A transformer layer consists of a self attention block followed by a two-layer multi-layer perceptron (MLP). We introduce model parallelism in both of these blocks separately. We start by detailing the MLP block as shown in Figure 2a. This block consists of two GEMMs with a [GeLU](https://arxiv.org/pdf/1606.08415.pdf) nonlinearity in between followed by a dropout layer. We partition the first GEMM in a column parallel fashion. This allows the GeLU nonlinearity to be independently applied to the output of each partitioned GEMM. The second GEMM in the block is parallelized along its rows and takes the output of the GeLU layer directly without requiring any communication. The output of the second GEMM is then reduced across the GPUs before passing the output to the dropout layer. This approach splits both GEMMs in the MLP block across GPUs and requires only a single all-reduce operation in the forward pass (<b>_g_</b> operator) and a single all-reduce in the backward pass (<b>_f_</b> operator). 



<figure>
<center><img src="images/megatronlm/MLP_SelfAttention.jpg" height="500" width="500"></center>
<center><b>Figure 2:</b> (a): MLP and (b): self attention blocks of transformer. <b><i>f</i></b> and <b><i>g</i></b> are conjugate, <b><i>f</i></b> is an <b>identity</b> operator in the forward pass and <b>all-reduce</b> in the backward pass while <b><i>g</i></b> is an <b>all-reduce</b> in forward and <b><i>identity</i></b> in backward.</center>
</figure>



As shown in Figure 2b, for the self attention block we exploit inherent parallelism in the multihead attention operation, partitioning the GEMMs associated with key (K), query (Q), and value (V) in a column parallel fashion such that the matrix multiply corresponding to one attention head is done locally on one GPU. This allows us to split per attention head parameters and workload across the GPUs, and doesn’t require any immediate communication to complete the self-attention. The subsequent GEMM from the output linear layer (after self attention) is parallelized along its rows and takes the output of the parallel attention layer directly, without requiring communication between the GPUs. This approach for both the MLP and self attention layer fuses groups of two GEMMS, eliminates a synchronization point in between, and results in better scaling performance. This enables us to perform all GEMMs in a simple transformer layer using only two all reduces in the forward path and two in the backward path (see Figure 3). Lastly, the output logit layer is also partitioned along the vocabulary dimension, and to avoid a massive all-gather across the vocabulary dimension, we fuse this layer with the cross entropy loss. 

This approach is simple to implement, because it requires only a few extra all-reduce operations be added to the forward and backward pass. It doesn’t require a compiler, and is orthogonal to the kind of pipeline model parallelism advocated by approaches such as gPipe.


<figure>
<center><img src="images/megatronlm/transformer.jpg" height="500" width="500"></center>
<center><b>Figure 3:</b> Model parallelism for a GPT-2 transformer layer.</center>
</figure>



**Performance**

To test the computational performance of our implementation, we consider GPT-2 models with four sets of parameters detailed in Table 1. To have consistent GEMM sizes in the self attention layer, the hidden size per attention head is kept constant at 96 while the number of heads and layers are varied to obtain configurations ranging from 1 billion to 8 billion parameters. The configuration with 1 billion parameters fits on a single GPU whereas the 8 billion parameter models requires 8-way model parallelism (8 GPUs). The original vocabulary size was 50,257, however, to have efficient GEMMs for the logit layer, it is critical for the vocabulary size to be a multiple of 128 as well as the number of model parallel GPUs. Since we study up to 8-way model parallelism, we pad the vocabulary such that it is divisible by 128x8=1024, resulting in a padded vocabulary size of 51,200.  We study both model and model+data parallel scalings. For the model parallel scaling, a fixed batch size of 8 is used across all configurations. Data parallel scaling is necessary for training many state of the art models which typically use a much larger global batch size. To this end, for the model+data parallel cases we fix the global batch size to 512 for all experiments which corresponds to 64-way data parallelism. 

<figure>
<center><img src="images/megatronlm/table_config.jpg" height="500" width="500"></center>
<center><b>Figure 3:</b> Model parallelism for a GPT-2 transformer layer.</center>
</figure>



<table>
  <tr>
   <td><strong>Config</strong>
   </td>
   <td><strong>Hidden size</strong>
   </td>
   <td><strong>Attention heads</strong>
   </td>
   <td><strong>Number of layers</strong>
   </td>
   <td><strong>Number of parameters (billions)</strong>
   </td>
   <td><strong>Model parallel GPUs</strong>
   </td>
   <td><strong>Model+data parallel GPUs</strong>
   </td>
  </tr>
  <tr>
   <td>1
   </td>
   <td>1536
   </td>
   <td>16
   </td>
   <td>40
   </td>
   <td>1.2
   </td>
   <td>1
   </td>
   <td>64
   </td>
  </tr>
  <tr>
   <td>2
   </td>
   <td>1920
   </td>
   <td>20
   </td>
   <td>54
   </td>
   <td>2.5
   </td>
   <td>2
   </td>
   <td>128
   </td>
  </tr>
  <tr>
   <td>3
   </td>
   <td>2304
   </td>
   <td>24
   </td>
   <td>64
   </td>
   <td>4.2
   </td>
   <td>4
   </td>
   <td>256
   </td>
  </tr>
  <tr>
   <td>4
   </td>
   <td>3072
   </td>
   <td>32
   </td>
   <td>72
   </td>
   <td>8.3
   </td>
   <td>8
   </td>
   <td>512
   </td>
  </tr>
</table>


**Table 1:** Parameters used for scaling studies.

All of our experiments are conducted on NVIDIA’s DGX SuperPod and we use up to 32 DGX-2H servers (a total of 512 Tesla V100 SXM3 32GB GPUs). This system is optimized for multi-node deep learning applications, with 300 GB/sec bandwidth between GPUs inside a server and 100 GB/sec of interconnect bandwidth between servers. Throughout this section, we will showcase weak scaling with respect to the model parameters for both model parallel and model+data parallel cases. Weak scaling is typically done with scaling the batch-size, however, this approach does not address training large models that do not fit on a single GPU and also convergence performance degrades for large batch sizes. In contrast, here we use weak scaling to train larger and larger models that were not possible otherwise. The baseline for all the scaling numbers is the first configuration in Table 1 running on a single GPU. 

Figure 4 shows scaling values for both model and model+data parallelism. We observe excellent scaling numbers in both settings. For example, the 8.3 billion parameters case with 8-way (8 GPU) model parallel achieves 77% of linear scaling. Model+data parallelism requires further communication of gradients after the back-propagation step and as a result the scaling numbers drop slightly. However, even for the largest configuration (8.3 billion parameters) running on 512 GPUs, we still achieve 74% scaling relative to the strong baseline single gpu configuration (1.2 billion parameters). 

**Figure 4:** Model (left) and model+data (right) parallel weak scaling with number of GPUs. 

Finally, we study the effect of attention heads on model parallel scaling. To this end, we consider the 8.3 billion parameter configuration with 8-way model parallelism and vary the number of heads from 16 to 32. The results are presented in Table 2. As the number of attention heads increases, some of the GEMMS inside the self-attention layer become smaller and also the number of elements in the self attention softmax increases. This results in a slight decrease in scaling. Future research should be wary of this hyperparameter to design large transformer models that balance model performance and model efficiency.


<table>
  <tr>
   <td>Config
   </td>
   <td>Attention heads
   </td>
   <td>Hidden size per head
   </td>
   <td>Scaling
   </td>
  </tr>
  <tr>
   <td rowspan="3" >8.3 billion parameters, 8 GPUs
   </td>
   <td>16
   </td>
   <td>192
   </td>
   <td>82%
   </td>
  </tr>
  <tr>
   <td>24
   </td>
   <td>128
   </td>
   <td>80%
   </td>
  </tr>
  <tr>
   <td>32
   </td>
   <td>96
   </td>
   <td>77%
   </td>
  </tr>
</table>


**Table 2:** Effect of number of attention heads on scaling.

**GPT-2 Training**

To train our GPT2 model we created a 37 GB _WebText_ dataset downloaded from _Reddit_ that is similar to the webtext dataset described in the original GPT-2 paper. To obtain the dataset, we leveraged the publicly available [OpenWebText](https://github.com/eukaryote31/openwebtext) codebase. Utilizing their pre-downloaded URLs we performed additional filtering to remove malformed, short or duplicate urls. To clean our downloaded content we used the [ftfy](https://ftfy.readthedocs.io/en/latest/) library and then removed non-english content using the [langdetect](https://pypi.org/project/langdetect/) library. We further cleaned our dataset by removing Wikipedia content and removing duplicates by using LSH filtering with a jaccard index of 0.9. The dataset ended up with 8.1 million URLs. For training, we randomly split the WebText dataset into a 95:5 ratio to obtain training and validation sets, respectively. We consider 4 model sizes: 345 million, 775 million, 2.5 billion, and 8.3 billion. 345 and 775 million  parameter models are similar to the ones used in GPT-2, and use (x) attention heads. 2.5 billion and 8.3 billion parameters models are detailed in Table 1, except that for the 8.3 billion parameter case, we use 24 attention heads. Learning rate in all cases is set to 1.5x10-4 with cosine annealing and 3000 iteration warmup. A gaussian distribution with zero mean and standard deviation of 0.02 is used for weight initialization and we scale the weights of residual layers by $$\frac{1}{\sqrt{2l}}$$ where $$l$$ is the number of layers. In all cases, a dropout of 0.1 is used.


![alt_text](images/2-blog6.png "image_tooltip")


**Figure 5**: Validation perplexity over a subset of training. The 8.3B model is stopped early after overfitting on our 37GB dataset.

Figure 5 shows validation perplexity as a function of number of epochs for different model sizes. At validation time we find empirically that larger models lead to lower validation perplexities (Figure 5). However, we find that our largest 8.3B parameter language model begins to overfit after ~6 epochs of training, where an epoch is defined as 15,200 iterations: the number of iterations required to train over the entire 37GB corpus. We suspect that this can be mitigated by using larger scale datasets similar to those used in recent papers such as [XLNet](https://arxiv.org/abs/1906.08237) and [RoBERTa](https://arxiv.org/abs/1907.11692).

**GPT-2 Evaluation** 

To analyze the performance of training large language models, we compute perplexity on the [wikitext-103 dataset](https://s3.amazonaws.com/research.metamind.io/wikitext/wikitext-103-v1.zip) and cloze-style prediction accuracy on the [Lambada dataset](https://github.com/cybertronai/bflm/blob/master/lambada_test.jsonl). As expected, the wikitext perplexity decreases and lambada accuracy increases with the growth of the model size (Table 3). At a respective wikitext perplexity of 17.7 and 17.4 both our 2.5B and 8.3B models surpass the previous state of the art perplexity of 18.3 set by [transformer-xl](https://arxiv.org/abs/1901.02860) model. We describe our evaluation methodologies below; however, more details are available in [our github repository](https://github.com/nvidia/megatron-lm).

<!--
<table>
  <tr>
   <td><strong>Model Size</strong>
   </td>
   <td><strong>Number of layers</strong>
   </td>
   <td><strong>Hidden Size</strong>
   </td>
   <td><strong>Attention heads</strong>
   </td>
   <td><strong>Wikitext (Perplexity ↓)</strong>
   </td>
   <td><strong>Lambada (Accuracy ↑)</strong>
   </td>
  </tr>
  <tr>
   <td>345 M
   </td>
   <td>24
   </td>
   <td>1024
   </td>
   <td>16
   </td>
   <td>24.21
   </td>
   <td>55.04
   </td>
  </tr>
  <tr>
   <td>775 M
   </td>
   <td>36
   </td>
   <td>1280
   </td>
   <td>16
   </td>
   <td>20.44
   </td>
   <td>58.86
   </td>
  </tr>
  <tr>
   <td>2.5 B
   </td>
   <td>54
   </td>
   <td>1920
   </td>
   <td>20
   </td>
   <td>17.77
   </td>
   <td>63.26
   </td>
  </tr>
  <tr>
   <td>8.3 B
   </td>
   <td>72
   </td>
   <td>3072
   </td>
   <td>24
   </td>
   <td>17.41
   </td>
   <td>63.11
   </td>
  </tr>
</table>
-->

| Model Size | Number of Layers | Hidden Size | Attention Heads | Wikitext (Perplexity ↓) | Lambada (Accuracy ↑) |
|:----------:|:----------------:|:-----------:|:---------------:|:-----------------------:|:--------------------:|
|    345 M   |        24        |     1024    |        16       |          24.21          |         55.04        |
|    775 M   |        36        |     1280    |        16       |          20.44          |         58.86        |
|    2.5 B   |        54        |     1920    |        20       |          17.77          |         63.26        |
|    8.3 B   |        72        |     3072    |        24       |          17.41          |         63.11        |


**Table 3**: Zero-shot evaluation results for wikitext perplexity (lower is better) and Lambada cloze accuracy (higher is better). Wikitext perplexity surpasses previous state of the art results (18.3) set by transformer-xl.

**Conclusion**

In this work, we built the world's largest transformer based language model on top of existing deep learning hardware, software, and models. In doing so, we successfully surpassed the limitations posed by traditional single GPU training by implementing a simple and efficient model parallel approach with only a few targeted modifications to the existing PyTorch transformer implementations. We efficiently train an 8.3 billion parameter language model (24x and 5.6x larger than the size of BERT and GPT-2, respectively) on 512 NVIDIA V100 GPUs with 8-way model parallelism and achieve up to 15.1 PetaFLOPS sustained over the entire application. With weak scaling, we found that increasingly large transformer models can be trained in a similar amount of time compared to their smaller counterparts and can demonstrably improve performance. However, as we showed in our work, NLP still requires suitable datasets, problems, and techniques to properly train these large language models without overfitting. We open source our work as a catalyst so that the community may build upon our efforts and address these problems.

May Your Learning Be Deep and (Un)supervised ...

**Appendix**

**Wikitext Perplexity:**

Perplexity is the exponentiation of the average cross entropy of a corpus. This makes it a natural evaluation metric for language models which represent a probability distribution over entire sentences or texts. We make note of principled modifications to the perplexity computation.



$$ PPL= \exp({-\frac{1}{T_o}\sum_{t}^{T} \text{log} P(t\vert 0:t-1))}$$


The wikitext-103 test corpus already comes pre-tokenized with word-level tokens that prior works have used to compute perplexity. To evaluate our models' perplexities on a level playing field with prior works, we normalize by the original number of word-level tokens, $$T_o$$, rather than the number of subword tokens, $$T$$, actually in the tokenized data fed as input to our model. Additionally, to alleviate distributional mismatch caused by wikitext processing artifacts, we first preprocess the wikitext-103 test dataset with invertible de-tokenizers to remove various artifacts related to punctuation and whitespace. The value of $$T_o$$ is calculated before this preprocessing. For wikitext-103's test set we arrive at $$T_o=245566$$ and $$T=270329$$. Lastly, to accommodate the transformer’s fixed window input size and inability to attend to all tokens [0, t-1], we utilize a sliding window approach with a stride of 32 tokens to compute the perplexities. 

**Cloze Accuracy:**

Cloze-style datasets like LAMBADA are designed to measure a model's ability to operate in and reason about long-term contexts. Handling long term contexts is crucial for state of the art language models to solve problems like long-form generation and document-based question answering.  Cloze-style reading comprehension uses a context of word tokens $$x=x_{1:t}$$ with one token $$x_j$$ masked; the models objective is to correctly predict the value $$y$$ of the missing $$j^{\text{th}}$$ token based on its understanding of the surrounding context. LAMBADA uses cloze-style reading comprehension to test generative left-to-right language models by constructing examples of 4-5 sentences where the last word in the context $$x_t$$ is masked. Our models utilize subword units, so for our version of LAMBADA evaluation we utilize the raw, unprocessed LAMBADA dataset, and mask $$x_t$$ corresponding to the last subword token of the samples in the dataset as opposed to the last word of the samples. We report the accuracy of our model based on the number of times that $$y = \text{argmax} P(x_t\vert x_{0:t−1})$$ divided by the total number of examples.



 
