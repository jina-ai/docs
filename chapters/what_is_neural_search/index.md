# What is Neural Search?

In short, neural search is a new approach to retrieving information. Instead of telling a machine a set of rules to understand what data is what, neural search does the same thing with a pre-trained neural network. This means developers don’t have to write every little rule, saving time and headaches, while the system trains itself to get better as it goes along. One such company providing an open-source neural search framework is [Jina](https://github.com/jina-ai/jina/).

## Background

Search is big business, and getting bigger every day. Just a few years ago, searching meant typing something into a text box (ah, those heady days of Yahoo! and Altavista). Now search encompasses text, voice, music, photos, videos, products, and so much more. Just before the turn of the millennium there were only 3.5 million Google searches a day. Today (according to the top result for search term `2020 google searches per day`) that figure could be as high as 5 billion and rising, more than 1,000 times more. That’s not to mention all the billions of Wikipedia articles, Amazon products, and Spotify playlists searched by millions of people every day from their phones, computers, and virtual assistants.

Just look at the stratospheric growth in Google queries — and that’s only until 2012!

![](https://cdn-images-1.medium.com/max/800/0*wnDzwewnBW9iIwYO)

In short, search is _huge_. We’re going to look at the reigning champ of search methods, symbolic search, and the plucky upstart contender, neural search.

![Source: Backgrounds from Unsplash](https://cdn-images-1.medium.com/max/1200/1*czKD1E9fL1ndRGzwjv9Kvg.png)
Source: Backgrounds from Unsplash

**Note:** This article is based on a [post by Han Xiao](https://hanxiao.io/2018/01/10/Build-Cross-Lingual-End-to-End-Product-Search-using-Tensorflow/) with his permission. Check there if you want a more technical introduction to neural search.

## Symbolic search: rules are rules

Google is a huge general-purpose search engine. Other companies can’t just adapt it to their needs and plug it into their systems. Instead, they use frameworks like [Elastic](http://elastic.co/) and [Apache Solr](https://lucene.apache.org/solr/), symbolic search systems that let developers write the rules and create pipelines for searching products, people, messages, or whatever the company needs.

Let’s take [Shopify](http://www.shopify.com) for example. They use Elastic to index and search through millions of products across hundreds of categories. This couldn’t be done out-of-the-box or with a general purpose search engine like Google. They have to take Elastic and write specific rules and pipelines to index, filter, sort, and rank products by a variety of criteria, and convert this data into symbols that the system can understand. Hence the name, _symbolic search_. Here’s [Greats](http://www.greats.com/), a popular Shopify store for sneakers:

![](https://cdn-images-1.medium.com/max/1200/1*gL70yGnLXRdWTdzY-LsLhw.png)

You and I know that if you search for `red nike sneakers` you want, well, red Nike sneakers. Those are just words to a typical search system though. Sure, if you type them in you’ll hopefully get what you asked for, but what if those sneakers are tagged as _trainers_? Or even tagged as _scarlet_ for that matter? In cases like this, a developer needs to write rules:

*   **Red** is a color
*   **Scarlet** is a synonym of red
*   **Nike** is a brand
*   **Sneakers** are a type of footwear
*   Another name for sneakers is **trainers**

Or, expressed in JSON as key-value pairs:

Each of these key-value pairs can be thought of as a symbol, hence the name _symbolic search_. When a user inputs a search query, the system breaks it down into symbols, and matches these symbols with the symbols from the products in its database.

![](https://cdn-images-1.medium.com/max/800/0*teWlGGTPTrSWv53P)

But what if a user types `nikke` instead of `nike`, or searches `shirts` (with an `s`) rather than `shirt`? There are so many rules in language, and people break them all the time. To get effective symbols (i.e. knowing that `nikke` really means `{"brand": "nike"}`), you need to define lots of rules and chain them together in a complex pipeline:

![](https://cdn-images-1.medium.com/max/800/0*4wLeOjEStyGbFaSV)

## Drawbacks of symbolic search

### You have to explain every little thing

Our example search query above was `red nike sneaker man`. But what if our searcher is British? A Brit would type `red nike trainer man`. We would have to explain to our system that sneakers and trainers are just the same thing with different names. Or what is someone is searching `LV handbag`? The system would have to be told `LV` stands for `Louis Vuitton`.

Doing that for every kind of product takes _forever_ and there are always things that fall between the cracks. And if you want to localize for other languages? You’ll have to go through it all over again. That means a lot of hard work, knowledge, and attention to detail.

![](https://cdn-images-1.medium.com/max/800/0*-UquURWXwEnCNbLH)

### It’s fragile

Text is complicated: As we explained above, if a user types in `red nikke sneaker man` a classic search system has to recognize that they're searching for a red (color) Nike (brand with corrected spelling) sneaker (type) for men (sub-type). This is done by interpreting the search string and product details to symbols via the pipeline, and these pipelines can have major issues.

![](https://cdn-images-1.medium.com/max/800/0*BuOUzbFcDXUDr4gg)

*   Every component in the chain has an output that is fed as input into the next component along. So a problem early on in the process and break the whole system
*   Some components may take inputs from multiple predecessors. That means you have to introduce more mechanisms to stop them blocking each other
*   It’s difficult to improve overall search quality. Just improving one or two components may lead to no improvement in actual search results
*   If you want to search in another language, you have to rewrite all the language-dependent components in the pipeline, increasing maintenance cost

## Neural search: (Pre )train, don’t explain

An easier way would be a search system trained on existing data. If you train a system on enough different scenarios beforehand (i.e. a pre-trained model), it develops a generalized ability to find outputs that match inputs, whether they’re [flowers](https://github.com/jina-ai/examples/tree/master/flower-search), [lines from South Park](https://github.com/jina-ai/examples/tree/master/southpark-search), or [Pokémon](https://github.com/jina-ai/examples/tree/master/pokedex-with-bit). You can plug this model directly into your system and start indexing and searching right away.

The code is pretty straightforward. It loads a “Flow”, which in turns loads a series of modules to process, index, and query your data:

This way, you don’t need to waste hours writing endless rules for your use case. Instead, just include a line in your code to download the model you want from an “app store” (like the upcoming [Jina Hub](https://github.com/jina-ai/jina-hub/)), and get going.

![](https://cdn-images-1.medium.com/max/800/0*YyOEFgDpyzcJglGi.gif)

Compared to symbolic search, neural search:

*   Removes the fragile pipeline, making the system more resilient and scalable
*   Finds a better way to represent the underlying semantics of products and search queries
*   Learns as it goes along, so improves over time

## Does neural search work?

A search “works” if it understands and returns quality results for:

*   **Simple queries:** Like searching ‘red’, ‘nike’, or ‘sneakers’
*   **Compound queries:** Like ‘red nike sneakers’

If it can’t even do those, there’s no point in checking for fancy things like spell-checking and ability to work in different languages.

Anyway, less talking, more searching:

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

So, as you can see, neural search does pretty well!

## Comparing symbolic and neural search

So, how does neural search compare to the reigning champ that is symbolic search? Let’s take a look at the pros and cons of each:

![](https://cdn-images-1.medium.com/max/800/1*yXtMVXx8K6HrSlm2PaGoUQ.png)

We’re not trying to choose between Team Symbolic and Team Neural. Both approaches have their own advantages and complement each other pretty well. So a better question to ask is: Which is right for your organization?

## Try neural search for yourself

![](https://cdn-images-1.medium.com/max/800/0*6zE8Q1xX7y8nb0jf)

There’s no better way to test-drive a technology than by diving in and playing with it. Jina provides pre-trained Docker images and [jinabox.js](https://github.com/jina-ai/jinabox.js/), an easy-to-use front-end for searching text, images, audio, or video. There’s no product search example (yet), but you _can_ search for more light-hearted things like [your favorite Pokémon](https://github.com/jina-ai/examples/tree/master/pokedex-with-bit) or [sentences from Wikipedia](https://github.com/jina-ai/examples/tree/master/wikipedia-sentences).
