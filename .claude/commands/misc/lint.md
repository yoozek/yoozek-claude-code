---
description: Run linting and code analysis for .NET and React projects
model: claude-sonnet-4-5
---

Run comprehensive code linting and analysis for .NET 10 and React codebase.

## Target

$ARGUMENTS

## .NET Code Analysis

### 1. **Built-in .NET Analyzers**

```bash
# Enable analyzer warnings as errors in .csproj
<PropertyGroup>
  <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  <AnalysisLevel>latest</AnalysisLevel>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
  <Nullable>enable</Nullable>
</PropertyGroup>

# Run analysis
dotnet build /p:EnforceCodeStyleInBuild=true

# Run with detailed diagnostics
dotnet build /p:EnforceCodeStyleInBuild=true /warnaserror
```

### 2. **Code Style Configuration (.editorconfig)**

```ini
# .editorconfig
root = true

[*.cs]
# Naming conventions
dotnet_naming_rule.interfaces_should_be_prefixed_with_i.severity = error
dotnet_naming_rule.interfaces_should_be_prefixed_with_i.symbols = interface
dotnet_naming_rule.interfaces_should_be_prefixed_with_i.style = begins_with_i

# Code style rules
csharp_prefer_braces = true:error
csharp_style_var_for_built_in_types = false:suggestion
dotnet_sort_system_directives_first = true

# Analyzer rules
dotnet_diagnostic.CA1001.severity = error # Types that own disposable fields should be disposable
dotnet_diagnostic.CA1822.severity = warning # Mark members as static
dotnet_diagnostic.CA2007.severity = none # Do not directly await a Task
```

### 3. **StyleCop Analyzers**

```bash
# Install StyleCop
dotnet add package StyleCop.Analyzers

# Add to .csproj
<ItemGroup>
  <PackageReference Include="StyleCop.Analyzers" Version="1.2.0-beta.507">
    <PrivateAssets>all</PrivateAssets>
    <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
  </PackageReference>
</ItemGroup>

# Configure in stylecop.json
{
  "$schema": "https://raw.githubusercontent.com/DotNetAnalyzers/StyleCopAnalyzers/master/StyleCop.Analyzers/StyleCop.Analyzers/Settings/stylecop.schema.json",
  "settings": {
    "documentationRules": {
      "companyName": "Your Company"
    }
  }
}
```

### 4. **Roslynator**

```bash
# Install Roslynator
dotnet add package Roslynator.Analyzers

# Run code analysis
dotnet roslynator analyze

# List diagnostics
dotnet roslynator list-symbols

# Fix issues automatically
dotnet roslynator fix
```

### 5. **SonarAnalyzer**

```bash
# Install SonarAnalyzer
dotnet add package SonarAnalyzer.CSharp

# Integrates with SonarQube/SonarCloud for continuous inspection
```

## React/TypeScript Linting

### 1. **ESLint Configuration**

```bash
# Install ESLint
npm install --save-dev eslint @eslint/js @types/eslint__js typescript typescript-eslint

# Initialize ESLint
npx eslint --init
```

**eslint.config.js** (ESLint 9+ flat config)
```javascript
import js from '@eslint/js'
import tseslint from 'typescript-eslint'
import reactHooks from 'eslint-plugin-react-hooks'
import reactRefresh from 'eslint-plugin-react-refresh'

export default tseslint.config(
  { ignores: ['dist', 'build'] },
  js.configs.recommended,
  ...tseslint.configs.strictTypeChecked,
  {
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      parserOptions: {
        project: ['./tsconfig.json', './tsconfig.node.json'],
      },
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      'react-refresh/only-export-components': 'warn',
      '@typescript-eslint/no-unused-vars': 'error',
      '@typescript-eslint/no-explicit-any': 'error',
      '@typescript-eslint/explicit-function-return-type': 'off',
    },
  },
)
```

### 2. **Running ESLint**

```bash
# Lint all files
npx eslint .

# Lint specific directory
npx eslint src/

# Auto-fix issues
npx eslint . --fix

# Show warnings and errors
npx eslint . --max-warnings 0
```

### 3. **Prettier Configuration**

```bash
# Install Prettier
npm install --save-dev prettier eslint-config-prettier

# .prettierrc
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}

# Format code
npx prettier --write .
```

### 4. **TypeScript Strict Mode**

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

## Combined Linting Scripts

**package.json**
```json
{
  "scripts": {
    "lint": "eslint . && dotnet build /p:EnforceCodeStyleInBuild=true",
    "lint:fix": "eslint . --fix && prettier --write . && dotnet format",
    "lint:ts": "eslint .",
    "lint:dotnet": "dotnet build /p:EnforceCodeStyleInBuild=true",
    "format": "prettier --write . && dotnet format"
  }
}
```

## .NET Formatting

```bash
# Format code with dotnet format
dotnet format

# Format and verify (CI mode)
dotnet format --verify-no-changes

# Format specific files
dotnet format --include path/to/file.cs

# Format with specific options
dotnet format --include-generated
```

## Pre-commit Hooks (Husky + lint-staged)

```bash
# Install husky and lint-staged
npm install --save-dev husky lint-staged

# Initialize husky
npx husky init

# .husky/pre-commit
npm run lint-staged

# package.json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.cs": ["dotnet format --include"]
  }
}
```

## CI/CD Integration

**GitHub Actions**
```yaml
name: Lint

on: [push, pull_request]

jobs:
  lint-dotnet:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.0.x'
      - run: dotnet build /p:EnforceCodeStyleInBuild=true
      - run: dotnet format --verify-no-changes

  lint-react:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run lint
      - run: npm run format:check
```

## Common Issues & Fixes

**.NET**
- Nullable reference warnings → Enable `<Nullable>enable</Nullable>`
- Unused variables → Remove or use discard `_`
- Missing XML docs → Add `<GenerateDocumentationFile>true</GenerateDocumentationFile>`

**React/TypeScript**
- `any` types → Replace with proper types
- Unused imports → Use `eslint --fix`
- Missing return types → Add explicit return types for complex functions

Generate comprehensive linting configuration and commands for maintaining code quality across .NET and React projects.
