### Ollama
There are lots of free Models. You can use any of the model from there. 
gemma3 = 270 million of parameters

We are going to use uv. instead of anaconda. 
###  System vs User Prompt
The system prompt tell the LLM who it is, how it should behave and what rules to follow like the personality, boundaries etc. and user prompt is the actual question like input, task ,etc. 

So I may ask, why put them in separate format ? Cuz if we put everything in user prompt, the model forgets long-term rules. And system prompt stays stable. 

Every request to every LLM must be in this structure
```js
[
  {"role": "system", "content": "You are helpful."},
  {"role": "user", "content": "Hello!"},
  {"role": "assistant", "content": "Hi! How can I help?"},
  {"role": "user", "content": "Explain JWT."}
]
```
This entire list is called messages array. Each of them have two keys: `role` and `content`. I may think that system prompt and user prompt are given separately. But that is not true, They are both entries inside single list called messages. 

LLM expect me to give system message which is optional, one or more user message, all previous assistant replies and new user message which is the question that I am asking. This are called ordered list. The list is called `messages`. the role `assistant` is model's previous reply and nothing else. Why ? cuz LLMs don't understand "state" or `memory` on their own. They only send what I send every time. That is it.  Like If I want to remember previous reply then I need to send old conversation in `assistant` role. 

Like this. 
First message:
```js
[
  {"role": "system", "content": "You are friendly."},
  {"role": "user", "content": "What's 2+2?"}
]
```

Second message: Do you see that we are sending the result of first message. Otherwise LLM don't understand what I am asking at all. 
```js
[
  {"role": "system", "content": "You are friendly."},
  {"role": "user", "content": "What's 2+2?"},
  {"role": "assistant", "content": "4"},
  {"role": "user", "content": "Why?"}
]
```

Some LLMs have roles like `tool`, `function`, `developer`(stronger than `user` but weaker than `system`) and many more. that is it. 

### Building the Open AI Website Summarizer
We can build the website summarizer with open AI API key but it only works for server rendered website only. We can use selenium or playwright to do it for client rendered website. as we need browser to work for it. 

### Model
A model is just basically very large function. It just predict the next work based on patterns it learned during training. 

It is just prediction to be honest. Like Model sees a sentence "The cat is sitting on the __ " and tries to guess next word. If wrong, training algorithm adjusts its internal "connections". Repeat millions of times. Eventually, it becomes very good at predicting. Just pattern prediction repeated at a mind blowing scale. 

How does it generate text ? 
Like I send `Explain gravity` to the model. First text -> numbers. It predict the next word "Gravity" then next and then next. It stops when it think your answer is complete.

Like Words are broken into token.

### Types

![[Pasted image 20251116200801.png]]
### Types of Models
LLM means things that generate contents. The best one in the planets are called frontier models. They are created by companies called frontier labs(Big companies, that is it). And when we refer to frontier models, then we are referring to closed sourced models like OPENAI. and there are open source models also called Llama model. We can access it and run ourselves also. 

In Open source we have Llama and sometimes small models are called SLM
Gemma
Phi
Qwen
Deepseek(main version has 671 billion parameters, way too big to run in local computer)
Gpt-oss(20 billion parameters)

Term to know
Distillation

### Ways to use AI
One is popular way. Like with User interface. Like how we use Chatgpt and Claude. We know they calll API but with data which we provide to ChatGPT right ? 


We also have managed service which means there is some service which is running the AI in the cloud and we can call those services. 

And we at last, we can download the open source in our device and run the model and use it. 

![[Pasted image 20251116204835.png]]
You need to look at 15 number video more. 
### Chat Completion API - HTTP Endpoints vs OPEN AI python Client
###### Chat Completion API
It is nothing special to be honest. Nothing special. It is just the wrapper for OPEN AI call that is it
```python
openai = OPENAI()
```
First we used to call OPENAI in post call like how we do for normal POST request. but the response we receive was/is very big. So people made library to do that automatically just like axios just for OPENAI but it was so popular that other corporation also used it(yes that same OPENAI() call). 
like
```
gemini = OPENAI(model="gemini", ) // while it write OPENAI but it is gemini call. 
```

And guess what, we can do that with locally running model also. That is cool. 

### Understanding LLM Types
Base Type(predicting next words when users types), Chat Type and Reasoning/Thinking Type and We also have Hybrid Type also(like Chat and Reasoning combo).

Budget Forcing
S1 thinking

![[Pasted image 20251116214258.png]]
Do you know about o model of chat. that is reasoning model while Chat GPT 4.1 was chat model only. GPT 4.1 is thus much more interactive. 

###### Base Models
They are "raw engine". They are trained on huge amount of text. 
- No extra specialization
- Predict the next token in a sequence. 

Like "I know everything I've read but I don't know how to follow instruction or reason well". They sometimes answer in gibberish because they don't know how to follow instruction consistently. One example is when we write message in our phone, we can see it can gives us autocomplete/predictive text. Before ChatGpt, it was mostly base models all in the internet. Even previous version of  chatGPT also.  

