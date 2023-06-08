# Models

Models usually indicate as llm models. However, there are more model concept in langchain rather than llm models. According to the officical document([https://python.langchain.com/en/latest/modules/models.html](https://python.langchain.com/en/latest/modules/models.html)), there are 3 types of models that are supported by langchain (2023.6.7)

> LLMs: Large Language Models (LLMs) take a text string as input and return a text string as output.

> Chat Models: Chat Models are usually backed by a language model, but their APIs are more structured. Specifically, these models take a list of Chat Messages as input, and return a Chat Message.

> Text Embedding Models: Text embedding models take text as input and return a list of floats.

### llm model list supported by lang chain

[https://python.langchain.com/en/latest/modules/models/llms/integrations.html](https://python.langchain.com/en/latest/modules/models/llms/integrations.html)

### chat model

there are very few chat model compared with llm model

#### the difference between chat model and llm model

According to the offical document

> There are two different sub-types of Language Models:

> LLMs: these wrap APIs which take text in and return text

> ChatModels: these wrap models which take chat messages in and return a chat message

> This is a subtle difference, but a value prop of LangChain is that we provide a unified interface accross these. This is nice because although the underlying APIs are actually quite different, you often want to use them interchangeably.

For my understanding, there is no functional differences between these two types of model.

### Embedding text model

> The base Embedding class in LangChain exposes two methods: embed\_documents and embed\_query. The largest difference is that these two methods have different interfaces: one works over multiple documents, while the other works over a single document. Besides this, another reason for having these as two separate methods is that some embedding providers have different embedding methods for documents (to be searched over) vs queries (the search query itself).
