---
description: Create a new React page/route with React Router and TypeScript
model: claude-sonnet-4-5
---

Generate a new React page/route following modern patterns for .NET 10 + React applications.

## Page Specification

$ARGUMENTS

## React Router Standards (v6+)

### 1. **File Structure**
```
src/
  pages/
    HomePage.tsx
    UserProfilePage.tsx
    NotFoundPage.tsx
  features/
    users/
      components/
      hooks/
      types/
```

### 2. **Route Configuration**
```typescript
// App.tsx or routes.tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom'

const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      { path: '', element: <HomePage /> },
      { path: 'users/:id', element: <UserProfilePage /> },
      { path: '*', element: <NotFoundPage /> }
    ]
  }
])
```

### 3. **Page Component Pattern**

```typescript
import { FC } from 'react'
import { useParams, useNavigate } from 'react-router-dom'
import { useQuery } from '@tanstack/react-query'

interface PageProps {
  // Optional props if needed
}

export const UserProfilePage: FC<PageProps> = () => {
  const { id } = useParams<{ id: string }>()
  const navigate = useNavigate()

  // Fetch data from .NET API
  const { data, isLoading, error } = useQuery({
    queryKey: ['user', id],
    queryFn: () => fetchUser(id!)
  })

  if (isLoading) return <LoadingSpinner />
  if (error) return <ErrorMessage error={error} />

  return (
    <div>
      {/* Page content */}
    </div>
  )
}
```

### 4. **Data Fetching from .NET API**

**With TanStack Query** (recommended)
```typescript
import { useQuery, useMutation } from '@tanstack/react-query'

export const HomePage: FC = () => {
  // Query data
  const { data, isLoading } = useQuery({
    queryKey: ['items'],
    queryFn: async () => {
      const res = await fetch('/api/items')
      if (!res.ok) throw await res.json()
      return res.json()
    }
  })

  // Mutation
  const createMutation = useMutation({
    mutationFn: (newItem: CreateItemDto) =>
      fetch('/api/items', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newItem)
      }),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['items'] })
  })

  return <div>{/* content */}</div>
}
```

## What to Generate

1. **Page Component** - Main page component with TypeScript
2. **Route Definition** - How to add to router configuration
3. **TypeScript Types** - Props, DTOs matching .NET backend
4. **Loading State** - Loading component/skeleton
5. **Error Handling** - Error boundary and error display
6. **SEO/Meta Tags** - Using react-helmet-async
7. **Data Fetching** - TanStack Query integration with .NET API

## Page Patterns

**Static Page** (no API calls)
- Marketing pages
- About, Contact, etc.
- Static content

**Data-Driven Page** (fetch from API)
- List views
- Detail views
- Dashboards
- Use TanStack Query for caching

**Form Page** (submit to API)
- User registration
- Data entry
- Settings pages
- Use React Hook Form + TanStack Query mutations

**Protected Page** (authentication required)
```typescript
import { Navigate } from 'react-router-dom'

export const ProtectedPage: FC = () => {
  const { isAuthenticated } = useAuth()

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />
  }

  return <div>{/* Protected content */}</div>
}
```

## Best Practices

**Structure**
- One page component per route
- Co-locate page-specific components
- Shared components in `components/shared/`
- Feature-based organization for complex pages
- Proper TypeScript typing

**Data Fetching**
- Use TanStack Query for server state
- Separate data fetching logic from UI
- Handle loading, error, and success states
- Implement proper error boundaries
- Cache and revalidate appropriately

**Performance**
- Lazy load routes with React.lazy()
- Code splitting per route
- Optimize images
- Virtualize long lists
- Memoize expensive computations
- Prefetch data for likely navigation

**SEO (with react-helmet-async)**
```typescript
import { Helmet } from 'react-helmet-async'

export const HomePage: FC = () => (
  <>
    <Helmet>
      <title>Home - My App</title>
      <meta name="description" content="Welcome to my app" />
      <meta property="og:title" content="Home" />
    </Helmet>
    <div>{/* content */}</div>
  </>
)
```

**Error Handling**
- Error boundaries for runtime errors
- 404 page for invalid routes
- Graceful degradation
- User-friendly error messages
- Log errors to monitoring service

**Accessibility**
- Semantic HTML5 elements
- ARIA labels for dynamic content
- Keyboard navigation
- Focus management on route change
- Announce route changes to screen readers

## React Router Features to Use

**Navigation**
```typescript
const navigate = useNavigate()
navigate('/users/123')
navigate(-1) // Go back

// Link component
<Link to="/about">About</Link>
```

**Route Parameters**
```typescript
const { id, category } = useParams<{ id: string, category: string }>()
```

**Search Params**
```typescript
const [searchParams, setSearchParams] = useSearchParams()
const query = searchParams.get('q')
setSearchParams({ q: 'new query' })
```

**Programmatic Navigation**
```typescript
// After form submission
const mutation = useMutation({
  mutationFn: createItem,
  onSuccess: (data) => navigate(`/items/${data.id}`)
})
```

## Integration with .NET Backend

**API Client Setup**
```typescript
// lib/api.ts
const API_BASE = import.meta.env.VITE_API_URL || '/api'

export async function apiFetch<T>(
  endpoint: string,
  options?: RequestInit
): Promise<T> {
  const token = localStorage.getItem('jwt_token')

  const response = await fetch(`${API_BASE}${endpoint}`, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      ...(token && { Authorization: `Bearer ${token}` }),
      ...options?.headers
    }
  })

  if (!response.ok) {
    // Handle ProblemDetails from .NET
    const problem = await response.json()
    throw new Error(problem.title || 'API Error')
  }

  return response.json()
}
```

**Authentication Flow**
```typescript
// Login page -> Get JWT from .NET -> Store token -> Navigate to protected page
const loginMutation = useMutation({
  mutationFn: (credentials: LoginDto) =>
    apiFetch<{ token: string }>('/auth/login', {
      method: 'POST',
      body: JSON.stringify(credentials)
    }),
  onSuccess: (data) => {
    localStorage.setItem('jwt_token', data.token)
    navigate('/dashboard')
  }
})
```

Generate a complete, production-ready React page with proper TypeScript types, error handling, data fetching from .NET API, and route configuration.
