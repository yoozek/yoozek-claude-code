---
description: Test ASP.NET Core Web API endpoints with xUnit and integration tests
model: claude-sonnet-4-5
---

Generate comprehensive Web API tests for the specified endpoint using xUnit.

## Target

$ARGUMENTS

## Test Strategy for .NET 10 Solo Developers

Create practical, maintainable tests using modern .NET testing tools:

### 1. **Testing Approach**
- Unit tests for validation logic and services
- Integration tests for full API flow with WebApplicationFactory
- Edge case coverage
- Error scenario testing

### 2. **Tools** (recommended stack)
- **xUnit** - Modern .NET testing framework (recommended)
- **FluentAssertions** - Readable assertions
- **Moq** - Mocking framework
- **WebApplicationFactory** - Integration testing
- **Testcontainers** - PostgreSQL container for integration tests (recommended)

### 3. **Test Coverage**

**Happy Paths**
- Valid inputs return expected results
- Proper status codes
- Correct response structure
- Successful database operations

**Error Paths**
- Invalid input validation
- Authentication failures
- Authorization failures
- Server errors
- Missing required fields
- Database constraint violations

**Edge Cases**
- Null/empty requests
- Malformed JSON
- Large payloads
- Special characters in input
- SQL injection attempts
- Concurrent operations

### 4. **Test Structure**

```csharp
public class ApiEndpointTests : IClassFixture<WebApplicationFactory<Program>>
{
    [Fact]
    public async Task EndpointName_ValidRequest_ReturnsSuccess()
    {
        // Arrange
        var client = _factory.CreateClient();
        var request = new { /* ... */ };

        // Act
        var response = await client.PostAsJsonAsync("/api/endpoint", request);

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.OK);
    }

    [Theory]
    [InlineData("invalid")]
    public async Task EndpointName_InvalidInput_ReturnsBadRequest(string input)
    {
        // Test implementation
    }
}
```

### 5. **What to Generate**

1. **Unit Test File** - Test service/business logic in isolation
2. **Integration Test File** - Test full API with WebApplicationFactory
3. **Mock Data/Fixtures** - Realistic test data
4. **Helper Classes** - Custom WebApplicationFactory with test database
5. **Test Configuration** - appsettings.Test.json

## Key Testing Principles

- Test behavior, not implementation
- Clear, descriptive test names following convention: `MethodName_Scenario_ExpectedResult`
- Arrange-Act-Assert pattern
- Independent tests (no shared state)
- Fast execution for unit tests, slower integration tests acceptable
- Realistic mock data
- Test error messages and ProblemDetails
- Use Theory for parametrized tests
- Don't test framework internals
- Avoid brittle tests

## Additional Scenarios to Cover

1. **Authentication/Authorization**
   - Valid JWT tokens
   - Expired tokens
   - Missing tokens
   - Invalid permissions/roles
   - Claims validation

2. **Data Validation**
   - FluentValidation rules
   - Data annotation validation
   - Type mismatches
   - Out of range values
   - SQL injection attempts

3. **Entity Framework**
   - Database writes succeed
   - Concurrency conflicts
   - Transaction rollback
   - Navigation properties loaded correctly

4. **Performance**
   - Response times
   - Large dataset handling
   - Concurrent requests
   - Database connection pooling

## Testcontainers for PostgreSQL Integration Tests

**NuGet Packages**
```bash
dotnet add package Testcontainers.PostgreSql
dotnet add package xUnit
dotnet add package FluentAssertions
```

**Custom WebApplicationFactory with Testcontainers**
```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc.Testing;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection.Extensions;
using Testcontainers.PostgreSql;
using Xunit;

public class CustomWebApplicationFactory : WebApplicationFactory<Program>, IAsyncLifetime
{
    private readonly PostgreSqlContainer _dbContainer = new PostgreSqlBuilder()
        .WithDatabase("testdb")
        .WithUsername("postgres")
        .WithPassword("postgres")
        .Build();

    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.ConfigureServices(services =>
        {
            // Remove existing DbContext registration
            services.RemoveAll(typeof(DbContextOptions<ApplicationDbContext>));

            // Add DbContext with Testcontainers connection string
            services.AddDbContext<ApplicationDbContext>(options =>
            {
                options.UseNpgsql(_dbContainer.GetConnectionString());
            });

            // Build service provider
            var serviceProvider = services.BuildServiceProvider();

            // Create database and apply migrations
            using var scope = serviceProvider.CreateScope();
            var dbContext = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
            dbContext.Database.Migrate();
        });
    }

    public async Task InitializeAsync()
    {
        await _dbContainer.StartAsync();
    }

    public new async Task DisposeAsync()
    {
        await _dbContainer.DisposeAsync();
    }
}
```

**Integration Test with Testcontainers**
```csharp
public class UserApiTests : IClassFixture<CustomWebApplicationFactory>
{
    private readonly CustomWebApplicationFactory _factory;
    private readonly HttpClient _client;

    public UserApiTests(CustomWebApplicationFactory factory)
    {
        _factory = factory;
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task CreateUser_ValidRequest_ReturnsCreated()
    {
        // Arrange
        var createUserDto = new CreateUserDto
        {
            Name = "John Doe",
            Email = "john@example.com"
        };

        // Act
        var response = await _client.PostAsJsonAsync("/api/users", createUserDto);

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.Created);
        var user = await response.Content.ReadFromJsonAsync<UserDto>();
        user.Should().NotBeNull();
        user!.Name.Should().Be("John Doe");
        user.Email.Should().Be("john@example.com");
    }

    [Fact]
    public async Task GetUser_ExistingUser_ReturnsUser()
    {
        // Arrange - Create user first
        var createDto = new CreateUserDto { Name = "Jane", Email = "jane@example.com" };
        var createResponse = await _client.PostAsJsonAsync("/api/users", createDto);
        var createdUser = await createResponse.Content.ReadFromJsonAsync<UserDto>();

        // Act
        var response = await _client.GetAsync($"/api/users/{createdUser!.Id}");

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.OK);
        var user = await response.Content.ReadFromJsonAsync<UserDto>();
        user.Should().NotBeNull();
        user!.Id.Should().Be(createdUser.Id);
    }
}
```

**Benefits of Testcontainers**
- Real PostgreSQL database for integration tests
- Isolated test environment (no shared state between test runs)
- Automatic cleanup after tests
- Same database engine as production
- No mocking of database layer
- Test actual SQL queries and constraints

**Running Tests**
```bash
# Ensure Docker is running
docker ps

# Run all tests
dotnet test

# Run specific test class
dotnet test --filter "FullyQualifiedName~UserApiTests"

# Run tests in parallel (faster)
dotnet test --parallel
```

Generate production-ready tests with Testcontainers integration that I can run immediately with `dotnet test`.
