# ASP.NET Core Identity with SQLite – Setup Guide

This project demonstrates how to integrate ASP.NET Core Identity for user authentication (registration, login, profile management) using **SQLite** as the database, all managed from **VS Code** on macOS.

---

## Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/download)
- [Visual Studio Code](https://code.visualstudio.com/)
- C# extension for VS Code (`ms-dotnettools.csharp`)
- (Optional) [dotnet-aspnet-codegenerator](https://learn.microsoft.com/en-us/aspnet/core/cli/dotnet-aspnet-codegenerator) and [dotnet-ef](https://learn.microsoft.com/en-us/ef/core/cli/dotnet) tools

---

## 1. Clone and Open the Project

```sh
git clone <your-repo-url>
cd ASP
code .
```

---

## 2. Install Required NuGet Packages

```sh
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

---

## 3. Configure SQLite in `appsettings.json`

Edit your `appsettings.json`:

```json
{
  "ConnectionStrings": {
    "ApplicationDbContextConnection": "Data Source=asp.db"
  }
  // ... other settings ...
}
```

---

## 4. Update `Program.cs` for Identity and SQLite

Make sure your `Program.cs` includes:

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;
using ASP.Data;

var builder = WebApplication.CreateBuilder(args);

var connectionString = builder.Configuration.GetConnectionString("ApplicationDbContextConnection")
    ?? throw new InvalidOperationException("Connection string 'ApplicationDbContextConnection' not found.");

builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlite(connectionString));

builder.Services.AddDefaultIdentity<ApplicationUser>(options => options.SignIn.RequireConfirmedAccount = true)
    .AddEntityFrameworkStores<ApplicationDbContext>();

builder.Services.AddControllersWithViews();
builder.Services.AddRazorPages();

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
app.MapRazorPages();

app.Run();
```

---

## 5. Scaffold Identity UI (if needed)

If you want to customize login, registration, or profile pages, scaffold the Identity UI:

```sh
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc ASP.Data.ApplicationDbContext
```

This will generate pages under `Areas/Identity/Pages/Account` and `Manage`.

---

## 6. Add Razor Pages and Authentication to the Pipeline

Ensure these lines are present in `Program.cs` (as above):

```csharp
builder.Services.AddRazorPages();
app.UseAuthentication();
app.MapRazorPages();
```

---

## 7. Add Login/Register Links to the Layout

In `Views/Shared/_Layout.cshtml`, add:

```html
<partial name="_LoginPartial" />
```

Or, if `_LoginPartial.cshtml` does not exist, add:

```html
<li><a asp-area="Identity" asp-page="/Account/Login">Login</a></li>
<li><a asp-area="Identity" asp-page="/Account/Register">Register</a></li>
```

---

## 8. Create and Apply Migrations

If you previously used SQL Server, **delete all files in your `Migrations` folder** and the `asp.db` file.

Then run:

```sh
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## 9. Run the Application

```sh
dotnet run
```

Open the URL shown in the terminal (e.g., `https://localhost:5001`) in your browser.

---

## 10. Register and Login

- You should see **Login** and **Register** links.
- Register a new user and log in to test the authentication flow.

---

## Troubleshooting

- If you see errors about database types (e.g., `nvarchar(max)`), ensure you deleted old migrations and regenerated them after switching to SQLite.
- If you don’t see login/register links, make sure you mapped Razor Pages and added the links to your layout.

---

## Summary

You now have a working ASP.NET Core MVC project with Identity authentication, using SQLite as the database, fully manageable from VS Code on macOS!

---

**If you have any issues, check the error messages and ensure all steps above are followed. For further help, open an issue or ask your assistant!**
