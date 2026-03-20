# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The solution has been transformed with no build errors across all projects:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

Since no build errors were detected, the transformation appears to have been successful. The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore and Build the Solution

Run the following commands from the root of the solution to confirm a clean restore and build:

```bash
dotnet restore
dotnet build --configuration Release
```

Ensure there are no warnings that could indicate deprecated APIs or compatibility issues that may surface at runtime.

---

## 2. Run the Unit Tests

Execute the test project to verify that existing logic behaves as expected after the migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --logger "console;verbosity=detailed"
```

Review the test output carefully. Any failing tests should be investigated to determine whether they are caused by behavioral differences in the new .NET runtime or by issues introduced during transformation.

---

## 3. Verify Runtime Behavior of the Web Project

Run the web application locally to confirm it starts and operates correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Check the following:

- The application starts without exceptions.
- All routes and pages load as expected.
- Any database connections (via `Bookstore.Data`) are functioning correctly.
- Authentication and authorization flows work if applicable.

---

## 4. Validate the Data Layer

Since `Bookstore.Data` handles data access, confirm the following:

- If Entity Framework Core is used, verify that migrations are up to date by running:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- Apply any pending migrations to a local or staging database:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- Confirm that all CRUD operations perform correctly against the updated schema.

---

## 5. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Confirm the following:

- All referenced AWS CDK or infrastructure libraries are compatible with the target .NET version.
- Any environment-specific configuration (connection strings, endpoints, keys) is correctly sourced from configuration files or environment variables and not hardcoded.

---

## 6. Check for Removed or Changed APIs

Even without build errors, some .NET APIs behave differently across versions. Review the following areas manually:

- Any use of `System.Web` namespaces, which are not available in cross-platform .NET. These should have been replaced with `Microsoft.AspNetCore` equivalents.
- Any use of `BinaryFormatter`, which is disabled by default in .NET 5 and later.
- Any reflection-based code that may behave differently under newer runtimes.

You can use the [.NET Upgrade Assistant compatibility analyzer](https://learn.microsoft.com/en-us/dotnet/core/porting/upgrade-assistant-overview) to scan for additional compatibility concerns.

---

## 7. Test on Target Operating Systems

Since the goal is cross-platform support, run the application and tests on each intended target platform (Windows, Linux, macOS) to catch any platform-specific issues:

```bash
dotnet test --configuration Release
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Pay attention to:

- File path separators (`\` vs `/`).
- Case sensitivity in file and directory names on Linux.
- Any platform-specific native dependencies.

---

## 8. Publish the Application

Once validation is complete, publish the application for deployment:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Review the contents of the `./publish` directory to confirm all required files and dependencies are present before deploying to the target environment.