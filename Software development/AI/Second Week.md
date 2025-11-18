### Day 1
just Connecting to multiple frontier models with APIs like OPENAI, Claude, Gemini. That is it

Training vs Inference time scaling


Testing on Brain Teaser
Not so important. Classic Prisoners dilemma was asked to different models. 

Local Models with Ollama, Native APIs and Open Router integration

Routers and Abstraction Layers
Router is remote provider that sits between the app and AI providers and forward the request to best model or model described. Like `You.com`. If we use Router then, we don't need to install 10 SDKs and also have easy fallback if a provider fails and better cost control and sometimes cheaper price. 

Abstraction Layer
An abstraction layer isn't remote service. It is local software library(code that install) that gives you a unified programming interface even though different LLMs have different APIs.  

Basically write code once -> it works for any LLM. Like using different LLMs with one standard code interface. 

Why are they famous ? Cuz they provides things like
- provider differences
- retries
- timeouts
- chunking long text
- batching
- streaming
- caching
- guards
- memory
- RAG integration
We don't need to manually write all that every time. 
It also hides lot of complexity of RAG pipeline abstractions like
- Splitting documents
- Embedding
- Vector search
- Re-ranking
- Context filtering
- Multi-step query planning

The real world app uses things like 
- RAG
- Document parsing
- Chunking
- Vector DBs
- Agents with tools
- Error handling
- Retries
- Rate limiting
- Multi-provider failover
- Guardrails

Lang chain solves all this so devs don't reinvent the wheel. 

```python
from langchain_openai import ChatOPENAI

llm = ChatOpenAI(model="gpt-5-mini")
response = llm.invoke(tell_a_joke)

display(response.content)
```

But by contrast to Lang Chain, in the opposite end there is the Abstraction Layer called Light LLM
```python
from litellm import completion
response = completion(model="", messages=tell_a_joke)
reply = response.choices[0].message.content
display(reply)
print(f"Input tokens: {response.usage.prompt_tokens}")
print(f"Output tokens: {respnse.}")...
```
Light LLM also let's you connect with everything. It also tells you about input token, output tokens, total token and total costs also. It is quite helpful. We get them in response.

It also has prompt caching. means if we put entire hamlet to AI and do it first time. it will cost huge amount but do it second time and use same context. It will pay less. sometimes even 5* less. 

But there is caveat for OPENAI. The beginning of prompt should match identical ..... look at it later. 

There are implicit and explicit prompt caching. 


IN last day for Week 1, an adversarial conversation between Chatbot. 

### Day 2
We will learn about Gradio(Open source library from Hugging Face. )


responsive API. 