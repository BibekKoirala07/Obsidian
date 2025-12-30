### Rendering Pattern
When react renders, it renders from parent to child, applies changed to real DOM, and then runs side Effect. child's side effect is run first rather than parent. React ensures that child should be fully ready so that parent run their effects. React does that so that if parent relies on something the child had set up in its effect, it is already done.

Render are top-down.
Effects are bottom-up.
### [[Hooks]]
### [[State Management - Data Fetching]]
### [[Tips]]
### [[React Patterns]]

### React Concepts
###### Context
###### Suspense
###### Error Boundaries
###### Fragments
###### Portals
###### Implicit Delegation
###### Streaming
###### Directives
###### Lazy Loading
Do everything lazy load if user don't need it
###### Async & Concurrent Rendering

###### Higher Order Component
###### Pure function/Impure function
###### virtualization
###### DOM Reconciliation
###### Strict Mode
###### Component life cycle
###### Tree shaking
###### How to you handle big data in react ?
###### Virtual Dom
###### Hooks System
###### React Lifecycle methods
###### Flux concept
###### Synthetic Events

### React Patterns
