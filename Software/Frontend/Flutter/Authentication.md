## Firebase
If we are using Firebase-CLI then we don't need to do any extra work like setting for android

###### For Android
1. Set up firebase app.

###### For iOS
1. Click in ios.
2. For the name go to `ios/Runner.xcodeproj/project.pbxproj` and search for `PRODUCT_BUNDLE_IDENTIFIER` and paste it to name.
3. After we need to download the `GoogleService-Info.plist` and needs to move to `ios/Runner` and it is setup

###### For Web
1. Give the name.

### MyApp
```
void main() async {

  // ensures that binding with native platforms have been created properly
  WidgetsFlutterBinding.ensureInitialized();

  if (kIsWeb) {
    await Firebase.initializeApp(
        options: const FirebaseOptions(
            apiKey: "AIzaSyCozyYjg1bnoOpVjEcZLPydDV64VfFI838",
            authDomain: "fir-cf4f3.firebaseapp.com",
            projectId: "fir-cf4f3",
            storageBucket: "fir-cf4f3.appspot.com",
            messagingSenderId: "103975159485",
            appId: "1:103975159485:web:a2f7832bba7aeaaed259d4"));
  } else {
    await Firebase.initializeApp();
  }
  runApp(const MyApp());
}
```

#### Auth
Before doing auth, we need to turn email/password in firebase authentication on. then only we can use auth. The big thing is that we put the home to `CheckAuth` Page or component. Then we redirect from there inside `body` of `Scaffold`. 
```
body: StreamBuilder<User?>(
	stream: FirebaseAuth.instance.authStateChanges();
	builder: (context, snapshot) {
		// here we check if user is logged in
		if(snapshot.hasData) {
			// redirect here or return components from here
		} else {
			// redirect here or return components from here
		}
	}
)
```

#### Register, Login and Logout
Inside the on Pressed in any button invoke the `Firebase.instance` which has many methods related to login and logout.
```
await Firebase.instance.signInWithEmailAndPassword();
awiat Firebase.instance.create // complete while coding
```
What if user hits wrong credentials ? use try catch and `FirebaseAuthException`
```
try {
	// login code
} on FirebaseAuthException catch (e) {
	if(e.code == "user-not-found") {
		// display it
	} else if(e.code == "wrond-password) {
		// do whatever or display it
	}
}
```
#### Google Sign-In
Install the package `google_sign_in`. Enable Google in Sign-in method in Firebase Console. Then go to google_sign_in package docs. and copy the required code to android folder. 
Go here: [google_sign_in | Flutter package](https://pub.dev/packages/google_sign_in). 
#### How to get current user
We also have current user in `Firebase.instance`.
