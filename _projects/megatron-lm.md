---
title: "MegatronLM: Training Billion+ Parameter Language Models Using GPU Model Parallelism"
collection: projects
author: Jared Casper, Patrick Legresley, Mostofa Patwary, Raul Puri, Mohammad Shoeybi, Bryan Catanzaro
permalink: /MegatronLM
excerpt: 'We train an 8.3 billion parameter transformer language model with 8-way model parallelism and 64-way data parallelism on 512 GPUs, making it the **largest transformer based language model ever trained at 24x the size of BERT and 5.6x the size of GPT-2**'
date: 2019-08-13
---
<div style="text-align: justify"> 
<p>
Larger language models are dramatically more useful for NLP tasks such as article completion, question answering, and dialog systems. Training the largest neural language model has recently been the best way to advance the state of the art in NLP applications. Two recent papers, <a href="https://arxiv.org/abs/1810.04805">BERT</a> and <a href="https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf">GPT-2</a>, demonstrate the benefits of large scale language modeling. Both papers leverage advances in compute and available text corpora to significantly surpass state of the art performance in natural language understanding, modeling, and generation. Training these models requires hundreds of exaflops of compute and <a href="https://arxiv.org/abs/1604.06174">clever memory management</a> to trade recomputation for a reduced memory footprint. However, for very large models beyond a billion parameters, the memory on a single GPU is not enough to fit the model along with the parameters needed for training, requiring model parallelism to split the parameters across multiple GPUs. Several approaches to model parallelism exist, but they are difficult to use, either because they rely on custom compilers, or because they scale poorly or require changes to the optimizer.
</p>
</div>

<div style="text-align: justify">
<p> 
In this work, we implement a simple and efficient model parallel approach by making only a few targeted modifications to existing <a href="https://openreview.net/pdf?id=BJJsrmfCZ">PyTorch</a> transformer implementations. <a href="https://github.com/nvidia/megatron-lm">Our code</a> is written in native Python, leverages mixed precision training, and utilizes the <a href="https://developer.nvidia.com/nccl">NCCL library</a> for communication between GPUs. We showcase this approach by training an 8.3 billion parameter transformer language model with 8-way model parallelism and 64-way data parallelism on 512 GPUs, making it the <b>largest transformer based language model ever trained at 24x the size of BERT and 5.6x the size of GPT-2</b>. We have published the code that implements this approach at <a href="https://github.com/NVIDIA/Megatron-LM">our GitHub repository</a>.
</p>
</div>

<div style="text-align: justify">
<p>
Our experiments are conducted on NVIDIA’s <a href="https://devblogs.nvidia.com/dgx-superpod-world-record-supercomputing-enterprise">DGX SuperPOD</a>. Without model parallelism, we can fit a baseline model of 1.2B parameters on a single V100 32GB GPU, and sustain 39 TeraFLOPS during the overall training process, which is 30% of the theoretical peak FLOPS for a single GPU in a DGX2-H server. Scaling the model to 8.3 billion parameters on 512 GPUs with 8-way model parallelism, we achieved up to <b>15.1 PetaFLOPS sustained performance</b> over the entire application and reached <b>76% scaling efficiency</b> compared to the single GPU case. Figure 1 shows more detailed scaling results.
</p>
</div>

<p>
<center><img src="images/megatronlm/flops_scaling.jpg" width="650"/></center>
<center><b>Figure 1: </b> Model (blue) and model+data (green) parallel FLOPS as a function of number of GPUs. Model parallel (blue): up to 8-way model parallel weak scaling with approximately 1 billion parameters per GPU (e.g. 2 billion for 2 GPUs and 4 billion for 4 GPUs). Model+data parallel (green): similar configuration as model parallel combined with 64-way data parallel.</center>
</p>


**Multi-GPU Parallelism**

