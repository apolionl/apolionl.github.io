---
layout: post
title: "Building a Neural Network from Scratch I"
date: 2023-07-09 10:52:01
categories: regular AI
tags: regular AI Julia
image: /assets/article_images/2023-05-19-why_latex/night-track.JPG
image2: /assets/article_images/2023-05-19-why_latex/night-track-mobile.JPG
---

In the last 5 years or so, the scientist in the theoretical chemistry community have been working intensively at the boom of artificial intelligence (AI). I could nightlight scientific publications, but I guess at this point is kind of useless. There are publications in many areas like material science, and DFT, therefore, my selection list would be subjective. Owing to my personal interest and field -I do not directly work on AI, but I work on thing for which AI could be applied- time to time I cross with papers that seem interesting. Interesting, sometimes I see that authors seem to **apply** AI without doing a compressive analysis of the results. With this in mind, I decided to explain my basic knowledge on machine learning, let's say from scratch. In the following two posts I will code in _**Julia**_ a **Neural network (NN)** from scratch.

The objective of the post is to show how relatively easy is to construct a NN, while I intend to show why NN can be difficult to converge. NN converge is an ongoing topic of research, thus I will not discuss it in detail. Nevertheless, after reading this post (and the next), one should understand to some extent NN.

<!--toc:start-->

