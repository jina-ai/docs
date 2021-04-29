# What is Neural Search?

##### In this section, you will get an understanding of what neural search is and how it differs from existing search methods

#### Overview

Like anything in technology, search engines evolve over time. While programming languages evolved from structured programming to concurrency-oriented programming, search has evolved from catalog, to symbolic, to neural-network-based search:

![](https://hanxiao.io/2020/08/02/Layer-of-Abstraction-when-Building-Tensorflow-for-Search/ff7f8875.png)

Traditional search frameworks use rules and complex pipelines to parse the data being searched. Neural search, on the other hand, relies on neural networks which actually "understand" that data.

#### Why neural search?

Unlike traditional search methods, neural search offers a number of advantages:

##### Universality

When most people think of search, they think about a text box, like Google. That's the traditional thinking, and that's what traditional search frameworks offer: text-to-text search. If a developer wants to offer a new language option in their search engine, implementing all the rules and logic is hard work.

With neural search, the algorithm does all the work. So if you want to switch from English to French, you just switch out an [English language algorithm](https://huggingface.co/distilbert-base-uncased) for a [French one](https://huggingface.co/camembert-base). Or if you want to search images, just drop in [image recognition algorithm](https://github.com/google-research/big_transfer). There's no need to rewrite the rules (since the algorithm "knows" them already).

In short, if data can be encoded to something a computer can understand, it can be searched with neural search.

##### Flexibility

Traditional searches are very brittle. For example, unless a developer creates a rule that states "red", "scarlet" and "crimson" are very similar, there's no way the search engine would "know" that. So if a user searches `red sneakers`, crimson or scarlet sneakers wouldn't rank highly in the search results.

Neural search uses algorithms that have been trained on huge datasets to understand natural language. So it's likely whatever synonym for red you can think up, the algorithm (and thus your search engine) already knows it. The result? Crimson and scarlet sneakers rank highly for the search term `red sneakers`.

##### Forgiveness

In a similar way, without a spell-checking library, traditional search can't understand that when a user types `pokeman` or `pokey man` they mean `pokemon`. And even with a spell-checking library, often new words (like Pokemon) just aren't in the dictionary.

Neural search can make a "best guess" in situations like these. If a word exists in the training dataset (which are often more up-to-date than spell-checking dictionaries) it can guess a user's intent from their misspelled query.

#### Comparing neural and symbolic search

When we talk about "traditional search", we typically mean symbolic search. Symbolic search frameworks include Lucene, Solr, and Elastic Search. 

Comparing neural and symbolic search is like comparing apples and oranges. Symbolic search excels in its given domain of single-language NLP, while neural search is a lot more flexible but still relatively new.

While neural search has many advantages, there are still some situations where it makes sense to use traditional search:

- Your search needs are constrained and unlikely to change over time
- You are searching for text in just one language
- Speed is more important than getting the most accurate results
- You want to understand **why** you're getting your *those* results
- Your computing power is highly limited
- You have a small amount of data (neural search requires big data)

On the other hand, there are some situations where neural search makes more sense

- Your search needs are likely to expand over different languages and data types
- Accurate results are important, even if you can't perfectly understand why you got *those* results
- You have a "noisy" set of data to search through
- You have a large dataset for the neural network to "learn"
- You don't have much knowledge in the search domain
- You want something that "just works" out of the box

#### Neural search in action

A search “works” if it understands and returns quality results for:

- **Simple queries:** Like searching `red`, `nike`, or `sneakers`
- **Compound queries:** Like `red nike sneakers`

```
🇬🇧 nike
```

![](https://cdn-images-1.medium.com/max/1200/0*5OYeNWKsGcF0gVRy)

```
🇩🇪 nike schwarz (different language)
```

![](https://cdn-images-1.medium.com/max/1200/0*siGeHPkUzI1Yg7wO)

```
🇬🇧 addidsa (misspelled brand)
```

![](https://cdn-images-1.medium.com/max/1200/0*MJwcdam2P6dmNPh3)

```
🇬🇧 addidsa trosers (misspelled brand and category)
```

![](https://cdn-images-1.medium.com/max/1200/0*4LLQp3l-PU7TXYzc)

```
🇬🇧 🇩🇪 kleider flowers (mixed languages)
```

![](https://cdn-images-1.medium.com/max/1200/0*9uRA1OnkjU6h1D_C)


