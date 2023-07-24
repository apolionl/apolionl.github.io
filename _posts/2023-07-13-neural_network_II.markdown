---
layout: post
title: "A Neural Network from Scratch in Julia (II)"
date: 2023-07-13 23:00:59
categories: regular AI
tags: regular AI Julia
image: /assets/article_images/2022-08-15-julia_environment/night-track.JPG
image2: /assets/article_images/2022-08-15-julia_environment/night-track-mobile.JPG
---

The previous post finished with a code that would allow us to simulate layers and perform the summation procedure. In this post I create some fake data to work with, _i.e._ the input data. For this purpose, I changed a spiral data function that I found somewhere (written in python)

![No so random data input data to train the neural network in this tutorial.][pic1]{:style="display:block; margin-left:auto; margin-right:auto"}

The **Julia** code to call the fake data is:

```julia
# X is the fake data. 300 data points
# y are teh indexes.
(X, y) = spiral_data(100, 3)
```

<!--toc:start-->

- [ACTIVATION FUNCTION](#activation-function)
  - [ReLu](#relu)
  - [Softmax](#softmax)
- [LOSS FUNCTION](#loss-function)
  - [Cross-Entropy](#cross-entropy)
- [OPTIMISATION FUNCTION](#optimisation-function)
<!--toc:end-->

## ACTIVATION FUNCTION

Let's define what is an activation function:

> An activation function is a function that is _added_ into an artificial neural network in order to help the network learn complex patterns in the data. It takes in the output signal from the previous cell and converts it into some form that can be taken as input to the next cell.

> An activation function in a neural network defines how the weighted sum of the input is transformed into an output from a node or nodes in a layer of the network. **The most important feature in an activation function is its ability to add non-linearity into a neural network.**

In the broad sense, an **activation function** is a function that changes the summation by including non-linearlity, thus helping to neural network to fit the input data. Among the most common activation functions are:

- Rectified Linear Activation (ReLU)
- Logistic (Sigmoid)
- Hyperbolic Tangent (Tanh)
- Softmax

![After the summation an activation function initiates. Scheme of some activation function examples: sigmoid, tanh, ReLu.][pic2]{:style="display:block; margin-left:auto; margin-right:auto"}

### ReLu

**ReLu** one of the most used activation functions owing to its simplicity and cheap computational cost. In sort, any input _less than 0_ becomes 0, otherwise the values stay. In a more mathematical way that is:

$$ReLu \left(𝑥 \right) = Max \left( \left( 0, x \right) \right)$$

Julia's code of ReLu is fairly simple:

```julia
function Activation_ReLu(input::Array{Float64,2})
    return @. max(0.0, input)
end
```

The code including the **activation function**, **fake data** and the working code from the previous post looks:

```julia
using Random
Random.seed!(0)

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

# Activation function ReLu
function Activation_ReLu(input::Array{Float64,2})
    return @. max(0.0, input)
end


## ACTUAL CALCULATION ##
# Fake data.
(X, y) = spiral_data(100, 3)


layer1 = Layer_Dense(2, 5)
output1 = forward(layer1, X)

activation_output1 = Activation_ReLu(output1)

println(activation_output1)
```

### Softmax

The softmax function is an alike logistic activation function that is used for multiclass classification. This function is mainly used in the output layer. One of the main purposes of this function is to avoid the exponential grow of weights and values, this is necessary as in certain cases weights and values can grow -_probabilities will differ enormously from one neuron to other_- to certain degree that is hard to get proper probabilities. Softmax function prevents the probabilities to be negative, a property of the exponential function.

Softmax consist in finding the max numerical value per row, then normalise those values. A quick implementation in **Julia** looks like:

```julia
function activation_softmax(input::Array{Float64,2})
    # Find and subst MAX per row
    input .-= maximum(input; dims=2)
    # e to all values
    exp_values = exp.(input)
    return exp_values ./ sum(exp_values; dims=2)
end
```

Now, adding the softmax function to our previous code from the ReLu implementation, we have:

```julia
using Random
Random.seed!(0)

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

# Activation function ReLu
function Activation_ReLu(input::Array{Float64,2})
    return @. max(0.0, input)
end

# Activation function softmax
function activation_softmax(input::Array{Float64,2})
    # Find and subst MAX per row
    input .-= maximum(input; dims=2)
    # e to all values
    exp_values = exp.(input)
    return exp_values ./ sum(exp_values; dims=2)
end

## ACTUAL CALCULATION ##
# Fake data.
(X, y) = spiral_data(100, 3)


layer1 = Layer_Dense(2, 3)
layer2 = Layer_Dense(3, 3)

output1 = forward(layer1, X)
activation_output1 = Activation_ReLu(output1)

output2 = forward(layer2, activation_output1)
activation_output2 = activation_softmax(output2)

println(activation_output2[begin:5, :])
```

## LOSS FUNCTION

It is the function to estimate the difference between input and estimated data. I will mention that there are several loss functions and their used depend on the data and the computation cost the same. Here, there are some of the most popular functions.

- Mean Squared Error loss
- Mean Absolute Error loss
- Binary cross entropy
- Cross-Entropy
- Mean Squared Logarithmic Error Loss

### Cross-Entropy

This is perhaps the most widely used function for loss estimation in neural networks for classification, applied when using softmax classifier. With this function I will estimate _how wrong is the model that we have being training so far_. One must be aware that the cross-entropy formula simplifies from its original form by _One-Hot encoding_ to simply `L= -log(y)`. Where `L` is the loss, and `y` the predicted value. Here are some of the things to have in mind for implementing the _Cross-Entropy function_:

- `-log(y) = ln(y)`
- Because we use `log` `y` must be different from zero (0).
- Large loss means that the probability confidence of the neural network is small.

Implementation in **Julia** looks like:

```julia
function Loss_CategoricalCrossentropy(y_pred::Array{Float64, 2}, y_true::Array{Float64, 2})
    samples, = size(y_pred)

    # Clamp
    y_pred_clipped = similar(y_pred)
    y_pred_clipped .= clamp(y_pred, 1e-7, 1-1e7)


    if ndims(y_true) == 1
    # Vector correct_con with points from clampled(2 dims) of samples # columns
    correct_confidences =  [ y_pred_clipped[[i, y_true[i]] for i in 1:samples ]
    elseif ndims(y_true) == 2
    # element mult , them sum per row
    correct_confidences =  sum(y_pred_clipped .* y_true, dims= 2)
    else
    println("There are more than 3D. ")
    end

    # Calculate likelihoods
    negative_log_likelihoods = -log.(correct_confidences)

    # Because here there is no class, it outputs the mean right away
    return mean(negative_log_likelihoods)
end
```

## OPTIMISATION FUNCTION

This function determine the "_best_" direction in which weights and bias should be tune, so that in the next learning step the estimated values are closer to the input data. These are some widely used algorithms:

- Adagrad
- Adam

[pap1]: https://github.com/shellysheynin/Deep-Learning-Book "Deep Learning Book - Shelly Sheynin."
[pap2]: https://udlbook.github.io/udlbook/ "Understanding Deep Learning - Simon J.D. Prince"
[pap3]: https://github.com/manjunath5496/Neural-Networks-Books "Many free books"
[pap4]: https://www.deeplearningbook.org "Deep Learning by Ian Goodfellow, Yoshua Bengio and Aaron Courville"
[pap5]: https://s3.amazonaws.com/assets.datacamp.com/email/other/ML+Cheat+Sheet_2.pdf "Machine learning algorithms cheat sheet"
[pic1]: /assets/article_images/2023-07-13-neural_network_II/spiral.png "Fake input data used in this blog/tutorial."
[pic2]: /assets/article_images/2023-07-13-neural_network_II/single_neu.png "Single neural network."
[pic3]: /assets/article_images/20\right)23-07-09-neural_network_I/nn.png "Shallow network."
