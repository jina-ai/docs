
# Jina Documentation Style Guide

Guides are publicly available and represent the Jina AI brand and the quality of the product. Please adhere to the following steps to ensure basic professionalism in the docs.

## Guiding principles:

-   **Accessibility**. Everyone who needs to should understand our documents.

-   **Professionalism**. A consistent writing and formatting style will give a professional look to the documentation.

## Specific guidelines:

**Code Context:** Anything inside of a code snippet.  

**For anything not covered here, we default to the [Google developer style guide.](https://developers.google.com/style)**

1.  Check the spelling and grammar in your contributions. Most editors include a spell checker or have an available spell-checking plugin. You can also paste your text into a Google Doc or other document software for a more robust spelling and grammar check. We use [American English spelling](https://www.oxfordinternationalenglish.com/differences-in-british-and-american-spelling/).

2.  Avoid [run-on sentences](https://www.grammarly.com/blog/run-on-sentence-basics/?gclid=CjwKCAiA65iBBhB-EiwAW253W1hOQlSbJZy6pz-2IrzriLcR9zyVubamEH_vni7zjORgu8sv9x6XVBoCdRkQAvD_BwE&gclsrc=aw.ds). Generally, keep sentences to less than 13 words long.

3.  When explaining technical steps, use a numbered step by step process. Don't list out every step in a single paragraph. For more details on this, see information [here](https://developers.google.com/tech-writing/one/lists-and-tables).

4.  When explaining general concepts, paragraphs should be used. Yet paragraphs should not be over six sentences long.

5.  Use the [second person tense](https://www.grammarly.com/blog/first-second-and-third-person/) and describe actions to the user. (this is the same as active style or imperative style)

> You should open Jina using python.
> In your pod, you will find.

6.  Do not use compound sentences, chains of clauses, and location-specific idioms, making text hard to understand and translate.

7.  Avoid semicolons.

8. When discussing a Jina component inside a code context (i.e. referring to CLI output or actual Python/YAML code) use Jina terms exactly as they should be portrayed in that context.

9. When dicussing a Jina component outside of a code context, capitalize all terms and products. Such as Flow, Pod, JinaD. Anything found in the [Jina glossary](https://docs.jina.ai/chapters/glossary/glossary.html) or [Jina 101](https://docs.jina.ai/chapters/101/index.html). Including Executor types (Evaluator, Segmenter, etc.)

10. Don't capitalize Flow types (index, query). Don't capitalize the adjective or  `-tion`  form (i.e. evaluation, evaluating, segmenting, etc)

11.  
- For top-level titles: Use Title Case [You can check with this website [here\]
](https://titlecaseconverter.com/)		
- For section titles: Use sentence case

12. For code markup:
- Use `<code>` in HTML or `` ` `` in Markdown to apply a monospace font and other styling to [code in text](https://developers.google.com/style/code-in-text), inline code, and user input.
- Use code blocks, `<pre>` or ` ``` `, for [code samples](https://developers.google.com/style/code-samples) or other blocks of code.
- Add a correct language identifier to enable syntax highlighting in your fenced code block. See [link here](https://docs.github.com/en/github/writing-on-github/creating-and-highlighting-code-blocks#syntax-highlighting) for instructions.
- Use code font to mark up code, such as class names, method names, HTTP status codes, console output, and placeholder variables.
- Capitalization should match the actual working code or output. 

```python
	#correct:
    	import numpy as np
    	from jina.executors.encoders import BaseImageEncoder

    	#incorrect
    	import Numpy as np
    	from jina.executors.Encoders import BaseImageEncoder
```

13. If you need to refer to installing Jina, use the following snippet:

| Jina Product | Snippet to use |
|--|--|
| Jina Core | **Requirements**: You have installed the latest stable release of Jina Core according to the instructions found [here](https://docs.jina.ai/chapters/install/index.html).  |
| Jina Dashboard | **Requirements**: You have installed the latest stable release of Jina Dashboard according to the instructions found [here](https://github.com/jina-ai/dashboard).  |
| Jina Box | **Requirements**: You have installed the latest stable release of Jina Box according to the instructions found [here](https://docs.jina.ai/chapters/box/introduction/index.html).  |

14.  Don’t use abbreviations without explaining them first. With the exception of abbreviations and acronyms that are common in the computer industry. Examples of commonly used abbreviations are YAML, API, GIF, PDF, REST, ID. If you are unsure if you should define something or not, please ask in #proj-docs on Slack.

15. If you are going to use one of the following words, please follow the specific rule mentioned.
	- **YAML**, not yaml.
	- **ID**, not id.
	- **gRPC**, not gprc.
	- **API, REST, ID** should all be in capital letters.

16. Jina runs **on** the cloud, not **in** in the cloud. Our slogan is "An easier way to do neural search **on** the cloud".
