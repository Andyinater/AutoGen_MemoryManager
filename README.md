# AutoGen_MemoryManager
This repository demonstrates how a simple memory file can be incorporated into an Agent's routine using the AutoGen framework. 

# Overview
The project is the result of exploratory work in developing a paradigm for stable, reliable, and automated memory management for an AutoGen Agent. While just a proof of concept, the author believes that such a method would prove useful in a variety of workflows, either simply as-is for a conversational style memory, or with some tweaks for other tasks which may benefit from having dependable memory/facts. Please note that the current configuration lacks some QoL in terms of it's execution; the user must manually control some portions of the flow, but these could be automated with minimal effort. Feedback and collaboration is welcomed and encouraged. 

# How it Works
## Memory File
A text file serves as the "Long Term Memory". It contains a series of thoughts/ideas/memories seperated by a delimiter, in this case '|'. An example of a semi-populated memory could be: "Users name is Andy| User is allergic to peanuts, shellfish| User likes heavy metal music|"

## Agents
There are two Agents: The ConversingAgent, and the MemoryManagerAgent. They work hand-in-hand to both maintain a smooth conversation with the user while managing memories in the background.

### ConversingAgent
The ConversingAgent (CA) chats with the User, maintaining a helpful assistant persona. It is given access to two functions: `write_to_memory_intent` and `read_from_memory_intent`. 

The CA is instructed, through an engineered prompt, to call upon `write_to_memory_intent` anytime it considers something in the last chat message important or otherwise noteworthy as it relates to the user. Through the system prompt and function and parameter definitions, it is instructed to do so often, unprompted, and to condense the intended memory to a succinct sentence or phrase.

The CA is instructed to call `read_from_memory_intent` at the initialization of any conversation, as well as any time it believes it is being asked to remember something that "doesn't seem to exist".

### MemoryManagerAgent
The MemoryManagerAgent (MMA) does not interact with the user conversationally, but instead serves as a middle layer between the CA and its memory.

When the CA calls `write_to_memory_intent`, it passes a short message that represents the memory to be stored. When the MMA receives this memory it is instructed to:

  1. Read the existing memory
  2. Decide how the new memory should be incorporated

The MMA will then rewrite *the entire memory* based on that criteria.

### UserProxy
UserProxy is the controlled by the Human user, and is used in handling function calls from the CA.

### UserProxy_ForFunctions
UserProxy_ForFunctions is used in handling function calls from the MMA.

## Functions

### write_to_memory_intent(message)
Available to CA only. This function will start a chat with the MMA with the `message` as the initial message.

### read_from_memory_intent()
Available to CA only. This function will read the entire contents of the memory file to the CA.

### rewrite_memory(message)
Available to MMA only. This function will rewrite the entire contents of the memory file to MMA.

### read_from_memory()
Available to MMA only. This function will read the entire contents of the memory file to the CA.



# Getting Started
The author assumes you have [installed AutoGen](https://github.com/microsoft/autogen#installation) and have it running on your system. 

## Preparing Memory Path
Take note of `mem_path` in `LongTermMemory_Example.py`: This is the path to the memory file that will be used. While the *.txt does not need to exist, the directory "LTM_Test", or whichever is used, must be in the active directory.

## Starting a chat
Run the file with `python LongTermMemory_Example.py`

## Chatting with the ConversingAgent
You may chat with the CA as you would ChatGPT. But when something in the chat inspires the CA to make a function call, you must manually guide the process. 

### First Message
As the CA is instructed to read from memory at the start of every conversation, after starting `LongTermMemory_Example.py` you should immediately see a function call from CA for `read_from_memory_intent`. Press enter to skip/auto-reply, allowing the function call to execute.

### Chatting
After the initial memory read request, you will now be chatting with the CA. Feel free to use it in a free-form AI assistant way. If you are not seeing it make a request to `write_to_memory_intent`, a phrase that seems to have high success is "I am deathly allergic to shellfish". Please be aware that the CA may make multiple requests on its turn in the conversation - this *is* intended, as it opens good opportunities.

### Handling a Function Call: write_to_memory_intent 
When the CA determines something noteworthy to remember, it will make this function call. Press enter to allow it to execute. This will initiate some automatic activity:

  1. The MMA, upon receiving the memory message, will call `read_from_memory` to see the preexisting memory contents.
  2. The MMA will call `rewrite_memory`, rewriting the memory file to preserve the old memories while simultaneously including the new.

At this point, the prompt will ask the user to give feedback to the MMA. Press enter to auto-reply and end the conversation with MMA.

After the chat with MMA has ended, the CA should pick up the conversation from where it left off, while likely commenting on the last message which prompted the memory storage.

### Handling a Function Call: read_from_memory_intent

If you ask the CA if it remembers something, or if it thinks it needs more context, it *may* call this function (e.g. "Do you remember Kyle?"). Press enter without submitting any text to allow it to execute. You will see the memory be read into chat.

# Future Work
The author believes this paradigm of memory storage will prove useful in many flows, but that there remains much work to be done to see the true potential. In no particular order, here are some further areas of study you might consider:
  1. Have CA pass a hint in `read_from_memory_intent` that says what it is looking for, and have the MMA read the full memory and return only the pertinent details.
  2. Devise a "read, reflect, reorganise" routine for the MMA (or other) to be able to consider rewriting the memory for organization and conciseness *without* needing to add new information.
  3. Devise a "short term memory" which is unmanaged and differently organized - perhaps as a scratch pad for the CA. Also, a "short to long" routine to smartly transfer short term memory to long, perhaps even excluding some short term memories entirely.

And many more - this stuff is seriously exciting.


# Contributions/Personal Comments
This is my GitHub debut, more or less. I have good intentions, but I am sure my style and format of text/files/code leaves much to be desired. I am sorry, but I hope this proves useful. I will welcome any sort of improvements (merge requests? pull requests?), but I cannot promise I will be able to do it properly. 
