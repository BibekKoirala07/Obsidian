### Ollama
There are lots of free Models
###  System vs User Prompt
### Building the OpenAI Website Summarizer
We can build the website summarizer with open AI API key but it only works for server rendered website only. We can use selenium or playright to do it for client rendered website. 

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


We also have managed service which means there is some service which is running the AI in the cloud and we can call those servies. 

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
Base Type, Chat Type and Reasoning/Thinking Type and We also have Hybrid Type also(like Chat and Reasoning combo)

Budget Forcing
S1 thinking

![[Pasted image 20251116214258.png]]
Do you know about o model of chat. that is reasoning model while Chat GPT 4.1 was chat model only. GPT 4.1 is thus much more interactive  