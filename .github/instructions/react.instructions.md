---
applyTo: "**/*.ts,**/*.tsx"
---
# Project coding standards for TypeScript and React

Apply the [general coding guidelines](./general-coding.instructions.md) to all code.

## Component Design
- **Functional Components & Hooks:** Use functional components with hooks (`useState`, `useEffect`, etc.) for all new components.
- **Props:** Define `propTypes` or use TypeScript interfaces for type checking. Keep prop interfaces clear and minimal.
- **Composition:** Favor composition over inheritance. Build complex components from smaller, reusable ones.
- **Single Responsibility:** Each component should have a single, well-defined purpose.

## State Management
- **Local State:** Use `useState` for simple, local component state.
- **Complex State:** Use `useReducer` for more complex state logic within a component.
- **Global State:** Use React Context API for simple global state. For more complex scenarios, consider libraries like Zustand (no Redux please).

## Styling
Ask the user what styling approach they prefer:
- for rapid UI development use **CSS Modules:** Scope styles locally to components to avoid class name collisions.
- Avoid **Styled-Components/Emotion:** Write CSS-in-JS for dynamic and component-scoped styles.
- for advanced styling use **Tailwind CSS:**.

## Performance Optimization
- **`React.memo`:** Memoize functional components to prevent re-renders when props haven't changed.
- **`useCallback` & `useMemo`:** Memoize functions and values to avoid unnecessary re-calculations and re-renders of child components.
- **Code Splitting:** Use `React.lazy` and `Suspense` to split code into smaller chunks and load them on demand.
- **Windowing:** Use libraries like `react-window` or `react-virtualized` for long lists to render only the visible items.

## Data Fetching
- use the fetch API: DO NOT use libraries like Axios for data fetching.
- Use async/await for API calls.
- Encapsulate fetch logic in custom hooks (e.g., useFetchUsers).
- Handle loading and error states explicitly in UI.

## Testing
- **Jest & React Testing Library:** Write unit and integration tests for your components. Focus on testing user behavior rather than implementation details.
- **Mocking:** Use Jest's mocking capabilities to mock API calls, modules, and functions.

## Best Practices
- **Keys:** Always use unique and stable keys for lists of elements. Avoid using array indexes as keys.
- **Event Handling:** Pass event handlers as props (e.g., `onClick`, `onChange`). Use arrow functions in props to avoid unnecessary re-renders.
- **Conditional Rendering:** Use ternary operators, `&&`, or dedicated functions for clean conditional rendering.
- **Folder Structure:** Organize files by feature or component. A common structure is to have a `components` folder with subfolders for each component, including its styles and tests.
- **Error Boundaries:** Use error boundaries to catch JavaScript errors in their child component tree and log those errors.
- **Fragments:** Use `<React.Fragment>` or `<>` to group multiple elements without adding extra nodes to the DOM.
