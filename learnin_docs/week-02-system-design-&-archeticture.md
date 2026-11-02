# Week 02 System Design & Architecture

> My notes about how I analyzed the project, how I designed the architecture, and why I chose to build it this way.
>
> This document is part of my Code2Career journey with Gaza Sky Geeks. I'm using it to document my thinking before writing code, understand the reasoning behind my design decisions, and build the habit of planning before implementing.
> I applied these notes while building the [Task-Manager-App](https://github.com/dinajendeya/Task-Manager-App.git)
---

## Quick Refrence

[Why I'm Writing This](#why-am-i-writing-this-document) ·
[System Design?](#what-is-system-design) ·
[Project Analysis](#project-analysis) ·
[Users](#who-are-the-users) ·
[Roles & Permissions](#user-roles--permissions) ·
[Requirements](#requirements-analysis) ·
[Security implementations ](#security-considerations) ·
[Layered Architecture](#why-i-chose-a-layered-architecture) ·
[Architecture Overview](#architecture-overview) ·
[Layers](#what-each-layer-does) ·
[Database Design](#planned-database-design) ·
[API Design](#current-api-design) ·
[Future Improvements](#future-security-improvements) ·
[Reflection](#reflection)

---

# Why Am I Writing This Document?

While building this project, I realized that writing code is only one part of software engineering.

Before coding, I need to understand:

- What problem am I solving?
- Who will use the application?
- What features do they need?
- How should the different parts of the application work together?

This process is called system design.

For this reason, I decided to document my thinking before building the complete application.

---

# What is System Design?

Before starting this project, I thought software development was mostly about writing code.

Now I understand that system design is the process of planning an application before building it.

I can think of it as creating a blueprint for a house before construction begins.

Instead of immediately writing code, I first think about:

- Features
- Users
- Data
- Architecture
- Security
- Scalability

Good design helps me avoid problems later.

---

# Project Analysis

## What Am I Building?

I am building a Secure Task Manager API.

The goal is to allow users to:

- Register an account
- Log in
- Create tasks
- Update tasks
- Delete tasks
- View their tasks
- Search tasks
- Filter tasks

At the beginning, the project will only expose an API.

A frontend can be added later.

---

## Why This Project?

I chose this project because it combines several concepts I want to learn:

- Backend development
- API design
- Databases
- Authentication
- Authorization
- Secure coding practices

Since I'm interested in cybersecurity, I also want this project to reflect good security practices instead of being just another CRUD application.

---

# Who Are The Users?

The main users are people who want to organize their tasks.

Each user should:

- Have their own account
- See only their own tasks
- Manage tasks securely

A user should never be able to view another user's tasks.

This immediately tells me that authentication and authorization will be important features.

---

# User Roles & Permissions

While analyzing the project, I realized that not all users should have the same permissions.

At first, I only thought about regular users creating and managing their own tasks. Later, I started thinking about administration and security concerns.

Because of that, I decided to introduce roles into the system.

Initially, the application will have two roles:

```text
USER
ADMIN
```

---

## Regular User

A regular user can:

- Register an account
- Login
- Create tasks
- View their own tasks
- Update their own tasks
- Delete their own tasks

A regular user should never be able to:

- View another user's tasks
- Modify another user's tasks
- Access administrative functionality

---

## Admin

An admin has additional permissions.

An admin can:

- View all users
- Manage users
- Delete inappropriate or unwanted tasks
- Access administrative endpoints

Examples:

```http
GET /api/admin/users
GET /api/admin/users/:id
DELETE /api/admin/users/:id
```

These endpoints should only be accessible by admins.

---

# Why Roles Matter

Without roles, every authenticated user would have the same level of access.

This could create security problems.

For example:

A regular user should never be able to delete another user's account.

A regular user should never be able to view tasks that belong to someone else.

Roles help enforce these restrictions.

---

# Role-Based Access Control (RBAC)

While researching authorization, I learned about something called:

```text
Role-Based Access Control (RBAC)
```

RBAC is an authorization model that grants permissions based on roles rather than individual users.

Instead of asking:

```text
Can Mahmoud access this endpoint?
```

the application asks:

```text
Does the USER role have permission?
```

or

```text
Does the ADMIN role have permission?
```

This makes permission management much simpler as the application grows.

---

## Planned Permission Matrix

| Action | User | Admin |
|----------|----------|----------|
| Register | ✅ | ✅ |
| Login | ✅ | ✅ |
| Create Task | ✅ | ✅ |
| View Own Tasks | ✅ | ✅ |
| Edit Own Tasks | ✅ | ✅ |
| Delete Own Tasks | ✅ | ✅ |
| View All Users | ❌ | ✅ |
| Delete Users | ❌ | ✅ |
| Access Admin Endpoints | ❌ | ✅ |

---

# Requirements Analysis

## Functional Requirements

These are things the application must do.

### Authentication

- Register
- Login
- Logout

### Task Management

- Create tasks
- View tasks
- Update tasks
- Delete tasks

### Search

- Search by title

### Filtering

- Filter by status
- Filter by priority

### Administration

- View users
- Manage users
- View all tasks
- Access administrative endpoints

---

## Non-Functional Requirements

These are qualities the application should have.

### Security

Since security is one of my interests, I want to follow secure development practices.

Examples:

- Password hashing
- JWT authentication
- HttpOnly cookies
- Input validation
- RBAC authorization

### Maintainability

I want the project structure to stay clean even when the application grows.

### Scalability

The application should be easy to extend later.

For example:

- Notifications
- User profiles
- Teams
- Shared tasks

---

# Security Considerations

Since cybersecurity is one of my main interests, I want security to be part of the design from the beginning instead of adding it later.

---

## Password Hashing

Passwords should never be stored in plain text.

I plan to use:

```text
bcrypt
```

to hash passwords before storing them in the database.

---

## Authentication

After logging in, users will receive a token that proves their identity.

I plan to use:

```text
JWT (JSON Web Token)
```

for authentication.

---

## Secure Cookies

Authentication data should not be exposed to client-side JavaScript.

For this reason, I plan to use:

```text
HttpOnly Cookies
```

when appropriate.

---

## Input Validation

All incoming data should be validated before reaching business logic.

Examples:

- Required fields
- Email format validation
- Password rules
- Task title length limits

---

## Authorization Checks

Authentication answers:

```text
Who is the user?
```

Authorization answers:

```text
What is the user allowed to do?
```

Even if a user is authenticated, the application must still verify that the user has permission to perform the requested action.

---

## Ownership Verification

Before updating or deleting a task, the application should verify:

```text
Does this task actually belong to the current user?
```

This helps prevent unauthorized access to other users' data.

---

# Why I Chose a Layered Architecture

At first, I could have written everything inside one file.

Something like:

```ts
app.get("/tasks", (req, res) => {
  // logic
});
```

This works for small projects.

However, as applications grow, files become difficult to manage.

To avoid this problem, I decided to separate responsibilities.

---

# What Does "Layer" Mean?

A layer is simply a section of the application that has one specific responsibility.

Each layer should focus on one job.

Instead of one file doing everything:

```text
Validation
Database
Business Logic
Response
```

I separate them.

This makes the project easier to understand and maintain.

---

# Architecture Overview

My application currently follows:

```text
Request
 ↓
Route
 ↓
Middleware
 ↓
Controller
 ↓
Service
 ↓
Database
 ↓
Response
```

Each layer has a different responsibility.

---

# What Each Layer Does

## Routes

Routes define API endpoints.

Example:

```ts
router.get("/", getTasksController);
```

Responsibilities:

- Define endpoints
- Map requests to controllers

Routes should not contain business logic.

---

## Middleware

Middleware runs before the controller.

Examples:

- Authentication
- Authorization
- Validation
- Logging
- Rate limiting

Example:

```ts
router.get(
  "/admin/users",
  authenticate,
  authorizeAdmin,
  getUsersController
);
```

---

## Controllers

Controllers act as a bridge between requests and business logic.

Example:

```ts
export const getTasksController = (req, res) => {
  const tasks = getTasksService();

  res.json(tasks);
};
```

Responsibilities:

- Read request data
- Call services
- Return responses

Controllers should stay thin.

---

## Services

The service layer contains business logic.

Example:

```ts
export const getTasksService = () => {
  return tasks;
};
```

Responsibilities:

- Process data
- Apply business rules
- Communicate with the database

This is usually where most application logic lives.

---

## Database Layer

The database stores the application's data.

For this project I chose:

```text
PostgreSQL
```

and

```text
Prisma ORM
```

Responsibilities:

- Store users
- Store tasks
- Retrieve data
- Update data

The rest of the application should not directly depend on database details.

---

# Why This Structure Is Useful

Let's imagine I decide to replace PostgreSQL with another database later.

Because I separated responsibilities:

```text
Routes
Controllers
Services
```

will mostly remain unchanged.

Only the database implementation needs to change.

This is one of the biggest advantages of layered architecture.

---

# Planned Database Design

At the moment, I expect the system to have two main entities.

## User

```text
id
name
email
passwordHash
role
createdAt
updatedAt
```

---

## Task

```text
id
title
description
status
priority
dueDate
userId
createdAt
updatedAt
```

---

## Relationship

```text
One User
    ↓
Many Tasks
```

A task belongs to exactly one user.

---

# Current API Design

At the moment I plan to implement:

```http
GET    /api/tasks
GET    /api/tasks/:id

POST   /api/tasks

PUT    /api/tasks/:id

DELETE /api/tasks/:id
```

Authentication:

```http
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
```

Admin:

```http
GET    /api/admin/users
GET    /api/admin/tasks
DELETE /api/admin/users/:id
```

Later I will add:

```http
GET /api/tasks?search=
GET /api/tasks?priority=
GET /api/tasks?status=
```

for searching and filtering.

---

# Future Security Improvements

As I continue learning secure software development, I may extend the project with:

- Refresh tokens
- Account lockout mechanisms
- Rate limiting
- Audit logging
- Password reset functionality
- Email verification
- Security monitoring

---

# Reflection

Before this project, I mostly focused on writing code and getting features to work.

While working on this design document, I started thinking more like a software engineer. Instead of jumping directly into implementation, I spent time understanding users, permissions, architecture, scalability, and security.

One thing that stood out to me is that software design is not about making things complicated. It is about organizing complexity before it becomes difficult to manage.

I also learned that security starts long before authentication middleware or JWTs are added to the codebase. Decisions about roles, permissions, ownership, and authorization begin during the design phase.

As I continue building this project, I expect this document to evolve with the application and serve as a reference for future improvements and architectural decisions.
