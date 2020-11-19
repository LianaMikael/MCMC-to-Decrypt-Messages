# MCMC-to-Decrypt-Messages

The idea is to use Monte Carlo Markov Chain (MCMC) sampling methods to decrypt messages. In particular, we use Metropolis-Hastings algorithm to decrypt messages encrypted by asimple substitution cipher.

Consider a cipher (an injective mapping) that maps every symbol to a different symbol from the same set (The set of 53 symbols is given in **symbols.txt**). 

First, we model English letters and punctuation as a first-order Markov Chain and learn the transition probabilities between every pair of symbols. Then we use Metropolis-Hastings to find an optimal permutaiton of symbols. 

## Markov Chain 
A Markov Chain is a way to model sequential data in which current observations depend on previous observations. A fist-order Markov Chain assumes that the current observation is independent of all the previous observations apart form the most recent one. That is, the joint distribution for a sequence of N observations is given by
<a href="https://www.codecogs.com/eqnedit.php?latex=p(s_1,&space;\ldots,&space;s_N)&space;=&space;p(s_1)&space;\prod_{n=2}^{N}&space;p(s_n|s_{n-1})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?p(s_1,&space;\ldots,&space;s_N)&space;=&space;p(s_1)&space;\prod_{n=2}^{N}&space;p(s_n|s_{n-1})" title="p(s_1, \ldots, s_N) = p(s_1) \prod_{n=2}^{N} p(s_n|s_{n-1})" /></a>

The transition probabilties <a href="https://www.codecogs.com/eqnedit.php?latex=p(s_i&space;=&space;\alpha|&space;s_{i-1}&space;=&space;\beta)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?p(s_i&space;=&space;\alpha|&space;s_{i-1}&space;=&space;\beta)" title="p(s_i = \alpha| s_{i-1} = \beta)" /></a>
represent the probability of a symbol being followed by another symbol. They are learnt using a large English text (**war-and-peace.txt**) and illustrated as a Hinton diagram. 

## Metropolis-Hastings
The purpose of MCMC sampling techniques is to approximate distributions by drawings samples in cases when exact inference is intractable. Metropolis-Hastings algorithm is defined as follows:

**Each step:** Starting from the current state **x**
  1. Propose a new state **x'** using a proposal distribution (a similar simpler distribution) 
    <a href="https://www.codecogs.com/eqnedit.php?latex=S(x'|x)&space;=&space;S(x'&space;\rightarrow&space;x)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?S(x'|x)&space;=&space;S(x'&space;\rightarrow&space;x)" title="S(x'|x) = S(x' \rightarrow x)" /></a>
    
  2. Accept the new state with probability:
    <a href="https://www.codecogs.com/eqnedit.php?latex=min(1,&space;p(x')S(x'&space;\rightarrow&space;x)/p(x)S(x&space;\rightarrow&space;x'))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?min(1,&space;p(x')S(x'&space;\rightarrow&space;x)/p(x)S(x&space;\rightarrow&space;x'))" title="min(1, p(x')S(x' \rightarrow x)/p(x)S(x \rightarrow x'))" /></a>
 
  3. Otherwise, keep the old state. 

In our case, the state variable is given by the symbol permutaiton and we assume a uniform prior over the permutations. 

The potential new state is calculated by randomly choosing two symbols out of 53 possible and swapping their corresponding encrypted symbols. Hence, the proposal probability is given by:

<a href="https://www.codecogs.com/eqnedit.php?latex=\bg_white&space;S(x'&space;\rightarrow&space;x)&space;=&space;S(x&space;\rightarrow&space;x')&space;=&space;\frac{1}{{53&space;\choose&space;2}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\bg_white&space;S(x'&space;\rightarrow&space;x)&space;=&space;S(x&space;\rightarrow&space;x')&space;=&space;\frac{1}{{53&space;\choose&space;2}}" title="S(x' \rightarrow x) = S(x \rightarrow x') = \frac{1}{{53 \choose 2}}" /></a>

Hence, the acceptance probability is given by: 

<a href="https://www.codecogs.com/eqnedit.php?latex=\bg_white&space;min(1,&space;p(x')/p(x))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\bg_white&space;min(1,&space;p(x')/p(x))" title="min(1, p(x')/p(x))" /></a>

During implementation, we consider the log-probabilities replacing division with subtraction and compare the acceptance ratio with a sample drawn from a uniform distribution to determine whether the new state is accepted or rejected. 

All code with results for two different encrypted texts is given in the Jupyter Notebook **MCMC.ipynb**. The encrypted messages can be found in **message_large.txt** and **message_small.txt**. 

## References
- Christopher M. Bishop (2006) Pattern Recognition and Machine Learning, Springer-Verlag.
- P. Diaconis, The Markov chain Monte Carlo revolution, Bull. Amer. Math. Soc. 46 (2009) 179–205. [Google Scholar](https://scholar.google.com/scholar_lookup?hl=en-GB&publication_year=2009&pages=179-205&author=P.+Diaconis&title=The+Markov+chain+Monte+Carlo+revolution)
- Probabilistic and Unsupervised Learning, Approximate Inference and Learning in Probabilistic Models course at [Gatsby Computational Neuroscience Unit, UCL](http://www.gatsby.ucl.ac.uk/teaching/courses/ml1/)
