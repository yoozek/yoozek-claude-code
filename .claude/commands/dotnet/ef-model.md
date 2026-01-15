---
description: Create Entity Framework Core model classes with relationships and configuration
model: claude-sonnet-4-5
---

Create Entity Framework Core model classes following modern .NET 10 best practices.

## Model Specification

$ARGUMENTS

## Entity Framework Core 9.0 Standards

### 1. **Model Class Pattern**

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace YourApp.Data.Models
{
    public class User
    {
        [Key]
        public int Id { get; set; }

        [Required]
        [MaxLength(100)]
        public string Name { get; set; } = string.Empty;

        [Required]
        [EmailAddress]
        [MaxLength(255)]
        public string Email { get; set; } = string.Empty;

        public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
        public DateTime? UpdatedAt { get; set; }

        // Navigation properties
        public ICollection<Order> Orders { get; set; } = new List<Order>();
    }
}
```

### 2. **Entity Configuration (Fluent API)**

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace YourApp.Data.Configurations
{
    public class UserConfiguration : IEntityTypeConfiguration<User>
    {
        public void Configure(EntityTypeBuilder<User> builder)
        {
            builder.ToTable("Users");

            builder.HasKey(u => u.Id);

            builder.Property(u => u.Name)
                .IsRequired()
                .HasMaxLength(100);

            builder.Property(u => u.Email)
                .IsRequired()
                .HasMaxLength(255);

            builder.HasIndex(u => u.Email)
                .IsUnique();

            builder.HasMany(u => u.Orders)
                .WithOne(o => o.User)
                .HasForeignKey(o => o.UserId)
                .OnDelete(DeleteBehavior.Cascade);
        }
    }
}
```

### 3. **DbContext Configuration**

```csharp
using Microsoft.EntityFrameworkCore;

namespace YourApp.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        public DbSet<User> Users { get; set; }
        public DbSet<Order> Orders { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // Apply configurations
            modelBuilder.ApplyConfiguration(new UserConfiguration());
            modelBuilder.ApplyConfiguration(new OrderConfiguration());

            // Or apply all configurations in assembly
            // modelBuilder.ApplyConfigurationsFromAssembly(typeof(ApplicationDbContext).Assembly);
        }
    }
}

// Program.cs - PostgreSQL Configuration
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection"),
        npgsqlOptions => npgsqlOptions.MigrationsAssembly("YourApp.Data")));
```

## What to Generate

1. **Entity Model Class** - Domain model with properties
2. **Entity Configuration** - Fluent API configuration (optional if using Data Annotations)
3. **DbSet Addition** - How to add to DbContext
4. **DTO Classes** - Request/Response DTOs for API
5. **Example Repository** - Repository pattern implementation (optional)

## Relationship Types

**One-to-Many**
```csharp
public class User
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; } = new List<Order>();
}

public class Order
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public User User { get; set; } = null!;
}
```

**Many-to-Many**
```csharp
public class Student
{
    public int Id { get; set; }
    public ICollection<Course> Courses { get; set; } = new List<Course>();
}

public class Course
{
    public int Id { get; set; }
    public ICollection<Student> Students { get; set; } = new List<Student>();
}

// Explicit join entity (optional)
public class StudentCourse
{
    public int StudentId { get; set; }
    public Student Student { get; set; } = null!;

    public int CourseId { get; set; }
    public Course Course { get; set; } = null!;

    public DateTime EnrolledAt { get; set; }
}
```

**One-to-One**
```csharp
public class User
{
    public int Id { get; set; }
    public UserProfile Profile { get; set; } = null!;
}

public class UserProfile
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public User User { get; set; } = null!;
}
```

## Best Practices

**Nullable Reference Types**
- Enable nullable reference types in project
- Use `= null!;` for required navigation properties
- Use `?` for optional properties

**Data Annotations vs Fluent API**
- Simple constraints: Data Annotations
- Complex relationships: Fluent API
- Prefer Fluent API for separation of concerns

**Auditing**
```csharp
public abstract class AuditableEntity
{
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public string? CreatedBy { get; set; }
    public DateTime? UpdatedAt { get; set; }
    public string? UpdatedBy { get; set; }
}
```

**Soft Delete**
```csharp
public class User
{
    public int Id { get; set; }
    public bool IsDeleted { get; set; }
    public DateTime? DeletedAt { get; set; }
}

// Global query filter
modelBuilder.Entity<User>()
    .HasQueryFilter(u => !u.IsDeleted);
```

**Value Conversions**
```csharp
modelBuilder.Entity<User>()
    .Property(u => u.Status)
    .HasConversion<string>(); // Convert enum to string
```

**Owned Types**
```csharp
public class User
{
    public int Id { get; set; }
    public Address Address { get; set; } = null!;
}

public class Address
{
    public string Street { get; set; } = string.Empty;
    public string City { get; set; } = string.Empty;
    public string ZipCode { get; set; } = string.Empty;
}

modelBuilder.Entity<User>()
    .OwnsOne(u => u.Address);
```

## DTOs for API

**Create DTO**
```csharp
public class CreateUserDto
{
    [Required]
    [MaxLength(100)]
    public string Name { get; set; } = string.Empty;

    [Required]
    [EmailAddress]
    public string Email { get; set; } = string.Empty;
}
```

**Response DTO**
```csharp
public class UserDto
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Email { get; set; } = string.Empty;
    public DateTime CreatedAt { get; set; }
}
```

**Mapping (AutoMapper or Mapster)**
```csharp
var userDto = user.Adapt<UserDto>(); // Mapster
// or
var userDto = _mapper.Map<UserDto>(user); // AutoMapper
```

## Common Patterns

**Repository Pattern**
```csharp
public interface IRepository<T> where T : class
{
    Task<T?> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
}
```

**Unit of Work Pattern**
```csharp
public interface IUnitOfWork
{
    IRepository<User> Users { get; }
    IRepository<Order> Orders { get; }
    Task<int> SaveChangesAsync();
}
```

## PostgreSQL-Specific Features

**UUID Primary Keys** (recommended for distributed systems)
```csharp
public class User
{
    public Guid Id { get; set; } = Guid.NewGuid();
    // ...
}

// Configuration
builder.HasKey(u => u.Id);
builder.Property(u => u.Id)
    .HasDefaultValueSql("gen_random_uuid()");
```

**JSON Columns** (PostgreSQL native JSON support)
```csharp
public class User
{
    public int Id { get; set; }
    public Dictionary<string, object> Metadata { get; set; } = new();
}

// Configuration
builder.Property(u => u.Metadata)
    .HasColumnType("jsonb");
```

**Arrays** (PostgreSQL array support)
```csharp
public class User
{
    public int Id { get; set; }
    public string[] Tags { get; set; } = Array.Empty<string>();
}
```

**Required NuGet Packages**
```bash
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL.Design
```

Generate production-ready Entity Framework Core models with proper PostgreSQL configuration, relationships, and DTOs for ASP.NET Core Web API integration.
