The right question is which model solves your problem.  At first we need to understand the need/requirement of ours. There are two way of judging the LLMs. First one is just basic information about model(like how many parameters does it have, costs, license, context window and things like that) while second one is comparing the benchmarks(published metric in which the models are assessed against different things like how good are they at coding, how good are they at understanding language) of the model. 

In basics, We look whether the model is Open-sourced or Closed.
![[Pasted image 20251222172208.png]]
We can get all this information by looking at model card which is provided by every provider like OPENAI publishes with its model. There are also the leader board which lay out this information so that we can compare it. There are also some other factor that should be taken in account when choosing 
![[Pasted image 20251222172929.png]]

The Chinchilla Scaling Law
The idea is years old and it was published by Google. but it seems like idea isn't as simple as it was previously thought. The name Chinchilla is there cuz their model at the time was called Chinchilla. It was the model that came after another model called gopher. The main idea was how do you scale models ? How do we go from 8 billion parameter to 16 billion parameters and get twice the performance from it ? Their discovery was that we need to scale the training data in proportional way. Like if double the number of parameter then requirements would be double the training token that was used. If you are getting diminishing return from training even with more training data, then it is the sign that you need to increase the number of parameters. Same with vice versa, If you are doubling the number of weights, then this law suggest we need twice the data also. This rule still helps but it is less emphasized nowadays. Particularly for two reasons. One reason is that we have found how to squeeze more and more information into a model without increasing the number of parameters. Like using better training techniques through filtering out of what they call pruning the number of weights in the model and through better architecture. Another reason is now inference time techniques have become so prominent this idea that getting better performance from a model isn't just about what we do from training time, it's also so much what we do at inference time. More reasoning using techniques like rag. Less emphasis on training data for performance. So people talk less about chinchilla scaling   law.

After doing all the basic, we have to move to benchmarks now. 
This are the benchmarks test that is done but there are other benchmarks tests also. ![[Pasted image 20251223095101.png]]
But the problem is that benchmark should be taken seriously and with grain of salt also. the problem ? There is a thing called training data contamination like future generation of benchmarks test have the info about testing data of previous generation.

There is a problem called Alignment problem also. Like the problem that LLMs know that they are being evaluated and they just follow the instruction. It is a big problem. 
![[Pasted image 20251223111954.png]]

Look at connect four challenge from ed-donner challenge.

![[Pasted image 20251223113403.png]]

I need to look deep into inference time training.

We need to look at LM Arena also(formerly LMSYS) also. 

Commercial Use Cases
Automate, then Augment then Differentiate

![[Pasted image 20251223122148.png]]

![[Pasted image 20251223142639.png]]

![[Pasted image 20251223150339.png]]

