---
description: Create a new ASP.NET Core Web API endpoint with validation, error handling, and C#
model: claude-sonnet-4-5
---

Create a new ASP.NET Core Web API endpoint following modern .NET 10 best practices for solo developers.

## Requirements

API Endpoint: $ARGUMENTS

## Implementation Guidelines

### 1. **.NET 10 Minimal API or Controller** (Recommended)
Use Minimal API for simple endpoints or Controllers for complex logic in .NET 10

### 2. **Validation**
- Use FluentValidation or Data Annotations
- Validate input early (before DB/service calls)
- Return clear validation error messages with ProblemDetails

### 3. **Error Handling**
- Global exception handling middleware
- Consistent error response format using ProblemDetails
- Appropriate HTTP status codes
- Never expose sensitive error details

### 4. **Type Safety**
- Strong typing for requests/responses (DTOs)
- Nullable reference types enabled
- No dynamic types

### 5. **Security**
- Input sanitization
- CORS configuration
- Rate limiting with ASP.NET Core middleware
- Authentication/Authorization with JWT or IdentityServer

### 6. **Response Format**
```csharp
// Success
public class ApiResponse<T>
{
    public T? Data { get; set; }
    public bool Success { get; set; } = true;
}

// Error (use ProblemDetails)
{
    "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
    "title": "One or more validation errors occurred",
    "status": 400,
    "errors": { ... }
}
```

## Code Structure

Create a complete Web API endpoint with:

1. **Controller or Minimal API** - `Controllers/[Name]Controller.cs` or `Program.cs`
2. **DTOs** - Request/Response models in `Models/` or `DTOs/`
3. **Validators** - FluentValidation validators in `Validators/`
4. **Service Layer** - Business logic in `Services/`
5. **Example Usage** - Client-side fetch example from React

## Best Practices to Follow

- Early validation before expensive operations
- Proper HTTP status codes (200, 201, 400, 401, 404, 500)
- Use ProblemDetails for error responses
- Dependency injection for services
- Minimal logic in controllers (use services)
- Configuration validation with IOptions pattern
- Structured logging with Serilog or built-in logging
- No sensitive data in responses
- No database queries without validation
- No inline business logic (extract to services)
- Use async/await for I/O operations
- Enable nullable reference types

Generate production-ready code that I can immediately use in my .NET 10 Web API project.
