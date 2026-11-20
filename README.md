# Task Management API — ASP.NET Core + EF Core

A RESTful task-management API built with ASP.NET Core Web API, Entity Framework Core, and SQLite. The database schema is created and updated through committed EF Core migrations—not by manually creating tables.

## Features

- Create, read, update, and delete tasks (CRUD)
- EF Core + SQLite persistent database
- Committed initial migration and automatic migration on startup
- Service layer (`ITaskService` / `TaskService`) between controller and database
- List filtering by search text, completion status, and priority
- Pagination with total-count metadata
- Request validation and Swagger UI

## Tech stack

- .NET 8 / ASP.NET Core Web API
- Entity Framework Core 8
- SQLite
- Swagger / OpenAPI

## Requirements

- .NET 8 SDK
- Git (for GitHub submission)

Check your SDK:

```bash
dotnet --version
```

## Run locally

1. Clone the repository:

```bash
git clone https://github.com/YOUR-USERNAME/task-api-efcore.git
cd task-api-efcore
```

2. Restore packages and run:

```bash
dotnet restore
dotnet run
```

The app applies the committed EF Core migrations at startup and creates `tasks.db` in the application working directory.

3. Open the Swagger URL printed in the terminal. In Development it is normally:

```text
https://localhost:7xxx/swagger
```

Use the exact port shown by `dotnet run`.

## Database migrations

The initial migration is included in `Migrations/`.

To apply migrations manually:

```bash
dotnet tool install --global dotnet-ef --version 8.0.11
dotnet ef database update
```

To create a future migration after changing `TaskItem` or `AppDbContext`:

```bash
dotnet ef migrations add DescribeYourChange
dotnet ef database update
```

Commit the newly generated migration files to GitHub. Do not create or edit the database tables by hand.

## API endpoints

Base route: `/api/tasks`

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/tasks` | List tasks with filters and pagination |
| GET | `/api/tasks/{id}` | Get one task |
| POST | `/api/tasks` | Create a task |
| PUT | `/api/tasks/{id}` | Replace/update a task |
| DELETE | `/api/tasks/{id}` | Delete a task |

### Query parameters for list endpoint

| Parameter | Example | Meaning |
|---|---|---|
| `search` | `search=report` | Searches title and description |
| `isCompleted` | `isCompleted=false` | Filters by completion |
| `priority` | `priority=High` | `Low`, `Medium`, or `High` |
| `page` | `page=1` | Page number (default 1) |
| `pageSize` | `pageSize=10` | Items per page (1–100, default 10) |

Example:

```http
GET /api/tasks?isCompleted=false&priority=High&page=1&pageSize=5
```

The list response includes `items` and `pagination` (`totalCount`, `page`, `pageSize`, `totalPages`).

### Create task example

```json
{
  "title": "Finish EF Core capstone",
  "description": "Implement service layer and pagination",
  "priority": "High",
  "dueDateUtc": "2026-11-26T18:30:00Z"
}
```

### Update task example

```json
{
  "title": "Finish EF Core capstone",
  "description": "API tested and documented",
  "isCompleted": true,
  "priority": "High",
  "dueDateUtc": "2026-11-26T18:30:00Z"
}
```

## Project structure

```text
TaskApi/
├── Controllers/
│   └── TasksController.cs
├── Data/
│   └── AppDbContext.cs
├── DTOs/
│   ├── CreateTaskRequest.cs
│   ├── UpdateTaskRequest.cs
│   └── TaskResponse.cs
├── Migrations/
│   ├── 202611200001_InitialCreate.cs
│   └── AppDbContextModelSnapshot.cs
├── Models/
│   └── TaskItem.cs
├── Services/
│   ├── ITaskService.cs
│   └── TaskService.cs
├── Program.cs
├── appsettings.json
└── TaskApi.csproj
```

## Design decisions

- Controllers handle HTTP concerns; business/data access logic lives in the service layer.
- `AsNoTracking()` is used for read-only queries.
- Filtering and counting happen in the database before paging.
- Page size is capped at 100 to avoid unbounded list responses.
- SQLite keeps local setup simple while still providing a real relational database.

## Before submitting

1. Run `dotnet restore`.
2. Run `dotnet build`.
3. Run `dotnet run` and test all endpoints in Swagger.
4. Push the complete project and README to a **public** GitHub repository.
5. Submit the repository URL in the assignment form.

## GitHub upload

Create a new public repository named `task-api-efcore`, then run:

```bash
git init
git add .
git commit -m "Build Task API with EF Core"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/task-api-efcore.git
git push -u origin main
```

Replace `YOUR-USERNAME` with your GitHub username.
