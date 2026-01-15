---
description: Create a new React component with TypeScript and modern best practices
model: claude-sonnet-4-5
---

Generate a new React component following 2025 best practices for .NET 10 + React applications.

## Component Specification

$ARGUMENTS

## Modern React + TypeScript Standards

### 1. **Function Components Only**
- Use function components (not class components)
- React 19 patterns
- Hooks-based architecture

### 2. **TypeScript Best Practices**
- Strict typing (`strict: true`)
- Interface for props
- Proper TypeScript utility types (ComponentProps, ReactNode, etc.)
- NO `any` types
- Explicit return types for complex components
- Nullable reference types alignment with .NET backend

### 3. **Component Pattern**

```typescript
import { FC } from 'react'

interface Props {
  // typed props matching .NET DTOs
}

export const Component: FC<Props> = ({ prop1, prop2 }) => {
  // implementation
  return <div>{/* JSX */}</div>
}
```

### 4. **State Management**
- `useState` for local state
- `useReducer` for complex state
- React Context for theme/auth/global state
- Consider Zustand or Redux Toolkit for large apps
- TanStack Query (React Query) for server state

### 5. **API Integration with .NET Backend**
- Fetch data from ASP.NET Core Web API
- Use TanStack Query for data fetching and caching
- TypeScript types matching .NET DTOs
- Handle ProblemDetails error responses

```typescript
import { useQuery } from '@tanstack/react-query'

const { data, isLoading, error } = useQuery({
  queryKey: ['items'],
  queryFn: async () => {
    const response = await fetch('/api/items')
    if (!response.ok) throw await response.json()
    return response.json() as ItemDto[]
  }
})
```

### 6. **Performance**
- Lazy loading with `React.lazy()`
- Code splitting with dynamic imports
- `useMemo()` for expensive computations
- `useCallback()` for callback functions
- Virtualization for long lists (react-window)

### 7. **Styling Approach** (choose based on project)
- **Tailwind CSS** - Utility-first (recommended)
- **CSS Modules** - Scoped styles
- **Styled Components** - CSS-in-JS
- **MUI/Ant Design** - Component libraries

## What to Generate

1. **Component File** - Main component with TypeScript
2. **Props Interface** - Fully typed props (matching .NET DTOs if applicable)
3. **Styles** - Tailwind classes, CSS module, or styled-components
4. **Example Usage** - How to import and use
5. **API Integration** - If component fetches data from .NET backend

## Code Quality Standards

**Structure**
- Feature-based folder organization (`features/`, `components/shared/`)
- Co-locate related files (component, styles, tests)
- Barrel exports (index.ts)
- Clear file naming conventions

**TypeScript**
- Explicit prop types via interface
- Proper generics where needed
- Utility types (Pick, Omit, Partial, Required)
- Discriminated unions for variants
- Types matching .NET backend DTOs

**Props**
- Required vs optional props
- Default values where appropriate
- Destructure in function signature
- Props spread carefully
- C# naming conventions -> camelCase in React

**Accessibility**
- Semantic HTML
- ARIA labels where needed
- Keyboard navigation
- Screen reader friendly
- Focus management

**Best Practices**
- Single Responsibility Principle
- Composition over inheritance
- Extract complex logic to custom hooks
- Keep components small (<200 lines)
- Separate business logic from presentation

## Component Types to Consider

**Presentational Components**
- Pure UI rendering
- No business logic or API calls
- Receive data via props
- Easy to test

**Container Components**
- Data fetching from .NET API
- Business logic
- State management
- Pass data to presentational components

**Form Components**
- Form validation with React Hook Form or Formik
- Submit to .NET Web API endpoints
- Handle validation errors from ProblemDetails
- Optimistic UI updates

## React 19 Features to Use

- **use()** API for reading promises/context
- **useActionState()** for form state
- **useFormStatus()** for form pending state
- **useOptimistic()** for optimistic UI updates
- **React Compiler** (automatic optimization)

## Integration with .NET Backend

**API Calls**
```typescript
// Type-safe API client
interface ApiResponse<T> {
  data: T
  success: boolean
}

async function fetchFromApi<T>(endpoint: string): Promise<T> {
  const response = await fetch(`/api/${endpoint}`)
  if (!response.ok) {
    // Handle ProblemDetails
    const problem = await response.json()
    throw new Error(problem.title || 'API Error')
  }
  const result: ApiResponse<T> = await response.json()
  return result.data
}
```

**Authentication**
```typescript
// JWT token from .NET backend
const token = localStorage.getItem('jwt_token')
fetch('/api/protected', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
})
```

Generate production-ready, accessible, and performant React components optimized for .NET 10 backend integration.