<div style="text-align: justify">
<p>
The typical paradigm for training models has made use of weak scaling approaches and distributed data parallelism to scale training batch size with number of GPUs. This approach allows the model to train on larger datasets, but has the constraint that all parameters must fit on a single GPU. Model parallel training can overcome this limitation by partitioning the model across multiple GPUs. Several general purpose model parallel frameworks such as <a href="https://arxiv.org/abs/1811.06965">GPipe</a> and <a href="https://arxiv.org/abs/1811.02084">Mesh-TensorFlow</a> have been proposed recently. gPipe divides groups of layers across different processors while Mesh-TensorFlow employs intra-layer model parallelism. Our approach is conceptually similar to Mesh-TensorFlow, we focus on intra-layer parallelism and fuse GEMMs to reduce synchronization. However, we only make a few targeted modifications to existing PyTorch transformer implementations to employ model parallelism for training large transformers. Our approach is simple, does not require any new compiler or code re-wiring for model parallelism, and can be fully implemented with insertion of few simple primitives (<b><i>f</i></b> and <b><i>g</i></b> operators in Figure 2) as described in the remainder of this section.
</p>
</div>

<div style="text-align: justify">
<p>
We take advantage of the structure of transformer networks to create a simple model parallel implementation by adding a few synchronization primitives. A transformer layer consists of a self attention block followed by a two-layer multi-layer perceptron (MLP). We introduce model parallelism in both of these blocks separately. We start by detailing the MLP block as shown in Figure 2a. This block consists of two GEMMs with a <a href="https://arxiv.org/pdf/1606.08415.pdf">GeLU</a> nonlinearity in between followed by a dropout layer. We partition the first GEMM in a column parallel fashion. This allows the GeLU nonlinearity to be independently applied to the output of each partitioned GEMM. The second GEMM in the block is parallelized along its rows and takes the output of the GeLU layer directly without requiring any communication. The output of the second GEMM is then reduced across the GPUs before passing the output to the dropout layer. This approach splits both GEMMs in the MLP block across GPUs and requires only a single all-reduce operation in the forward pass (<b><i>g</i></b> operator) and a single all-reduce in the backward pass (<b><i>f</i></b> operator). 
</p>
</div>

<p>
<center><img src="images/megatronlm/MLP_SelfAttention.jpg" width="550"></center>
<center><b>Figure 2: </b>(a): MLP and (b): self attention blocks of transformer. <b><i>f</i></b> and <b><i>g</i></b> are conjugate, <b><i>f</i></b> is an <b>identity</b> operator in the forward pass and <b>all-reduce</b> in the backward pass while <b><i>g</i></b> is an <b>all-reduce</b> in forward and <b><i>identity</i></b> in backward.</center>
</p>

<div style="text-align: justify">
<p>
As shown in Figure 2b, for the self attention block, we exploit inherent parallelism in the multihead attention operation, partitioning the GEMMs associated with key (K), query (Q), and value (V) in a column parallel fashion such that the matrix multiply corresponding to one attention head is done locally on one GPU. This allows us to split per attention head parameters and workload across the GPUs, and doesn’t require any immediate communication to complete the self-attention. The subsequent GEMM from the output linear layer (after self attention) is parallelized along its rows and takes the output of the parallel attention layer directly, without requiring communication between the GPUs. This approach for both the MLP and self attention layer fuses groups of two GEMMS, eliminates a synchronization point in between, and results in better scaling performance. This enables us to perform all GEMMs in a simple transformer layer using only two all reduces in the forward path and two in the backward path (see Figure 3). Lastly, the output logit layer is also partitioned along the vocabulary dimension, and to avoid a massive all-gather across the vocabulary dimension, we fuse this layer with the cross entropy loss. 
</p>
</div>

<div style="text-align: justify">
<p>
This approach is simple to implement, because it requires only a few extra all-reduce operations be added to the forward and backward pass. It doesn’t require a compiler, and is orthogonal to the kind of pipeline model parallelism advocated by approaches such as gPipe.
</p>
</div>

<p>
<center><img src="images/megatronlm/transformer.jpg" width="520"></center>
<center><b>Figure 3: </b>Model parallelism for a GPT-2 transformer layer. </center>
</p>


**Performance**