Use Cases:
- Embedding,
- Generating raw text
- Fine-tuning for my purpose
###### Chat/Instruct Models
Base Model fined tune to follow human instructions. Trained with RLHF. They follow prompts, answer politely, stay on topic, avoid unsafe answers. It is extension of base model. After base models. engineer thought, why not train base models more and data structured in one message and response and one message and response and so on. And the idea was that one message should give overall information of the overall chat that will happen. It is called System Prompt.  

Use Case: 
- Chat bots, 
- Answering questions
- Summarizations

###### Reasoning Models
Chat Models that are specialized for reasoning. Trained to think step-by-step. 
Can handle Math problems, logic puzzles, planning tasks

They are good at thinking carefully before answering, even if slower or more expensive. 

Budget Forcing
Some models do “**budget forcing**” — meaning the model is forced to **spend more computation steps** internally to reason carefully, even if it costs more tokens or compute.

Budget forcing is related to adding wait in prompt. It forces the model to think. So. You need to learn more about this. 

Use case:
- Coding assistants
- Math solvers
- Planning agents
- Legal or scientific reasoning

Intuition
- Base model: “I know everything, but don’t ask me to behave”
- Chat model: “I know everything, and I behave politely and follow instructions”
- Reasoning model: “I know everything, behave politely, and I think carefully step by step”
### Limitation of Frontier Models
LLM are trained to be confident. and they do it with so much conviction. They perform best under supervision.   
![[Pasted image 20251117113445.png]]
### Testing ChatGPT-5 and frontier LLMs
You can ask question like "What is Jealousy" and "How many numbers of words are there is in answer to this question". ? Sometimes, you can see that models overthink for simple questions. 
Deep Research(Telling the LLM to go work from my behalf and come back later).
### Agentic AI in Action
Agents = How models take actions with external tools

### Frontier Models Showdown
## Day 4
### Understanding Transformers
So, before Transformers, models struggled with long sequence and RNNs and LSTMs read token by token. They had it hard to remember context far back. Slow to train as everything is sequential. Transformers solved this with attention. The ability to look at all tokens at once and decide what matters.

Like when reading a book. Instead of reading word by word and forgetting the start of chapter. You can instantly jump back and see all the words that matter to understand the meaning. 

Like Every word in the input looks at all other words. assign them a importance and computes a weighted sum to understand context. 

Like 
```
"The cat sat on the mat because it was tired."
```
Attention helps the model know that "it" = "cat", not mat. With attention, the model might forget or confuse pronouns. 

###### Working of Transformers
Step 1: Tokenize
Split text into token(words/subwords)

Step 2: Convert to vectors
Tokens -> Embeddings(number that encode meaning)

Step 3: Attention(Encoder/Decoder)
Each token looks at all other tokens
Learn relationships and produce context-aware vectors. 

 Step 4: Feed forward & Layering
- Multiple layers of attention + processing
- Builds **higher-level understanding**

Step 5: Output
- Generates next token probability
- Can sample a token → next → repeat → text generated

The extraordinary rise of the Transformer. 

![[Pasted image 20251117161436.png]]
It is not the requirement but just a optimization. 

Along the way, we got this
![[Pasted image 20251117162714.png]]Need to watch video 26 again. 

### Parameters


![[Pasted image 20251117165133.png]]

### Token
Token is the smallest piece of text the model understands. Could be word like "cat", "ing" or even "." or ",". If text is lego castle, then token in single lego brick. and models build ideas using these bricks. So For example. 

Text: "I love pizza" then tokens might be `["I", "love", "pizz", "a", "."]`. Tokens are what models "reads" and "builds with". 

Neural Networks were trained at the character level. predict the next character in this sequence. Small vocab but expects too much from network. 
![[Pasted image 20251117170117.png]]
https://platform.openai.com/tokenizer

### Breaking Tokenization : Breaking Words into Tokens

![[Pasted image 20251117170939.png]]

### Understanding the tik token and illusion of memory. 
Everytime we do request to LLM, it is stateless. and we would like to do this when we do it. 
![[Pasted image 20251117172132.png]]

### Context Windows, API Costs, and Token Limits in LLMs
Context Window is maximum amount of tokens that LLM can see at one time. Like short-term memory. Everything we send to the models like system prompt, user input, history, tools, code, documents plus everything the model takes as input. If it doesn't api throw the error or model forgets earlier part of the questions. 

should look at context windows. like we know input is context window but in each prediction that input is again put. Need to understand this phenomenon. 

So Big Context windows for LLM are huge deal. as with Larger Window, an LLM can read entire books, process codebase, ingest PDF, CSVs, logs, maintain long conversation state, perform long reasoning tasks and do RAG with fewer steps. It becomes closer to an agent that can "remember" everything in the tasks. 

Context Window  = how much the model can see now

So larger window = more "memory"
![[Pasted image 20251118090052.png]]
Streming means that typewriter like feeling coming from the chat gpt response. 

## Day 5
