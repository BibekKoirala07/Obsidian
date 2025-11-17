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
Base Type(predicting next words when users types), Chat Type and Reasoning/Thinking Type and We also have Hybrid Type also(like Chat and Reasoning combo)

Budget Forcing
S1 thinking

![[Pasted image 20251116214258.png]]
Do you know about o model of chat. that is reasoning model while Chat GPT 4.1 was chat model only. GPT 4.1 is thus much more interactive. 
### Limitation of Frontier Models
LLM are trained to be confident. and they do it with so much conviction. They perform best under supervision.   
![[Pasted image 20251117113445.png]]

### Testing ChatGPT-5 and frontier LLMs
You can ask question like "What is Jealousy" and "How many numbers of words are there is in answer to this question". ? Sometimes, you can see that models overthink for simple questions. 
Deep Research(Telling the LLM to go work from my behalf and come back later).
### Agentic AI in Action

### Frontier Models Showdown

## Day 4
### Understanding Transformers
Generative Pre-trained Transformers
The extraordinary rise of the Transformer. 

![[Pasted image 20251117161436.png]]
It is not the requirement but just a optimization. 

Before the transformer, it was LSTM

Along the way, we got this
![[Pasted image 20251117162714.png]]Need to watch video 26 again. 

### Parameters


![[Pasted image 20251117165133.png]]

### Token
Neural Networks were trained at the character level. predict the next character in this sequence. Small vocab but expects too much from network. 
![[Pasted image 20251117170117.png]]
https://platform.openai.com/tokenizer

### Breaking Tokenization : Breaking Words into Tokens

![[Pasted image 20251117170939.png]]

### Understanding the tik token and illusion of memory. 
Everytime we do request to LLM, it is stateless. and we would like to do this when we do it. 
![[Pasted image 20251117172132.png]]

### Context Windows, API Costs, and Token Limits in LLMs
