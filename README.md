# Transformer Dissection: An Unified Understanding for Transformer's Attention via the Lens of Kernel

> Empirical Study of Transformer’s Attention Mechanism via the Lens of Kernel.

Correspondence to: 
  - Yao-Hung Hubert Tsai (yaohungt@cs.cmu.edu)

## Paper
[**Transformer Dissection: An Unified Understanding for Transformer's Attention via the Lens of Kernel**](https://arxiv.org/pdf/1908.11775.pdf)<br>
[Yao-Hung Hubert Tsai](https://yaohungt.github.io), [Shaojie Bai](https://jerrybai1995.github.io), [Makoto Yamada](https://riken-yamada.github.io), [Louis-Philippe Morency](https://www.cs.cmu.edu/~morency/), and [Ruslan Salakhutdinov](https://www.cs.cmu.edu/~rsalakhu/)<br>
Empirical Methods in Natural Language Processing (EMNLP), 2019. 

Please cite our paper if you find our work useful for your research:

```tex
@inproceedings{tsai2019TransformerDissection,
  title={Transformer Dissection: An Unified Understanding for Transformer's Attention via the Lens of Kernel},
  author={Tsai, Yao-Hung Hubert and Bai, Shaojie and Yamada, Makoto and Morency, Louis-Philippe and Salakhutdinov, Ruslan},
  booktitle={EMNLP},
  year={2019},
}
```

## Overview

### Inspiration
*Transformer's attention* and *kernel learning* both concurrently and order-agnostically process all inputs by calculating the similarity between inputs. 

### Achievement
We present a new formulation of attention via the lens of kernel. This formulation highlights naturally the main components of Transformer's attention, enabling better understanding of this mechanism. Recent variants of Transformers can be expressed through these individual components. The approach also paves the way to a larger space of composing Transformer's attention.

### Kernel-Based Formulation of Transformer's Attention

<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathrm{Attention}\Big(x_q\,\,;\,\,M(x_q,S_{\mathbf{x}_k})\Big)=\sum_{{x_k}\in\,M(x_q,S_{\mathbf{x}_k})}\frac{k(x_q,x_k)}{\sum_{{x_k}'\in\,M(x_q,S_{\mathbf{x}_k})}k(x_q,{x_k}')}v(x_k)=\mathbb{E}_{p(x_k|x_q)}\Big[v(x_k)\Big]" />
</p>

* <img src="https://latex.codecogs.com/svg.latex?x_q" /> : __query__
* <img src="https://latex.codecogs.com/svg.latex?S_{\mathbf{x}_k}" /> : the set for __keys__
* <img src="https://latex.codecogs.com/svg.latex?M(x_q,S_{\mathbf{x}_k})" /> : __set filtering function__, which returns a set with its elements that are visible to <img src="https://latex.codecogs.com/svg.latex?x_q" />
* <img src="https://latex.codecogs.com/svg.latex?k(\cdot,\cdot)" /> : non-negative __kernel__ function
* <img src="https://latex.codecogs.com/svg.latex?v(\cdot)" /> : __value__ function
* <img src="https://latex.codecogs.com/svg.latex?p(x_k|x_q)=\frac{k(x_q,x_k)}{\sum_{{x_k}'\in\,M(x_q,S_{\mathbf{x}_k})}k(x_q,{x_k}')}" /> : linear smoother with kernel smoothing

### Dissecting Individual Components
##### Kernel Feature Space <img src="https://latex.codecogs.com/svg.latex?\mathcal{X}" />

1. Sequence Transformer ([Vaswani _et al._, 2017](https://arxiv.org/abs/1706.03762), [Dai _et al._, 2019](https://arxiv.org/abs/1901.02860)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathcal{X}:=(\mathcal{F}\times\mathcal{T})" />
</p>
with <img src="https://latex.codecogs.com/svg.latex?\mathcal{F}" /> being non-positional feature space and <img src="https://latex.codecogs.com/svg.latex?\mathcal{T}" /> being the positional embedding space.

2. Image Transformer ([Parmar _et al._, 2018](https://arxiv.org/abs/1802.05751)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathcal{X}:=(\mathcal{F}\times\mathcal{H}\times\mathcal{W})" />
</p>
with <img src="https://latex.codecogs.com/svg.latex?\mathcal{F}" /> being non-positional feature space, <img src="https://latex.codecogs.com/svg.latex?\mathcal{H}" /> being the positional space of the height in an image, and <img src="https://latex.codecogs.com/svg.latex?\mathcal{W}" /> being the positional space of the width in an image.

3. Set Transformer ([Lee _et al._, 2018](https://arxiv.org/abs/1810.00825)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathcal{X}:=(\mathcal{F})" />
</p>
with no any positional information present.

4. Multimodal Transformer ([Tsai _et al._, 2019](https://arxiv.org/abs/1906.00295)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?\mathcal{X}:=(\mathcal{F}^\ell\times\mathcal{F}^v\times\mathcal{F}^a\times\mathcal{T})" />
</p>
with <img src="https://latex.codecogs.com/svg.latex?\mathcal{F}^\ell" /> representing the language feature space, <img src="https://latex.codecogs.com/svg.latex?\mathcal{F}^v" /> representing the vision feature space, <img src="https://latex.codecogs.com/svg.latex?\mathcal{F}^a" /> representing the audio feature space, and <img src="https://latex.codecogs.com/svg.latex?\mathcal{T}" /> representing the temporal indicator space. 

##### Kernel Construction and the Role of Positional Embedding <img src="https://latex.codecogs.com/svg.latex?k(\cdot,\cdot)" />
Most of the work utilizes __assymetric exponential kernel__ with learned mappings <img src="https://latex.codecogs.com/svg.latex?W_q, W_k" /> and scaled factor <img src="https://latex.codecogs.com/svg.latex?\sqrt{d_k}" />:
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?k_{\mathrm{exp}}(f_q,f_k)=\mathrm{exp}\left(\frac{\langle\,f_qW_q,f_kW_k\rangle}{\sqrt{d_k}}\right)." />
</p>
Note that in the paper, we also try linear, polynomial, and rbf kernel. We observe kernel with infinite feature dimension (i.e., exponential kernel and rbf kernel) leads to best performance. 

1. Absolute Positional Embedding ([Vaswani _et al._, 2017](https://arxiv.org/abs/1706.03762)): 
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?k(x_q,x_k):=k_{\mathrm{exp}}(f_q+t_q,f_k+t_k)" />
</p>
assuming direct sum of the feature space (<img src="https://latex.codecogs.com/svg.latex?\mathcal{X}=\mathcal{F}\oplus\mathcal{T}" />).

2. Relative Positional Embedding ([Dai _et al._, 2019](https://arxiv.org/abs/1901.02860)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?k\Big(x_q,x_k\Big):=k_{\mathrm{exp}}\Big(f_q,f_k\Big)\cdot\,k_{f_q}\Big(t_q,t_k\Big)" />
</p>
with <img src="https://latex.codecogs.com/svg.latex?k_{f_q}\Big(t_q,t_k\Big)" /> being an asymmetric kernel with coefficients inferred by <img src="https://latex.codecogs.com/svg.latex?f_q" />.

3. Relative Positional Embedding ([Shaw _et al._, 2018](https://arxiv.org/abs/1803.02155), [Huang _et al._, 2018](https://arxiv.org/abs/1809.04281)):
<p align="center">
<img src="https://latex.codecogs.com/svg.latex?k\Big(x_q,x_k\Big):=L_{t_q-t_k,f_q}\cdot\,k_{\mathrm{exp}}\Big(f_q,f_k\Big)" />
</p>
with <img src="https://latex.codecogs.com/svg.latex?L_{t_q-t_k,f_q}=\mathrm{exp}(f_qW_qa_{t_q-t_k})" /> with <img src="https://latex.codecogs.com/svg.latex?a_\cdot" /> being a learnable matrix.

For the above variants of positional embedding integration, we find product kernel (relative positional embedding by [Dai _et al._, 2019](https://arxiv.org/abs/1901.02860)) works the best.



##### Value Function <img src="https://latex.codecogs.com/svg.latex?v(\cdot)" />

##### Set Filtering Function <img src="https://latex.codecogs.com/svg.latex?M(x_q,S_{\mathbf{x}_k})" />