<div style="text-align: justify">
<p>
To test the computational performance of our implementation, we consider GPT-2 models with four sets of parameters detailed in Table 1. To have consistent GEMM sizes in the self attention layer, the hidden size per attention head is kept constant at 96 while the number of heads and layers are varied to obtain configurations ranging from 1 billion to 8 billion parameters. The configuration with 1 billion parameters fits on a single GPU whereas the 8 billion parameter models requires 8-way model parallelism (8 GPUs). The original vocabulary size was 50,257, however, to have efficient GEMMs for the logit layer, it is critical for the vocabulary size to be a multiple of 128 as well as the number of model parallel GPUs. Since we study up to 8-way model parallelism, we pad the vocabulary such that it is divisible by 128x8=1024, resulting in a padded vocabulary size of 51,200.  We study both model and model+data parallel scalings. For the model parallel scaling, a fixed batch size of 8 is used across all configurations. Data parallel scaling is necessary for training many state of the art models which typically use a much larger global batch size. To this end, for the model+data parallel cases we fix the global batch size to 512 for all experiments which corresponds to 64-way data parallelism. 
</p>
</div>

<p>
<center><img src="images/megatronlm/table_config.jpg" width="700"></center>
<center><b>Table 1:</b> Parameters used for scaling studies.</center>
</p>

<div style="text-align: justify">
<p>
All of our experiments are conducted on NVIDIA’s DGX SuperPOD and we use up to 32 DGX-2H servers (a total of 512 Tesla V100 SXM3 32GB GPUs). This system is optimized for multi-node deep learning applications, with 300 GB/sec bandwidth between GPUs inside a server and 100 GB/sec of interconnect bandwidth between servers. Throughout this section, we will showcase weak scaling with respect to the model parameters for both model parallel and model+data parallel cases. Weak scaling is typically done with scaling the batch-size, however, this approach does not address training large models that do not fit on a single GPU and also convergence performance degrades for large batch sizes. In contrast, here we use weak scaling to train larger and larger models that were not possible otherwise. The baseline for all the scaling numbers is the first configuration in Table 1 running on a single GPU. 
</p>
</div>

<div style="text-align: justify">
<p>
Figure 4 shows scaling values for both model and model+data parallelism. We observe excellent scaling numbers in both settings. For example, the 8.3 billion parameters case with 8-way (8 GPU) model parallel achieves 77% of linear scaling. Model+data parallelism requires further communication of gradients after the back-propagation step and as a result the scaling numbers drop slightly. However, even for the largest configuration (8.3 billion parameters) running on 512 GPUs, we still achieve 74% scaling relative to the strong baseline single gpu configuration (1.2 billion parameters). 
</p>
</div>

<p>
<center><img src="images/megatronlm/weak_scaling.jpg" width="800"></center>
<center><b>Figure 4:</b> Model (left) and model+data (right) parallel weak scaling with number of GPUs.</center>
</p>

<div style="text-align: justify">
<p>
Finally, we study the effect of attention heads on model parallel scaling. To this end, we consider the 8.3 billion parameter configuration with 8-way model parallelism and vary the number of heads from 16 to 32. The results are presented in Table 2. As the number of attention heads increases, some of the GEMMS inside the self-attention layer become smaller and also the number of elements in the self attention softmax increases. This results in a slight decrease in scaling. Future research should be wary of this hyperparameter to design large transformer models that balance model performance and model efficiency.
</p>
</div>

<p>
<center><img src="images/megatronlm/table_attention_heads.jpg" width="450"></center>
<center><b>Table 2:</b> Effect of number of attention heads on scaling.</center>
</p>


**GPT-2 Training**

<div style="text-align: justify">
<p>
To train our GPT-2 model we created an aggregate 174GB dataset (~4.5x larger than WebText) consisting of <a href="https://github.com/NVIDIA/Megatron-LM#collecting-wikipedia-training-data">Wikipedia</a>, <a href="https://github.com/eukaryote31/openwebtext">OpenWebText</a>, <a href="https://github.com/rowanz/grover">RealNews</a>, and <a href="https://arxiv.org/abs/1806.02847">CC-Stories</a>. We performed additional filtering to remove malformed, short or duplicate documents less than 128 tokens. To clean the datasets we used the <a href="https://ftfy.readthedocs.io/en/latest/">ftfy</a> library and then removed non-english content using the <a href="https://pypi.org/project/langdetect/">langdetect</a> library. We further cleaned our dataset by removing WikiText test-set content and remove duplicates by using <a href="https://github.com/mattilyra/LSH">LSH filtering</a> with a jaccard index of 0.7. The dataset ended up with approximately 40 million documents. For training, we randomly split the WebText dataset into a 29:1 ratio to obtain training and validation sets, respectively.
</p>
</div>

