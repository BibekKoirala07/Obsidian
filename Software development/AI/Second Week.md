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
response API. it enables the things like running browser in remote and running code in remote. But sadly it is paid functionality.  

and one more things to care. When we call model directly then we get wrong information in things like `What is the today's date ?` or things like that. It get exposed. but I may be thinking like `Well, this doesn't happen in ChatGPT` but I remember that ChatGPT is a product not model. We are talking about model. 
### Day 3
Hugging Face is GitHub for AI Models, npm for AI tools and cloud hosting fro AI inference

How LLM Tool calling really works ? 
What does it mean to give the model tools ? It means allowing the LLM to connect to external function to be able to call a function a bit of code. Since LLM get access to extra knowledge, it allows the model to get deeper responses. It could be tool to retrieve something from database or carry out applications, perhaps it can book a ticket or enhance activities or even executing python code or run a test case. All of it are tools that we can equip the model to do. 

So you may be thinking how exactly does it run the code in my computer ? Does OPENAI reach out to my computer and run the code ? Hell no
In theory we think that LLM Decides to run code on my computer but it is not true. It may sound intuitive but that isn't true at all.  
![[Pasted image 20251119121640.png]]
But the above image is false. Why cause LLM just generates token right ? but here we are telling the LLM to use a tool. That can't be true. (in terms of model, not ChatGPT)

In practice this is what happens.
![[Pasted image 20251119121719.png]]
 At first LLM get the request which says I want to do this and this and send the response from LLM comes and it says in order to that, we need to do this. And the code is the thing that goes out, do that thing (python) call and comes back with the answer, and then code call the LLM a second time with answer and say Hey this is the conversation history and I said that and You responded and said, please call a tool. I called the tool and here is the answer to that. Now please answer the question. and based on that conversation history and nothing else, LLM answer the question. 

Write the prompt 
![[Pasted image 20251119141037.png]]
You see it asks for tools rather than giving the answers.

Common Use Case for Tools  
![[Pasted image 20251119141813.png]]

n 