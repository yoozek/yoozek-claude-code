---
description: Add authentication, authorization, and security to ASP.NET Core Web API endpoints
model: claude-sonnet-4-5
---

Add comprehensive security, authentication, and authorization to the specified ASP.NET Core Web API endpoint.

## Target API Endpoint

$ARGUMENTS

## Security Layers to Implement

### 1. **Authentication** (Who are you?)
- Verify user identity
- Token validation (JWT, API keys, Azure AD)
- Handle expired/invalid tokens
- Use ASP.NET Core Identity or JWT Bearer

### 2. **Authorization** (What can you do?)
- Role-based access control (RBAC) with [Authorize] attributes
- Policy-based authorization
- Resource-based authorization with IAuthorizationService
- Claims-based access control

### 3. **Input Validation**
- Sanitize all inputs
- SQL injection prevention with parameterized queries
- XSS prevention
- Model validation with FluentValidation or Data Annotations

### 4. **Rate Limiting**
- Use built-in ASP.NET Core rate limiting middleware
- Per-user/IP limits
- Custom policies for different endpoints

### 5. **CORS** (if needed)
- Whitelist allowed origins
- Proper headers in Program.cs
- Credentials handling

## Implementation Approach

### For JWT Bearer Authentication:
```csharp
// Configure in Program.cs
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]))
        };
    });

// Use [Authorize] attribute on controllers/endpoints
[Authorize(Roles = "Admin")]
```

### For Policy-Based Authorization:
```csharp
// Define policies in Program.cs
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("RequireAdminRole",
        policy => policy.RequireRole("Admin"));
    options.AddPolicy("MinimumAge",
        policy => policy.Requirements.Add(new MinimumAgeRequirement(18)));
});

// Use on endpoints
[Authorize(Policy = "RequireAdminRole")]
```

### For API Key Authentication:
```csharp
// Custom middleware for API key validation
public class ApiKeyMiddleware
{
    public async Task InvokeAsync(HttpContext context)
    {
        if (!context.Request.Headers.TryGetValue("X-API-Key", out var apiKey))
        {
            context.Response.StatusCode = 401;
            return;
        }
        // Validate API key
    }
}
```

## Security Checklist

**Authentication**
- Verify JWT tokens or API keys
- Handle missing/invalid tokens (401 Unauthorized)
- Check token expiration
- Secure token storage recommendations for clients
- Use HTTPS only
- Configure authentication in Program.cs

**Authorization**
- Use [Authorize] attributes on controllers/actions
- Check user roles/permissions (403 Forbidden)
- Implement resource-based authorization with IAuthorizationService
- Use policy-based authorization for complex rules
- Log authorization failures

**Input Validation**
- Use FluentValidation or Data Annotations
- Always use parameterized queries with Entity Framework
- Validate all DTO properties
- Limit payload sizes
- Use ModelState.IsValid checks

**Rate Limiting**
- Configure ASP.NET Core rate limiting middleware
- Per-endpoint policies
- Per-user/IP limits
- Return 429 Too Many Requests with Retry-After header

**CORS**
- Configure CORS policy in Program.cs
- Whitelist specific origins
- Handle preflight requests
- Secure credentials
- Use named policies for different scenarios

**Error Handling**
- Use ProblemDetails for errors
- Don't expose stack traces in production
- Generic error messages to clients
- Log detailed errors server-side with Serilog
- Consistent error format

**Logging & Monitoring**
- Log authentication attempts with ILogger
- Log authorization failures
- Track suspicious activity
- Monitor rate limit hits
- Use Application Insights or similar

## What to Generate

1. **Protected Controller/Endpoint** - Secured version with [Authorize] attributes
2. **Authentication Configuration** - Program.cs setup for JWT/Identity
3. **Authorization Policies** - Custom policies and requirements
4. **Middleware** - Custom auth middleware if needed
5. **DTOs and Validators** - Request models with FluentValidation
6. **Usage Examples** - React client-side integration with fetch

## Common Patterns for .NET Solo Developers

**Pattern 1: Simple API Key Auth**
```csharp
// For internal tools, admin panels
[ApiKey] // Custom attribute
public class AdminController : ControllerBase
{
    // Protected endpoints
}
```

**Pattern 2: JWT Bearer Auth**
```csharp
// For user-facing apps
[Authorize]
public class UserController : ControllerBase
{
    [HttpGet("profile")]
    public IActionResult GetProfile()
    {
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        // ...
    }
}
```

**Pattern 3: Role-based Auth**
```csharp
// For apps with different user types
[Authorize(Roles = "Admin,Manager")]
public class ReportsController : ControllerBase
{
    // Admin/Manager only endpoints
}
```

**Pattern 4: Policy-based Auth**
```csharp
// For complex authorization logic
[Authorize(Policy = "CanEditDocument")]
public class DocumentController : ControllerBase
{
    private readonly IAuthorizationService _authz;

    [HttpPut("{id}")]
    public async Task<IActionResult> Update(int id, DocumentDto dto)
    {
        var document = await _repo.GetByIdAsync(id);
        var authResult = await _authz.AuthorizeAsync(User, document, "CanEdit");
        if (!authResult.Succeeded)
            return Forbid();
        // ...
    }
}
```

Generate production-ready, secure code that follows the principle of least privilege and uses modern .NET 10 security features.
