 Know inference time technique
 ![[Pasted image 20251223171715.png]]
 ![[Pasted image 20260203195348.png]]
 
 
Auto regressive LLM 

![[Pasted image 20260118161521.png]]
![[Pasted image 20260118161933.png]]

![[Pasted image 20260118162754.png]] That encoder(Auto encoding) is no way related to LLM (which is auto regressing LLM)

RAG is actually a Hack and it comes with lot of sets of Hack. 

![[Pasted image 20260118163414.png]]Langchain is heavy now. It even has its own language. (It wasn't such in before). Langchain has two version. pre v1 and post v1. 


![[Pasted image 20260118164253.png]]


![[Pasted image 20260118164635.png]]

we human can't visualize multiple dimension . Luckiy mathematician have come to our rescue. There are few technique. There is one called PCA, there is one called t-SNE. T-Distributed Stochastic neighbor embeddings. It is very popular technique for taking higher dimensional data and projecting it down. Like things that are likely to be closer in 2D dimension are likely to be closer in multi dimensional space also. It is a staticcal technique. t-Sne can also do 3D representation also. 

![[Pasted image 20260203205915.png]]
If you call invoke, it is called Runnables. In previous version, Runnalbes wasn something that was very much surfaced. But in new version, it is taken out of as sort of interface 

retriever do RAG look up easily without any hassle

==At inference time, when the LLM is generating each token by token but LLM don't work like that. The main thing is how you pick which token comes next. A model actually don't spit out the next token as you think, what is actually spits out is the probability of all the possible next tokens. . Every posible next words get some probability, even if it is very small probablility. But when we say pick the next token then we say, pick the token that go the highest probability, the one that model said is the most likely next token.  with temp of 0, all we do is pick whichever token got the highest probability and we do it every single time. High temp means pick token which may be sometimes second or third. But this frequency happens very low times(Not as much as I would had guess as it would had happen on the basis of answer that I got when I put high temp)==.

In OPENAI(with temp 1), if the token we given a 10% probability, then you would pick that 110% of the time you actually sample what token comes next basid on the probabilites that they got. So if the token got 1 percent probablity then 1 percent chance. still 1 in 100 times. 

If you want the model to be creative, the right way is from system prompt. not temperature. 

How to manages messages history in the array that we might send more than one time in let's say chatbot ? 
Some times when appending the AI message might take very huge amount of context window. One solution is just to append user questions and that is it. It is extremley intiuitve and makes our work easier but this is very bad as it might lose context like `Who is her and things like that`. 

There are so many problem. Let's say John Max has HR record and it is quite big. Somewhere in some paragraph(chunk). It is written "John won this award". And when you ask "Who won this award". The AI will respond with only first name and not surname(as chunking removed the context).  and what if that entire chunk as pronoun as "her"

Another problem is when you ask so many question. then all the question is added to message list right ? then when you ask question then that question looses its strength and power. So this is quite a big problem. context for that question gets lost. 

Another problem is question like "How many employee has salary more than 60,000"

You have to look at 120 number video more than 1 time. 

![[Pasted image 20260203215434.png]]

Now onto evaluations. 

==We can make LLM evaluate its own answer also. it is a big thing. ==

Always make best possible dataset.
How good are you at retrieving. There are bunch of metric. MRR is one thing. 

Recall@K (eg. Recall@3)  is top 3 chunk has how much relevant context. 

![[Pasted image 20260204095604.png]]

What we really call about is Recall. 

==jsonl is file with each line being python dict==. These things are very common

MRI and keyword_coverage. 

Advanced RaG Technique. 
One things to remember is that it is all pseudoscience. 

Graph RAG. 
Graph Database aren't that much needed as much as they are advertized.

![[Pasted image 20260204113533.png]]

Many people say RAG is dead. It is cuz of two reason. One being The context window are huge today and another one is becuase of Agentic RAG

Query Expansion and Multiprocessing. 
semantic chunking