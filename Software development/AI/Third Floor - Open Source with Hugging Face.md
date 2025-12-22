There are two kinds of Hugging Face. One is Hugging Face Platform and Other is Hugging Face Libraries. 

In case of Hugging Face Libraries. 
![[Pasted image 20251222113424.png]]

Other things is about Google Colab. It lets you remote in to a remote box running on Google Cloud. It also allow us to collaborate with others. It also be able to integrate with other Google services. It also helps us to access high end devices. Expensive GPU's are quite expensive but we can rent them in Google Colab. 

![[Pasted image 20251222114323.png]]
Whatever code we are running in Colab is running in Google machine(mostly Tesla T4 machine).

As we know hugging face has two thing. Even in that code one, we have two level of code. There is a lower level code in which we can mess around with tokens, individual calls to models, we are looking at deep neural network that code and it is where the real juicy stuff happens.
But HF has built another level on top of it which gives us simpler interface and helps us do common task hassle free and we don't have to get into so much detail and bits. The higher level on is called pipelines. It is called High level API. The lower level one is about tokenizers and model. It is more of a real granular API where we get to work with the underlying deep neural network. and it gives most power and control. 

![[Pasted image 20251222135108.png]]

![[Pasted image 20251222150606.png]]