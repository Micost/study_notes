# Concepts in langchain

## Chain of Thought

>Chain of Thought (CoT) is a prompting technique used to encourage the model to generate a series of intermediate reasoning steps. A less formal way to induce this behavior is to include “Let’s think step-by-step” in the prompt.

Chain of Thought is not just the concept in lang chain, it is also an import concept in the whole NLP area. It is also called as "reasoning" in some other NLP area. It is a very important concept in lang chain. It is the key to make lang chain to be a powerful tool.

ref:

Chain-of-Thought Paper <https://arxiv.org/pdf/2201.11903.pdf>

The step by step Paper<https://arxiv.org/abs/2112.00114>

## Action Plan Generation

>Action Plan Generation is a prompting technique that uses a language model to generate actions to take. The results of these actions can then be fed back into the language model to generate a subsequent action.

## ReAct

>ReAct is a prompting technique that combines Chain-of-Thought prompting with action plan generation. This induces the model to think about what action to take, then take it.

## Self-ask

>Self-ask is a prompting method that builds on top of chain-of-thought prompting. In this method, the model explicitly asks itself follow-up questions, which are then answered by an external search engine.

## Prompt Chaining

>Prompt Chaining is combining multiple LLM calls, with the output of one-step being the input to the next.

## Memetic Proxy

>Memetic Proxy is encouraging the LLM to respond in a certain way framing the discussion in a context that the model knows of and that will result in that type of response. For example, as a conversation between a student and a teacher.

## Self Consistency

>Self Consistency is a decoding strategy that samples a diverse set of reasoning paths and then selects the most consistent answer. Is most effective when combined with Chain-of-thought prompting.

## Inception

>Inception is also called First Person Instruction. It is encouraging the model to think a certain way by including the start of the model’s response in the prompt.

## MemPrompt

>MemPrompt maintains a memory of errors and user feedback, and uses them to prevent repetition of mistakes.
