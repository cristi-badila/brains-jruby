# Brains

A Feedforward neural network toolkit for JRuby. Easily add machine learning features
to your ruby application using this Gem. Though there are faster native C implementations
available (e.g. FANN) we need something that is simple, beginner friendly and just works.

This java based implementation provides a balance of performance and ease of use.

## Installation

Do note that this gem requires JRuby as it uses a java backend to run the neural network
computations.

Add this line to your application's Gemfile:

```ruby
gem 'brains'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install brains

## Features

* Customizable network parameters depending on requirements
* Fast (A bit slower than FANN but significantly faster than a pure ruby implementation)
* NN backend implementation in Java which allows for platform agnostic implementation

## Usage

The brains gem contains facilities for training and using the feedforward neural network

Training (XOR example)
--------

Initialize the neural net backend

```ruby
require 'brains'


# Build a 3 layer network: 4 input neurons, 4 hidden neurons, 3 output neurons
# Bias neurons are automatically added to input + hidden layers; no need to specify these

nn = Brains::Net.create(2 /* no. of inputs */, 1 /*no. of outputs */, 1 /*hidden layer*/, { neurons_per_layer: 4 })
nn.randomize_weights
```

Consider that we want to train the neural network to handle XOR computations

```
A    B   A XOR B
1    1      0
1    0      1
0    1      1
0    0      0
```

First we build the training data. This is an array of arrays with each item
in the following format:

```
[
[[input1, input2, input3....], [expected1, expected2, expected3 ...]]
[[input1, input2, input3....], [expected1, expected2, expected3 ...]]
]
```

```ruby
training_data = [
  [[0.9, 0.9], [0.1]],
  [[0.9, 0.1], [0.9]],
  [[0.1, 0.9], [0.9]],
  [[0.1, 0.1], [0.1]],
]
```
Note that we map 1 = 0.9 and 0 = 0.1 since using absolute 1 and 0s might cause
issues with certain neural networks. There are other techniques to "normalize"
input, but this is beyond the scope of this example.

Start training on the data by calling optimize. Here we use 0.01 as the expected
MSE error before terminating and 1000 as the max epochs.

```ruby
result = nn.optimize(training_data, 0.01, 1_000 ) { |i, error|
  puts "#{i} #{error}"
}
```

To test the neural network you can call the feed method.

nn.feed( [test_input1, test_input2, .....]) => [output1, output2, ...]

Check if the network is trained. There are more advanced and proper techniques to check if
a network is sufficiently trained, but this is beyond the scope of this example.

```ruby
# test on untrained data
test_data = [
  [0.9, 0.9],
  [0.9, 0.1],
  [0.1, 0.9],
  [0.1, 0.1]
]

results = test_data.collect { |item|
  nn.feed(item)
}

p results

[[0.19717958808009528], [0.7983320405281495], [0.8386219299757574], [0.16609147896733775]]
```

Using the test data we can see the correlation and the neural network function now approximates
the xor function with the desired error:

```
[0.9, 0.9] => [0.19717958808009528]
[0.9, 0.1] => [0.7983320405281495]
[0.1, 0.9] => [0.8386219299757574]
[0.1, 0.1] => [0.16609147896733775]
```

Saving brain state
==================

Save the neuron state at any time to a string using to_json

```ruby
saved_state = nn.to_json
```

You can then save it to a file. You can then load it back using load()

```ruby
  nn = Brains::Net.load(saved_state)


  # use
  nn.feed([0.9, 0.9])
```

For other samples please take a look at the example folder.

Java Neural Network backend is based on:

https://github.com/jedld/brains

You can compile the java source code as brains.jar and use it directly with this gem.

## RNNs (Recurrent Neural Networks)

For recurrent neural networks (Look at the sine function in the examples). Only
the backpropagation through time (BPTT) training algorithm is
supported for now.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Resources

Machine learning is still a rapidly evolving field and research is ongoing on various aspects of it. This is just the tip of the iceberg, the field of machine learning is extremely complex, below are various resources for the average developer to get started:

ftp://ftp.sas.com/pub/neural/FAQ.html#questions

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/brains. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