<div style="text-align: justify">
<p>
We consider training 3 model sizes: 355 million, 2.5 billion, and 8.3 billion. The 355 million  parameter model is similar to the one used in GPT-2, and uses 16 attention heads. The 2.5 billion and 8.3 billion parameters models are detailed in Table 1, except that for the 8.3 billion parameter case, we use 24 attention heads. Learning rates in all cases are set to $1.5\times 10^{-4}$ with single cycle cosine annealing and 3000 iteration warmup. We clamp our learning rate to a minimum value of $1\times 10^{-5}$. A gaussian distribution with zero mean and standard deviation of 0.02 is used for weight initialization and we scale the weights of layers preceding residual connections by $\frac{1}{\sqrt{2N}}$ where $N$ is the number of layers. In all cases, a dropout of 0.1 is used.
</p>
</div>


<p>
<center><img src="images/megatronlm/validation_perp.png" width="550"></center>
<center><b>Figure 5:</b> Validation perplexity over a subset of training. The 8.3B model surpasses all other models and continues to drop at the end of training.</center>
</p>

<div style="text-align: justify">
<p>
Figure 5 shows validation perplexity as a function of number of training iterations for different model sizes. At validation time we find empirically that larger models lead to lower validation perplexities (Figure 5). Our 8.3B model achievs a validation perplexity of 9.27. An epoch is defined as 68,507 iterations to train over the entire 174GB corpus.
</p>
</div>


**GPT-2 Evaluation** 

<div style="text-align: justify">
<p>
To analyze the performance of training large language models, we compute perplexity on the <a href="https://s3.amazonaws.com/research.metamind.io/wikitext/wikitext-103-v1.zip">WikiText-103 dataset</a> and cloze-style prediction accuracy on the <a href="https://github.com/cybertronai/bflm/blob/master/lambada_test.jsonl">LAMBADA dataset</a>. Earlier we made sure to remove WikiText test set content to avoid leakage. Our resulting dataset has a WikiText 8-gram overlap of 10% which is similar to the 9% 8-gram overlap between the WikiText-103 train and test sets. As expected, the WikiText perplexity decreases and LAMBADA accuracy increases with the growth of the model size (Table 3). At a respective WikiText perplexity of 12.68 and 10.81 both our 2.5B and 8.3B models surpass the previous state of the art perplexity of 16.43 set by <a href="https://arxiv.org/abs/1904.08378">Krause et. al</a>. Our models achieve 61.52% and 66.51% accuracy on LAMBADA even without any stopword filtration, surpassing <a href="https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf">Radford et. al</a>. We describe our evaluation methodologies below; however, more details are available in <a href="https://github.com/nvidia/megatron-lm">our github repository</a>.
</p>
</div>

<p>
<center><img src="images/megatronlm/table_eval_results.png" width="650"></center>
<center><b>Table 3:</b> Zero-shot evaluation results for WikiText perplexity (lower is better) and LAMBADA cloze accuracy (higher is better). * <a href="https://arxiv.org/abs/1904.08378">Dynamic Evaluation of Transformer Language Models, Krause et. al., 2019</a>. ** <a href="https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf">Language Models are Unsupervised Multitask Learners, Radford et. al., 2019</a>. </center>
</p>


**Conclusion**

