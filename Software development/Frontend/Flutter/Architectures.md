## Clean Architecture
There are 3 layers. 
###### Data Layer
###### Presentation Layer
###### Business Layer

#### Data Layers
It has three parts. They are models, repositories and data sources.
##### Business Layer
It has 3 parts. They are entities, repo and use cases.
#### Presentation Layer
It has three parts. They are pages, providers and widgets.

==Explanation==![[Pasted image 20241022220514.png]]At first we have Pages which contains widgets. As we do some actions (like clicking a button), we trigger the use-cases. The use-cases will use the repositories in business layer with entities and will create a bridge between business and data layer. That's why we have another repository inside the data layers. That repository will use the models. The repository will use the data sources to get the data. Data sources will check for internet. If internet is available, get from remote sources. if not, get from local Storage. And it will get all the data and put it in the providers and pages will reload.
###### Using Linear Flow 
![[Pasted image 20241022223808.png]]
When the button is clicked it will trigger the use cases. the duty of use case is to get the required data. In order to get the data, the use-case will need to pass by the bridge of business layer and go to bridge of data layer Once we use bridges to pass from business to data layer, we can use repository to get data sources. And data will be sent to provider.

