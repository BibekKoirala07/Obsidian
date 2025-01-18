Tips: 
1. Use `react-native-permissions` for permission management. It is tedious to do it without library
2. For push notification, use `firebase-cloud-messaging` to solve the issue.
3. Gestures like pinch and swipe may not work properly in ios so use `react-native-gesture-handler`.
4. Background fetching is more stricter in ios so use libraries like `react-native-background-fetch` or `react-native-background-task`.
5. use `react-navigation`, `react-native-paper` and `react-native-vector-icons`. making them normally would be pain in the ass.
#### View/Text and Image
Use libraries like `react-native-fast-image` for compression for image.
```
<View
  // Styling
  style={} // Style the container (e.g., flex, margins, padding, background color)

  // Layout
  pointerEvents={} // Controls if this view should block touches or pass them through (e.g., "auto", "none", "box-none")

  // Accessibility
  accessible={} // Marks the view as accessible for screen readers
  accessibilityLabel={} // Label for screen readers
  accessibilityRole={} // Describes the role of the component (e.g., "button", "header")
  accessibilityState={} // Indicates the state of the component (e.g., disabled, selected)
  accessibilityHint={} // Additional hint for the component’s action
/>

<Text
  // Styling
  style={} // Style the text (e.g., font size, color, weight, alignment)

  // Text Behavior
  numberOfLines={} // Limits the text to a specified number of lines (e.g., 1 for single-line text)
  ellipsizeMode={} // How text is truncated when it overflows (e.g., "tail", "head", "middle", "clip")
  selectable={} // Makes text selectable if true
  adjustsFontSizeToFit={} // Adjusts the font size to fit within the container
  minimumFontScale={} // Sets the smallest font size allowed when `adjustsFontSizeToFit` is true

  // Accessibility
  accessible={} // Marks the text as accessible for screen readers
  accessibilityLabel={} // Label for screen readers
  accessibilityRole={} // Describes the role of the text (e.g., "header", "link")
  testID={} // Identifier for testing purposes
/>

<Image
  // Source
  source={} // Defines the image source (local or remote)
  source={{ uri: url }}

  // Styling
  style={} // Style the image (e.g., width, height, border radius)

  // Performance
  resizeMode={} // How the image fits within its container (e.g., "cover", "contain", "stretch", "repeat", "center")
  defaultSource={} // Placeholder image while the main image loads (iOS only)
  blurRadius={} // Adds a blur effect to the image (in pixels)

  // Accessibility
  accessible={} // Marks the image as accessible for screen readers
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>
```
#### Keyboard Avoiding View
```
<KeyboardAvoidingView
  // Behavior
  behavior={} // Determines how the view behaves when the keyboard appears (e.g., "height", "position", "padding")
  keyboardVerticalOffset={} // Adjusts the vertical offset for the keyboard (used to avoid overlapping)

  // Styling
  style={} // Styles the view container
/>

```
#### Text Input
```
<TextInput
  // Styling
  style={} // Style the input field (e.g., width, border, padding, font)

  // Text Behavior
  value={} // The current text value
  onChangeText={} // Callback triggered when the text changes
  placeholder={} // Placeholder text displayed when input is empty
  placeholderTextColor={} // Color of the placeholder text
  secureTextEntry={} // Masks text for password fields
  multiline={} // Allows multi-line input if true
  keyboardType={} // Configures the keyboard type (e.g., "default", "email-address", "numeric")
  returnKeyType={} // Controls the return key type (e.g., "done", "go", "next")
  maxLength={} // Limits the number of characters allowed
  editable={} // Enables or disables the input

  // Accessibility
  accessible={} // Marks the input as accessible for screen readers
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>

```
#### Pressable/Touchable Opacity/Buttons
Don't use button if excessive styling is required. Infact, don't use it at all.
```
<Pressable
  // Interactivity
  onPress={} // Callback triggered on press
  onLongPress={} // Callback triggered on long press
  onPressIn={} // Callback triggered when the press starts
  onPressOut={} // Callback triggered when the press ends

  // Styling
  style={} // Accepts styles or dynamic styles via a function (e.g., `style={({ pressed }) => {}}`)

  // Feedback
  android_ripple={} // Adds a ripple effect on Android
  delayLongPress={} // Delay (ms) before triggering onLongPress
  delayPressIn={} // Delay (ms) before triggering onPressIn
  delayPressOut={} // Delay (ms) before triggering onPressOut

  // Accessibility
  accessibilityLabel={} // Label for screen readers
  accessibilityRole={} // Describes the role of the component (e.g., "button")
  testID={} // Identifier for testing purposes
/>

<TouchableOpacity
  // Interactivity
  onPress={} // Callback triggered when the component is pressed
  onLongPress={} // Callback triggered when the component is long-pressed
  activeOpacity={} // Opacity level when the component is pressed (0 to 1)

  // Styling
  style={} // Style the touchable container

  // Accessibility
  accessible={} // Marks the button as accessible for screen readers
  accessibilityLabel={} // Label for screen readers
  accessibilityRole={} // Role of the component (e.g., "button")
  testID={} // Identifier for testing purposes
/>


<Button
  // Interactivity
  onPress={} // Callback triggered when the button is pressed
  title={} // The text displayed on the button
  disabled={} // Disables the button if true

  // Styling
  color={} // Changes the color of the button text or background (platform-specific)

  // Accessibility
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>
```
#### Flat List/Section List/
There are alternative like `ScrollView` but but don't use it
```
<FlatList
  // Styling
  style={} // Style applied to the FlatList container (outer container)
  contentContainerStyle={} // Style applied to the inner container where the items are rendered

  // Item Spacing
  ItemSeparatorComponent={} // A component to render as a separator between items
  columnWrapperStyle={} // Style for rows when using numColumns for grid layout
  inverted={} // Reverses the order of the items in the list

  // Scroll Behavior
  horizontal={} // Enables horizontal scrolling if set to true
  showsVerticalScrollIndicator={} // Displays or hides the vertical scroll bar
  showsHorizontalScrollIndicator={} // Displays or hides the horizontal scroll bar
  scrollEnabled={} // Enables or disables scrolling interaction
  scrollEventThrottle={} // Sets the rate at which the onScroll event is triggered (in milliseconds)
  decelerationRate={} // Controls how fast the list stops scrolling when user lifts their finger
  snapToInterval={} // Specifies the distance between snap points for inertial scrolling
  snapToAlignment={} // Aligns snapped items to the start, center, or end of the scroll view
  pagingEnabled={} // Scrolls one page at a time when set to true
  bounces={} // Enables or disables the bounce effect at scroll boundaries
  onScroll={} // Function triggered on scrolling

  // Data and Rendering
  data={} // The data array to render items from
  renderItem={} // A function to render each item in the data array
  keyExtractor={} // Function to uniquely identify each item for rendering
  numColumns={} // Number of columns for grid-style layout
  getItemLayout={} // Optimizes performance by precomputing item sizes

  // Header, Footer, and Empty States
  ListHeaderComponent={} // A component to display above the list of items
  ListFooterComponent={} // A component to display below the list of items
  ListEmptyComponent={} // A component to display if the list is empty

  // Load More
  onEndReached={} // Function triggered when reaching the end of the list
  onEndReachedThreshold={} // Distance from the end (in fractional height) to trigger onEndReached

  // Pull-to-Refresh
  refreshing={} // A boolean to show the pull-to-refresh loading indicator
  onRefresh={} // Function triggered when the list is pulled to refresh

  // Performance
  initialNumToRender={} // Number of items to render initially (optimizes load performance)
  maxToRenderPerBatch={} // Maximum number of items to render per batch
  windowSize={} // Number of items to render outside of the visible area for smoother scrolling
  removeClippedSubviews={} // Improves performance by removing items outside the viewport
  updateCellsBatchingPeriod={} // Time interval (ms) for batch updates during scroll rendering
  extraData={} // Additional data that forces FlatList to re-render if it changes

  // Accessibility
  accessibilityLabel={} // An accessibility label for the FlatList
  keyboardShouldPersistTaps={} // Determines how taps are handled when the keyboard is open
/>

<SectionList
  // Data
  sections={} // The array of sections to render (each section contains `data` and `title` or other properties)
  keyExtractor={} // Unique key for each item
  renderItem={} // Renders each item in the list
  renderSectionHeader={} // Renders the header for each section

  // Styling
  style={} // Styles the list container
  contentContainerStyle={} // Styles the inner content

  // Behavior
  onRefresh={} // Callback for pull-to-refresh functionality
  refreshing={} // Boolean to control the refresh state
  showsVerticalScrollIndicator={} // Shows/hides the vertical scroll indicator

  // Accessibility
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>
```
#### Modal
```
<Modal
  // Visibility
  visible={} // Boolean to control if the modal is shown

  // Behavior
  animationType={} // Animation for modal appearance (e.g., "none", "slide", "fade")
  transparent={} // Makes the modal background transparent if true
  onRequestClose={} // Callback triggered when the user tries to close the modal (useful for Android hardware back button)

  // Styling
  style={} // Applies styles to the modal container (custom wrappers are often used)

  // Accessibility
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>
```
#### Safe Area
Use `react-native-safe-area-context`to emulate like ios.
```
<SafeAreaView
  // Styling
  style={} // Styles the safe area view

  // Accessibility
  accessibilityLabel={} // Label for screen readers
  testID={} // Identifier for testing purposes
/>
```
#### ActivityIndicator
```
<ActivityIndicator
  // Behavior
  animating={} // Controls if the spinner is visible
  size={} // Size of the spinner (e.g., "small", "large" or custom number)
  color={} // Color of the spinner

  // Styling
  style={} // Styles the spinner container
/>

```
