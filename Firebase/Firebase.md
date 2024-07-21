# Start a Firebase Project
### Install firebase

`npm install firebase`

### Initialize firebase

```
import { initializeApp } from 'firebase/app';  
  
// TODO: Replace the following with your app's [Firebase project configuration]

(https://firebase.google.com/docs/web/learn-more?authuser=0#config-object)  

const firebaseConfig = {  // firebase config object. It is unique but non-secret 
 apiKey: **"API_KEY"**, 
 
 authDomain: **"PROJECT_ID.firebaseapp.com"**,
  
     (https://firebase.google.com/docs/projects/locations?  authuser=0#rtdb-locations)  
 databaseURL: **"https://DATABASE_NAME.firebaseio.com"**, // The value of `databaseURL`      depends on the location of the database 
 
 projectId: **"PROJECT_ID"**,  
 
 storageBucket: **"PROJECT_ID.appspot.com"**,
   
 messagingSenderId: **"SENDER_ID"**,  
 
 appId: ""APP_ID"",** optional **  // For Firebase JavaScript SDK v7.20.0 and later,
    measurementId is an optional field  measurementId: **"G-MEASUREMENT_ID"**,
    
};  
  
const app = initializeApp(firebaseConfig);
```

**After Initialization, we can start using firebase services**
A Firebase App is a container-like object that stores common configuration and shares authentication across Firebase services. After you initialize a Firebase App object in your code, you can add and start using Firebase services.


### Authentication

#### For easy Solution
- Use FireBaseUI Auth

#### I want full control over sign-in experience

- Firebase **User** objects represent the use account signed up for the project. apps usually have many registered users, and every app in a project shares a user database. User instances are independent from Firebase Authentication instances, so you can have several references to different users within the same context and still call any of their methods.
- Firebase users have a fixed set of basic properties—a unique ID, a primary email address, a name and a photo URL-stored in the project's user database, that can be updated by the user. If we need additional user properties then, you can store it in any other services like Google Cloud Firestore

#### Different Sign-In Providers
- Email and password
- federated identity
- custom authentication


#### [[Sign in with Google]]



### Using FireStore

- Used to store structed data, such as user profile
- Build real-time applications, where data is updated and displayed in real time.
- Scale your application to support a large number of users and devices.

```
import {getFirestore} from 'firebase/firestore'

const firestore = getFirestore(app)
```


### Using CloudStorage

- Store images, videos, and audio files.
- Deliver files to users with high availability and performance.
- Serve static and dynamic content for your website or app.

```
import {getStorage} from 'firebase/storage'

const storage = getStorage(app)
```

# [[For JavaScript]]

# [[Node.js]]
