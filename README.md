# prAIdictor
Text prediction using AI and standard algorithms


# Roadmap

- [ ] Get a large dataset of text, sort words by frequency
- [ ] Train (or get a pretrained) word embedding model (word2vec, GloVe, etc.)
- [ ] Simple working algorithm
From here we should be able to predict the next word given a sequence of words.
- [ ] Get a fast and efficient implementation of a classic algorithm (e.g. Kneser-Ney smoothing) for snappy predictions
- [ ] Get a more accurate AI model that might be slower (a transformer looks like the best tool for the job).
- [ ] Caching of predictions

## Basic idea

The algorithm will try to predict the next most likely possibilities with a threshold of p. The user will be able to select one of the predictions, or continue typing. If the user selects a prediction, the algorithm will (maybe) be trained on the new data.

It's very important to figure what latency we can get away with. We don't want the user to sit around to get the predictions.

## Caching of predictions

The idea here is that if we can predict faster than the user types, we can show better predictions:

When the probability of the last prediction is below p - we can recursively call the algorithm with the new data. This will give us a new set of predictions, which we can show to the user if it the user happens to choose one of the predictions in the first set. This is trivially parallelizable.

## Size of the prediction set (value of p)

The value of p influences how far ahead the algorithm will predict. If p is too low, the algorithm will fail to cache predictions, but if it's too high and we are overly optimistic it will be slow again (if the user types a letter that is not on the list we need to recalculate). We should be able to find a good value for p by testing. One good test is to test the words per minute (wpm) of users. This parameter can even be adjusted on the fly.

## Limitations:

The classic algorithm will probably fail for conjugated verbs. We might want to stop at the root of the word and then let the user finish the word. It's unclear if the AI model will have this limitation. 

## Implementation

The classic algorithm will be implemented in rust and the AI model in python with pyTorch for now. If the latency to call the python model is too high, we can try to implement the AI model in rust as well.