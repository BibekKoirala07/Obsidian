## State Management
##### Providers
It should be in `/providers` folder inside `lib` folder
Steps
1. First, we create a provider Class inside lib in `providers` folder with appropriate name. 
2. Second we should wrap the `myApp` with `ChangeNotifierProvider` but if we have multiple provider we need `MultiProvider`.
3. To read the value of provider we need to write this line of code 
```
child: Text(context.watch<>Name of the provider).username // username is inside the provider
```
4. To write the value we need to write this code.
```
onTap or onPressed: () {
	context.read<ProviderName>().function(arguments)
}
```

> [!NOTE] Don't write the code
> Just type give me boilerplate for provider state management and it will do all the things
##### Bloc State Management
We need to add `flutter_bloc` package . We don't need to add `bloc` at all. Why ? cuz `flutter_bloc` already has `bloc` already. After that we need to add `BlocProvider` in `MaterialApp` inside `home`. If there are more than one Bloc states, use `MultiBlocProvider`. I will not write code here and will suggest to use AI. 
![[Pasted image 20241013165512.png]]When Button is clicked in `counter_page.dart`, it triggers a event. It then goes to Bloc Machine. Then, that machine releases the new state and it rebuild the widgets that are affected by those state.

> [!NOTE] Important
> Event, Bloc and State is always one thing together. nothing stands alone. They are always connected.

So we need to create a folder named `bloc` which will have three files. They are 
1. `counter_state.dart`
2. `counter_enent.dart`
3. `counter_bloc.dart`
What will happen ?
At first event is detected. Like what event did user pressed ? after getting that, the bloc mutates the states and returns the new state which triggers state rebuilding.
**`counter_state.dart`**
```
Class CounterState {
	final int num;

	CounterState(this.num);
}
```
`counter_event.dart`
```
	abstract class CouunterEvent {}

	class CounterIncrementPressed extends CounterEvent {}

	class CounterDecrementPressed extends CounterEvent {}
```
`counter_bloc.dart`
It takes two things. the event and the state. We also have default value. Here we have two event. They are CounterIncrementPressed and CounterDecrementPressed. 
```
Class CounterBloc extends Bloc<CounterEvent, CounterState> {
	CounterBloc(): super(CounterState(0)) {
		on<CounterIncrementPressed>((event, emit) {
			emit(CounterState(state.count + 1)); 
		})
		on<CounterDecrementPressed>((event, emit) {
			emit(CounterState(state.count - 1)); 
		})
	}
}
```

How to read and write the value? 
```
@override 
Widget build(BuildContext context) {
	final counterBloc = context.read<CounterBloc>();

	// inside the widget I want to use
	child: BlocBuikder<CounterBloc, CounterState> {
		builder: (context, state) {
			// here we can use to read or write
			print(state.count);

			// here is how to write
			Button(
				onPressed: () {
					// adding a event to a button
					counterBloc.add(CounterDecrementPressed());
				}
			)
		}
	}
}
```

But what if there is more than one actions? like there may be sigin data, any other actions. In that case we can do like 
```
lib/
│
├── actions/
│   ├── bloc/
│   │   ├── counter_bloc.dart
│   │   ├── counter_state.dart
│   │   └── counter_event.dart
│   └── pages/
│
├── actions_2/
│   ├── bloc/
│   └── pages/
│
└── main.dart
```
##### Cubit
It is a bloc architecture with less boiler plate. It removes the need for events like in `bloc`

##### River pod
