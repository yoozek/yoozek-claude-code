---
description: Create and manage Entity Framework Core migrations for database schema changes
model: claude-sonnet-4-5
---

Create and manage Entity Framework Core migrations following .NET 10 best practices.

## Migration Request

$ARGUMENTS

## Entity Framework Core Migration Workflow

### 1. **Creating a Migration**

```bash
# Create a new migration
dotnet ef migrations add AddUserTable

# With specific DbContext if multiple exist
dotnet ef migrations add AddUserTable --context ApplicationDbContext

# With specific project and startup project
dotnet ef migrations add AddUserTable --project src/YourApp.Data --startup-project src/YourApp.Api
```

### 2. **Reviewing the Migration**

Generated migration files in `Migrations/` folder:
- `[Timestamp]_AddUserTable.cs` - The migration class
- `[Timestamp]_AddUserTable.Designer.cs` - Snapshot metadata
- `ApplicationDbContextModelSnapshot.cs` - Current model snapshot

**Example Migration**
```csharp
using Microsoft.EntityFrameworkCore.Migrations;

public partial class AddUserTable : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Users",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("Npgsql:ValueGenerationStrategy", NpgsqlValueGenerationStrategy.IdentityByDefaultColumn),
                Name = table.Column<string>(maxLength: 100, nullable: false),
                Email = table.Column<string>(maxLength: 255, nullable: false),
                CreatedAt = table.Column<DateTime>(type: "timestamp with time zone", nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Users", x => x.Id);
            });

        migrationBuilder.CreateIndex(
            name: "IX_Users_Email",
            table: "Users",
            column: "Email",
            unique: true);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(name: "Users");
    }
}
```

### 3. **Applying Migrations**

```bash
# Apply all pending migrations to database
dotnet ef database update

# Apply to specific migration
dotnet ef database update AddUserTable

# Rollback to previous migration
dotnet ef database update PreviousMigrationName

# Rollback all migrations
dotnet ef database update 0

# Apply to specific environment
dotnet ef database update --environment Production
```

### 4. **Managing Migrations**

```bash
# List all migrations
dotnet ef migrations list

# Remove last migration (if not applied)
dotnet ef migrations remove

# Generate SQL script from migrations
dotnet ef migrations script

# Generate SQL for specific migration range
dotnet ef migrations script AddUserTable AddOrderTable

# Generate idempotent script (safe to run multiple times)
dotnet ef migrations script --idempotent

# Output SQL to file
dotnet ef migrations script --output migrations.sql
```

### 5. **Database Operations**

```bash
# Drop the database (careful!)
dotnet ef database drop

# Drop and recreate database
dotnet ef database drop --force
dotnet ef database update

# Get connection string information
dotnet ef dbcontext info

# Scaffold DbContext from existing PostgreSQL database (reverse engineer)
dotnet ef dbcontext scaffold "Host=localhost;Database=MyDb;Username=postgres;Password=..." Npgsql.EntityFrameworkCore.PostgreSQL --output-dir Models
```

## Common Migration Scenarios

### Adding a Column
```csharp
// Model change
public class User
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Email { get; set; } = string.Empty;
    public string PhoneNumber { get; set; } = string.Empty; // New column
}

// Generated migration
migrationBuilder.AddColumn<string>(
    name: "PhoneNumber",
    table: "Users",
    maxLength: 20,
    nullable: false,
    defaultValue: "");
```

### Renaming a Column
```csharp
// Migration (manual edit required)
migrationBuilder.RenameColumn(
    name: "Email",
    table: "Users",
    newName: "EmailAddress");
```

### Adding an Index
```csharp
migrationBuilder.CreateIndex(
    name: "IX_Users_Email",
    table: "Users",
    column: "Email",
    unique: true);
```

### Adding a Foreign Key
```csharp
migrationBuilder.CreateIndex(
    name: "IX_Orders_UserId",
    table: "Orders",
    column: "UserId");

migrationBuilder.AddForeignKey(
    name: "FK_Orders_Users_UserId",
    table: "Orders",
    column: "UserId",
    principalTable: "Users",
    principalColumn: "Id",
    onDelete: ReferentialAction.Cascade);
```

### Seeding Data
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.InsertData(
        table: "Users",
        columns: new[] { "Id", "Name", "Email", "CreatedAt" },
        values: new object[] { 1, "Admin", "admin@example.com", DateTime.UtcNow });
}
```

## Best Practices

**Migration Naming**
- Use descriptive names: `AddUserEmailIndex`, `AddOrderTable`
- Follow pattern: `[Action][Entity][Detail]`
- Avoid generic names like `Update1`, `Changes`

**Before Creating Migration**
- Review model changes carefully
- Ensure DbContext is configured correctly
- Test locally before applying to production

**Reviewing Migrations**
- Always review generated migration code
- Customize Up/Down methods if needed
- Add data migration logic for complex changes
- Consider backward compatibility

**Production Migrations**
- Generate SQL script: `dotnet ef migrations script --idempotent`
- Review SQL before applying
- Have rollback plan ready
- Backup database before applying
- Apply during maintenance window for major changes

**Data Migrations**
```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    // Schema change
    migrationBuilder.AddColumn<string>(
        name: "FullName",
        table: "Users",
        nullable: true);

    // Data migration
    migrationBuilder.Sql(@"
        UPDATE Users
        SET FullName = CONCAT(FirstName, ' ', LastName)
        WHERE FullName IS NULL
    ");

    // Make required after data migration
    migrationBuilder.AlterColumn<string>(
        name: "FullName",
        table: "Users",
        nullable: false);
}
```

**Multiple DbContexts**
```bash
# Specify context
dotnet ef migrations add MigrationName --context IdentityDbContext
dotnet ef database update --context IdentityDbContext
```

**Separate Migrations Assembly**
```csharp
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(
        connectionString,
        b => b.MigrationsAssembly("YourApp.Data")));
```

**appsettings.json Connection String**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=myapp;Username=postgres;Password=yourpassword"
  }
}
```

## Troubleshooting

**Migration Already Applied**
- Use `dotnet ef migrations remove` if not applied
- Create new migration to revert changes if applied

**Conflicting Migrations**
- Pull latest code and migrations
- Remove conflicting migration
- Recreate migration

**Migration Errors**
- Check connection string in appsettings.json
- Ensure startup project is correct
- Verify DbContext registration in Program.cs
- Check for model validation errors

## CI/CD Integration

**Automated Migrations**
```csharp
// Program.cs - Apply migrations on startup (development only)
if (app.Environment.IsDevelopment())
{
    using var scope = app.Services.CreateScope();
    var dbContext = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
    await dbContext.Database.MigrateAsync();
}
```

**Production Approach**
- Generate SQL scripts in CI pipeline
- Review and apply manually or via deployment automation
- Use migration bundles for deployment

Generate clear migration instructions and SQL scripts for safe database schema evolution in .NET 10 applications.
