# Memory

## Memory

> By default, Chains and Agents are stateless, meaning that they treat each incoming query independently (as are the underlying LLMs and chat models). In some applications (chatbots being a GREAT example) it is highly important to remember previous interactions, both at a short term but also at a long term level. The Memory does exactly that.

> LangChain provides memory components in two forms. First, LangChain provides helper utilities for managing and manipulating previous chat messages. These are designed to be modular and useful regardless of how they are used. Secondly, LangChain provides easy ways to incorporate these utilities into chains.

### ChatMessageHistory

ChatMessageHistory is a light wight memory wrapper which exposes convenience methods for saving Human messages, AI messages, and then fetching them all.

### ConversationBufferMemory

ConversationBufferMemory is just a wrapper around ChatMessageHistory that extracts the messages in a variable.

### ConversationBufferWindowMemory

ConversationBufferWindowMemory keeps a list of the interactions of the conversation over time. It only uses the last K interactions. This can be useful for keeping a sliding window of the most recent interactions, so the buffer does not get too large.

### ConversationKGMemory

This type of memory uses a knowledge graph to recreate memory.

### ConversationSummaryMemory

This type of memory creates a summary of the conversation over time. This can be useful for condensing information from the conversation over time.

### ConversationSummaryBufferMemory

ConversationSummaryBufferMemory combines the last two ideas. It keeps a buffer of recent interactions in memory, but rather than just completely flushing old interactions it compiles them into a summary and uses both. Unlike the previous implementation though, it uses token length rather than number of interactions to determine when to flush interactions.

### ConversationTokenBufferMemory

ConversationTokenBufferMemory keeps a buffer of recent interactions in memory, and uses token length rather than number of interactions to determine when to flush interactions.

### VectorStore-Backed Memory

VectorStoreRetrieverMemory stores memories in a VectorDB and queries the top-K most “salient” docs every time it is called.

### Store memory in db

Memories can be stored in different type of database include SQLite, PostgresSQL, redis , mongodb and so on
