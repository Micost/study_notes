# Prompts

> A prompt is the value passed into the Language Model. This value can either be a string (for LLMs) or a list of messages (for Chat Models).

The Prompts can be understood as the init input of the models or the basic configs of the models.

### PromptTemplate

> PromptTemplates are responsible for constructing a prompt value. These PromptTemplates can do things like formatting, example selection, and more. At a high level, these are basically objects that expose a format\_prompt method for constructing a prompt. Under the hood, ANYTHING can happen.
