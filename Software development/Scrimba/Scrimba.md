### Intro to AI engineering
![[Pasted image 20251126194630.png]]

The LLM is the type of algorithm that uses training data  to recognize patterns and make predictions or decisions. 
```js
import OpenAI from 'openai'

const openai = new OpenAI({
	dangerouslyAllowBrowser: true
})

const messages = [
{
role: 'system',
content: 'You are a helpful assistant that explains things in language a 10-year-old can understand. Your answers are always less than 100 words.'
},
{
role: 'user',
content: 'What is Quantum Computing?'
}
]

const response = await openai.chat.completions.create({
model: 'gpt-4',
messages: messages,
max_tokens: 16 // default: inf
})
```

temperature means what does it means to be creative(0 to 2).

Few shot approach. It just means providing one or more examples in user or system object. That is it. It is more expensive and less performance. but we do get the output exactly we.

Stop sequence. We can use max token for that or tell model to stop once you see certain pattern. 

frequency and presence penalties. 
High presence penalties means talking about same topic over one chat while low presence penalties means talking about different topic. High frequency penalties means decreasing the probability of using same words or phrase in one chat. opposite for low frequency penalties.  

Fine tuning
Just giving a standard, pre-trained model a specific data set to enhance its performance on a particular tasks. 
Why would we need fine tuning. We can save money by decreasing the prompt token size by giving the model data we may need to give in prompt. It saves token. We can teach the model how it can respond. We can make the AI to return certain format every time. We can reduce the cost by fine-tuning the cheap model and fine-tuning it instead of expensive model. However fine-tuning should be used as last resort. First try good prompt and use few shot approach. It is quite tedious to do it. We need plenty of data. We need at least about 50 data for fine-tuning. write in ` openai.files.create({ purpose: "", file: ... }) `

for image, we get blob from Dalle E. 
### Deployment
We need to create Cloudflare workers to handle API request securely. shift API request from client side to server side. and set up cloudflare AI gateway to make the app robust.  

### Open source Models


### Embeddings and Vector Database


### Agents


### Context Engineering

### Vercel AI SDK

### MCP

### Multi-Modality




Agents can pulls multiple tools and work on a tasks until the tasks is complete.  On simple level it means using LLM to gather real time information and loop over a task, solving one piece at a time. On complex terms it could be driving the autonomous car and driving it until the car drives the destination. 

It just means AI can take action on its own. Digital assistant that can do work. 

The term ReAct = Reasoning and Act which is main theme of Agentic AI. 

It does its thing in three steps
In first step, we use LLM to get the logic or make. Not to generate content like normal way. 
In second step, we write the code in such a way such that LLM can perform the logic/action that it made. At first LLM will tell what function it want to call 

![[Pasted image 20251122205145.png]]
![[Pasted image 20251122205350.png]]
