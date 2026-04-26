# Three architectures
> [!PDF|] [[7.pdf#page=4&selection=142,0,149,66|7, p.4]]
> > The decoder is the architecture we’ve introduced above. It takes as input a seriesdecoder of tokens, and iteratively generates an output token one at a time

> [!PDF|] [[7.pdf#page=5&selection=23,0,32,28|7, p.5]]
> > The encoder takes as input a sequence of tokens and outputs a vector repre-encoder sentation for each token. Encoders are usually masked language models, meaning they are trained by masking out a word, and learning to predict it by looking at surrounding words on both sides

> [!PDF|] [[7.pdf#page=5&selection=40,0,52,11|7, p.5]]
> > The encoder-decoder takes as input a sequence of tokens and outputs a seriesencoderdecoder of tokens. What makes it different than the decoder-only models, is that an encoderdecoder has a much looser relationship between the input tokens and the output tokens, and they are used to map between different kinds of tokens. 

# Conditional Generation of Text

# Prompting
instruction-tuning 专门训练大模型进行问答模式。

> [!PDF|] [[7.pdf#page=7&selection=71,2,78,34|7, p.7]]
> > A prompt is a text string that a user issues to a language model to getprompt the model to do something useful. 

> [!PDF|] [[7.pdf#page=8&selection=76,0,84,81|7, p.8]]
> > We therefore call the kind of learning that takes place during prompting incontext learning—learning that improves model performance or reduces some lossin-context learning but does not involve gradient-based updates to the model’s underlying parameters.

# Generation and Sampling
> [!PDF|] [[7.pdf#page=11&selection=71,0,74,10|7, p.11]]
> > In practice, however, we don’t use greedy decoding with large language models. A major problem with greedy decoding is that because the tokens it chooses are (by definition) extremely predictable, the resulting text is generic and often quite repetitive

> [!PDF|] [[7.pdf#page=12&selection=183,0,188,40|7, p.12]]
> > Alas, it turns out random sampling doesn’t work well either. The problem is that even though random sampling is mostly going to generate sensible, high-probable tokens, there are many odd, low-probability tokens in the tail of the distribution, and even though each one is low-probability, if you add up all the rare tokens, they constitute a large enough portion of the distribution that they get chosen often enough to result in generating weird sentences.

> [!PDF|] [[7.pdf#page=13&selection=249,17,250,76|7, p.13]]
> > In low-temperature sampling, we smoothly increase the probability of the most probable tokens and decrease the probability of the rare tokens.


# Training
## pretraining
self-supervision or self-training自训练

> [!PDF|] [[7.pdf#page=16&selection=24,2,46,1|7, p.16]]
> > Then we move to the next word, we ignore what the model predicted for the next word and instead use the correct sequence of tokens w1:t+1 to get the model to estimate the probability of token wt+2. This idea that we always give the model the correct history sequence to predict the next word (rather than feeding the model its best guess from the previous time step) is called teacher forcing.


## Finetuning
> [!PDF|] [[7.pdf#page=18&selection=81,54,86,2|7, p.18]]
> > This process of taking a fully pretrained model and running additional training passes using the cross-entropy loss on some new data is called finetuning. 


## instruction tuning

## alignment

# Evaluation
## perplexity

## downstream tasks

## time,memory,energy