<div style="text-align: justify">
<p>
In this work, we built the world's largest transformer based language model on top of existing deep learning hardware, software, and models. In doing so, we successfully surpassed the limitations posed by traditional single GPU training by implementing a simple and efficient model parallel approach with only a few targeted modifications to the existing PyTorch transformer implementations. We efficiently train an 8.3 billion parameter language model (24x and 5.6x larger than the size of BERT and GPT-2, respectively) on 512 NVIDIA V100 GPUs with 8-way model parallelism and achieve up to 15.1 PetaFLOPS sustained over the entire application. With weak scaling, we found that increasingly large transformer models can be trained in a similar amount of time compared to their smaller counterparts and can demonstrably improve performance. Our 8.3B model exemplifies this by setting new state of the art results for both WikiText-103 (10.81 perplexity) and LAMBADA (66.51% accuracy).  We open source our work so that the community may reproduce our efforts and extend them.
</p>
</div>

**_Update 9/5/2019_: Larger dataset and stricter lambada formulation**


**Appendix**


**WikiText Perplexity:**

<div style="text-align: justify">
<p>
Perplexity is the exponentiation of the average cross entropy of a corpus. This makes it a natural evaluation metric for language models which represent a probability distribution over entire sentences or texts. We make note of the detailed methods we use to compute perplexity for the sake of reproducibility.
$$ PPL= \exp\left(-\frac{1}{T_o}\sum_{t}^{T} \text{log}\;P(t\vert 0:t-1)\right)$$
The WikiText-103 test corpus already comes pre-tokenized with word-level tokens that prior work has used to compute perplexity. To evaluate model perplexity fairly and consistently, we normalize by the original number of word-level tokens, $T_o$, rather than the number of subword tokens, $T$, that were present in the tokenized data. Additionally, to alleviate distributional mismatch caused by WikiText processing artifacts, we first preprocess the WikiText-103 test dataset with invertible de-tokenizers to remove various artifacts related to punctuation and whitespace. The value of $T_o$ is calculated before this preprocessing. For WikiText-103's test set we arrive at $T_o=245566$ and $T=270329$. Lastly, to accommodate the transformer’s fixed window input size and inability to attend to all tokens [0, t-1], we utilize a sliding window approach with a size of 1024 tokens, and a stride of 32 tokens to compute perplexity. 
</p>
</div>


**Cloze Accuracy:**

<div style="text-align: justify">
<p>
Cloze-style datasets like LAMBADA are designed to measure a model's ability to operate in and reason about long-term contexts. Handling long term contexts is crucial for state of the art language models to solve problems like long-form generation and document-based question answering.  Cloze-style reading comprehension uses a context of word tokens $x=x_{1:t}$ with one token $x_j$ masked; the model's objective is to correctly predict the value $y$ of the missing $j^{\text{th}}$ token based on its understanding of the surrounding context. LAMBADA uses cloze-style reading comprehension to test generative left-to-right language models by constructing examples of 4-5 sentences where the last word in the context $x_t$ is masked. Our models utilize subword units, so for our version of LAMBADA evaluation we utilize the raw, unprocessed LAMBADA dataset, and require that our model predict the multiple $x_t$ subwords that make up the last word token. We use teacher forcing in our predictions, and consider an answer correct only when all output subword predictions are correct. This formulation is equivalent to the original task of word prediction.
</p>
</div>



 




<!--
<table>
  <col align="center">
  <col align="center">
  <col align="center">
  <col align="center">
  <col align="center">
  <col align="center">
  <col align="center">
  <tr bgcolor="#E7B4AE">
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
  <tr bgcolor="#FFF2D8">
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
  <tr bgcolor="#CAD9FA">
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
  <tr bgcolor="#FFF2D8">
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
  <tr bgcolor="#CAD9FA">
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
-->



<!--
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
-->



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

<!--
| Model Size | Number of Layers | Hidden Size | Attention Heads | Wikitext (Perplexity ↓) | Lambada (Accuracy ↑) |
|:----------:|:----------------:|:-----------:|:---------------:|:-----------------------:|:--------------------:|
|    345 M   |        24        |     1024    |        16       |          24.21          |         55.04        |
|    775 M   |        36        |     1280    |        16       |          20.44          |         58.86        |
|    2.5 B   |        54        |     1920    |        20       |          17.77          |         63.26        |
|    8.3 B   |        72        |     3072    |        24       |          17.41          |         63.11        |
-->