- [BASIC KNOWLEDGE (_**FREE**_)](#basic-knowledge-free)
- [SINGLE NEURAL NETWORK](#single-neural-network)
  - [Summation on a single neuron](#summation-on-a-single-neuron)
- [SHALLOW NETWORK](#shallow-network)
- [BEYOND THE SHALLOW NETWORK](#beyond-the-shallow-network)
<!--toc:end-->

## BASIC KNOWLEDGE (_**FREE**_)

Because I do not work in AI, and I want to avoid compromises in this post I will list bellow some nice sources in NN. Fell free to consult them.

1. [**Nice introductory book: Deep Learning Book**][pap1].
2. [**Free book with focus on algorithm description: Understanding Deep Learning**][pap2].
3. [**Collection of free books in AI**][pap3].
4. [**Free online version of the so-called Deep Learning Bible: Deep Learning**][pap4].
5. [**Cheat sheet on machine learning algorithms**][pap5].

## SINGLE NEURAL NETWORK

![Neural Network with 5 neurons in the hidden layer.][pic1]

Neural network calculations are intimidating in some occasions owing to the complexity of the hidden layer. For simplicity, I first focus on a single neuron. A standard single neural network consists of: input, weights and bias, net sum, and activation function.

![Part of a single neural network according: input value (blue), weights and bias (gray), net sum (green), and activation function (pink).][pic2]

As one can see in the above picture the kind of calculations performed in a neural network are simple, namely sum and multiplication (in the green circle). To make things simple, I will assume a scenario similar to that of the previous image. **Let's assume that there are 3 neurons (X1, X2, X3) feeding our neuron (the green one based on our scheme)**. Because there are 3 feeding neurons, there are **3 weights (W1, W2, W3)**, that is a unique connection between the feeding neurons (X in blue) and our neuron (green). Because the calculations are done in our green neuron, only one, there is **only one bias**, that is correct every neuron has a unique bias.

### Summation on a single neuron

For the single neuron we have: 3 inputs (X1, X2, X3), 3 weights (W1, W2, W3) and one bias. The code in _**Julia**_ looks like:

```JULIA
# Inputs
inp = [1, 2, 3]

# 3 weights
wei = [0.2, 0.5, -0.5]

# 1 bias
bia = 2

output = (inp[1] * wei[1] + inp[2] * wei[2] + inp[3] * wei[3]) + bias

println(" The result is $output ")

# Output
The results is 4.7
```

Easy right? But what are those weights and bias?. Well, from [**the book ""Understanding deep learning**][pap2]:

> If every element in one layer connects to every element in the next, the network is fully connected. These connections represent slope parameters in the underlying equations and are referred to as network weights. The offset parameters are called biases.

In short, **weights and bias are parameters that must be learned**. In AI one does try to predict something, in my example X1-3. Those inputs never change, right? Because those are the parameters one aims. The output (the orange circle) is our prediction, this one is the final result, as such it does not change once the calculations finishes. That means that weights and bias are **parameters that define the behaviour of a _neuron_, so well-tuned weights and bias yield good reproduction of the input data (X1, X2, X3)**. One can think of weight as something that _scale_ the input data, thus a weight of _two_ will make the input _twice larger_, whereas a weight of 0.5 shortens the input by half. Bias instead _offset_ the product of weight and input, if you think of a point in the X axis, bias just move that point x units.

## SHALLOW NETWORK

![Shallow network scheme with notation of consisting parts: input layer, hidden layer, output layer.][pic3]

Let's expand the summation calculation from a single neuron to a layer, as in the picture above. For this example there are 4 input neurons **(X1, X2, X3, X4)** in the input layer and 3 neurons **(h, h2, h3)** in the hidden layer. Remember, each neuron in the hidden layer has a bias. Using the previous code to this example one has:

```JULIA
# input layer X1, X2, X3, X4
inp = [1, 2, 3, 2.5]

# 3 weights. 1 per input neuron
wei1 = [0.2, 0.8, -0.5, 1]
wei2 = [0.5, -0.91, 0.26, -0.5]
wei3 = [-0.26, -0.27, 0.17, 0.87]

# 3 final Bias
bia1 = 2
bia2 = 3
bia3 = 0.5

output = [
    inp[1] * wei1[1] + inp[2] * wei1[2] + inp[3] * wei1[3] + inp[4] * wei1[4] + bia1,
    inp[1] * wei2[1] + inp[2] * wei2[2] + inp[3] * wei2[3] + inp[4] * wei2[4] + bia2,
    inp[1] * wei3[1] + inp[2] * wei3[2] + inp[3] * wei3[3] + inp[4] * wei3[4] + bia3,
]

println(" The result is $output ")

# Output
The results is 4.8 1.21 2.385
```

In real world examples the programmer does not need to write such a long and tedious script. Instead, often basic linear algebra will make the trick, namely matrix multiplication (the dot product). Matrix multiplication is outside the scope of this blog, nevertheless, I will comment that the most important thing is to be aware of the **shape** of your matrix; an indispensable requisite in matrix multiplication (you will see soon). Consequently, passing `wei1-3` and `bia1-3` to matrixes, the codes become:

```JULIA
# input layer X1, X2, X3, X4
inp = [1, 2, 3, 2.5]

# 3 weights. 1 per set
wei = [0.2 0.8 -0.5 1; 0.5 -0.91 0.26 -0.5; -0.26 -0.27 0.17 0.87]

# 3 final Bias
bia = [2, 3, 0.5]

output = wei * inp + bia

println(" The result is $output ")

# Output
The results is 4.8 1.21 2.385
```

The code now is simpler and easy to understand.

## BEYOND THE SHALLOW NETWORK

Up to this point we see that computations are simpler, but this example is for a single hidden layer, real world applications require multiple hidden layers. I will now code some functions that permit computations in a batch. Before that, some important notes that I must point out.

- In AI calculations weights and bias are initialised using random algorithms, unless one has those values from previous models. In weights initialisation is better if those values are close to each other, usually between `-1 and 1`. Bias initialisation is usually set to `0`, unless there are some problems with the calculations.
- **Julia** does not have classes as python, thus I will use `function` and `struct`.
- **Julia** benefits from using strong typing, _I am use code like that, therefore most of the code is strongly typed_.
- The bias is added to each of the **rows** from the matrix product, for this reason look that in the forward pass I transpose the bias `Vector{Float64}`. To transpose a `Vector` in **Julia** use `'` at the end of the variable, for instance `MyBias'`.

```JULIA
using Random
Random.seed!(0)

# initial conditions AKA input layer
X::Array{Float64,2} = [
    1 2 3 2.5
    2.0 5.0 -1.0 2.0
    -1.5 2.7 3.3 -0.8
]

# Make "class" Layer_Dense
mutable struct Layer_Dense
    weights::Array{Float64,2}
    biases::Vector{Float64}
end

# Construct Layer
function Layer_Dense(n_int::Int, n_neur::Int)
    weight::Array{Float64,2} = 0.1 * rand(n_int, n_neur)
    bias::Vector{Float64} = zeros(n_neur)
    return Layer_Dense(weight, bias)
end

# Layer transpose is '
function forward(Layer::Layer_Dense, input::Array{Float64,2})
    return input * Layer.weights .+ Layer.biases'
end

layer1 = Layer_Dense(4, 5)
layer2 = Layer_Dense(5, 2)

output1 = forward(layer1, X)
output2 = forward(layer2, output1)

println(output2)
```

The code have annotations, thus is easy to understand what is done in the code. One thing I want to point is that the `shape` must be considered, look how the number of neurons of `layer1` (5), is the same number of inputs of `layer2`.

IN the next block I will introduce activation functions and so on.

[pap1]: https://github.com/shellysheynin/Deep-Learning-Book "Deep Learning Book - Shelly Sheynin."
[pap2]: https://udlbook.github.io/udlbook/ "Understanding Deep Learning - Simon J.D. Prince"
[pap3]: https://github.com/manjunath5496/Neural-Networks-Books "Many free books"
[pap4]: https://www.deeplearningbook.org "Deep Learning by Ian Goodfellow, Yoshua Bengio and Aaron Courville"
[pap5]: https://s3.amazonaws.com/assets.datacamp.com/email/other/ML+Cheat+Sheet_2.pdf "Machine learning algorithms cheat sheet"
[pic1]: /assets/article_images/2023-07-09-neural_network_I/neural.png "Neural networ scheme."
[pic2]: /assets/article_images/2023-07-09-neural_network_I/single_nn.png "Single neural network."
[pic3]: /assets/article_images/2023-07-09-neural_network_I/nn.png "Shallow network."
