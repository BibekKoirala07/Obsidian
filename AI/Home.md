AI is the technique to program a computer to behave as intelligent being. While traditional computer or programming works in this way. AI is the technique of programming a computer to respond to data in the same way an intelligent being does. It can be thought of as a simulation of how an intelligent being reacts to, processes, and understands the data.

#### Traditional Programming
We have to formulate the explicit rules for it to follow

![[Pasted image 20240721100941.png]]

#### AI
Instead in AI, we come up with lots of example and computer learn from it like human learn. For example, if we try to cut some vegetable, we don't learn the rules to cut vegetable. Rather we just try and adjust and find the best way to cut vegetable, i.e. learning through real example and perception. It is same with AI. We give them example or dataset to distinguish between good and wrong move and computer learn. When example is provided, it chants through all the example and learn the patterns and turn them into ==models== that could be used to understand future pattern. e.g.. Like in chess game, next move is founded in this way

![[Pasted image 20240721101750.png]]

![[Pasted image 20240721102116.png]]

 This kind of pattern recognition can be incredibly  where it is very hard to find the rules of the pattern. like for e.g. in retina scan, fingerprint scan

> [!warning] Overfitting
> It is a condition in which we provide less amount or species of data than required. Like if we were to train a model which can detect computers and we give data of just laptops, then it may not be able to detect PCs. When overfitting happens, the model does not perform well when given an example that it has not seen during the training process.

#### How does Models works
At first, divide the data into class so that it can be used. like "Class 1", "Class 2" and many more. e.g. "Rock", "Paper", "Scissors". Upload a data to each class. Train the model. Test it. One of the Model Generator is [Teachable Machine](https://teachablemachine.withgoogle.com/)

#### Application of AI: Computer Vision
![[Pasted image 20240721160903.png]]
Computer Vision is one of the key application of AI, in which we can train the machines to identify the content of images. It can be used to extract the health condition of retina as shown above. At first large amount of data is provided. The application then attempts to extract features from the image using **filters**. During the training process, the application learns the best filters that change images such that the **features** that differentiate healthy and diseased retinas emerge. Now it can diagnose unseen retina image. The job of programmer is to make the program to handle all of those filter and algorithm for learning them.