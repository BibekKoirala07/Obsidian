###### Error boundaries
- React components that catch JavaScript errors in child components
- Use static getDerivedStateFromError() and componentDidCatch() lifecycle methods
- Not available in functional components without libraries
- Example implementations include react-error-boundary library
- Key interview questions: implementing custom error boundaries, handling different error types
###### Higher Ordered Components
- Functions that take a component and return an enhanced component
- Used for code reuse, logic abstraction, and cross-cutting concerns
- Common patterns: withRouter, withAuth, withTheme
- Understand composition of multiple HOCs and the order of application
- Compare with Render Props and Custom Hooks patterns
###### Suspense
- React feature for handling asynchronous operations
- Works with React.lazy() for code splitting
- Future API for data fetching with suspense resources
- Understand fallback props and suspense boundaries
- New concurrent features like SuspenseList and useTransition
###### Portals
- ReactDOM.createPortal() API for rendering outside component hierarchy
- Use cases: modals, tooltips, dropdowns that avoid z-index/overflow issues
- Event bubbling still follows React component tree, not DOM structure
- Accessibility considerations when using portals
- Managing portal lifecycle and state synchronization
###### Fragments
- Syntax: <React.Fragment> or shorthand <>...</>
- Key prop usage in fragment lists
- Performance benefits vs container divs
- Implementation details in different frameworks
###### Directives
- Angular: Structural (@NgIf, @NgFor) vs attribute directives
- Vue: v-if, v-for, v-bind, v-model, custom directives
- Solid.js: special $ directives and JSX control flow
- Custom directive implementation patterns
###### CSR and SSR vs ISR vs SSG vs Progressive Rendering
Streaming SSR and On Demand ISR and normal ISR
###### Hydration/ Hybrid Hydration
- Process of attaching event listeners to server-rendered HTML
- Hydration mismatch errors and prevention strategies
- Islands architecture and partial hydration
- Progressive enhancement techniques
- Next.js, Astro, and other framework approaches
###### Async Rendering
- Loading states and error handling patterns
- Suspense data fetching
- Using async/await in rendering patterns
- Race conditions prevention
- Data loading waterfall prevention
###### Concurrent Rendering
- React's non-blocking rendering model
- useTransition and useDeferredValue hooks
- Prioritizing UI updates
- Time slicing and scheduling
- Handling interruptions in rendering
###### Implicit Delegation
- Spreading props to child components
- Component composition patterns
- Prop drilling vs context
- TypeScript prop forwarding with Omit, Pick
- Performance implications
###### Advanced Hooks
Custom hook design patterns
###### Performance optimization
React profiler API usage
Virtualization
Web workers for CPU-intensive tasks
###### State management patters
- Redux patterns: selectors, normalized state, slice pattern
- Context API optimization
- Zustand, Jotai, Recoil patterns
- State machines with XState
- Server state with React Query/SWR
###### SEO
##### FCP, TTI, LCP metrics
###### Axios
###### Memoize
###### Architecture pattern
Component composition vs inheritance
Render props pattern
Atomic design principles
Feature-based vs component-based organization
###### Module Federation
Version management strategies
Shared dependencies management
Webpack module federation



