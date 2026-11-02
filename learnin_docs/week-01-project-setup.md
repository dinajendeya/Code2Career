# Week 01 - Project Foundations

## Goal

Today I started documenting my journey with Code2Career porgramme backend development path.

My goal was not to build features yet, but to understand the foundations of the project, set up the environment correctly, and learn how the different parts of a backend application work together.

---

## Qiuck Refrence
[Node.js](#what-is-nodejs) ·
[TypeScript](#why-am-i-using-typescript) ·
[tsc](#what-is-tsc) ·
[npm](#what-is-npm) ·
[package.json](#what-is-packagejson) ·
[Installing Packages](#what-happens-when-i-install-packages) ·
[Express](#what-is-express) ·
[@types Packages](#why-did-i-install-types-packages) ·
[save-dev](#what-does--d-or---save-dev-mean) ·
[npx](#what-is-npx) ·
[tsconfig.json](#what-is-tsconfigjson) ·
[Modules](#commonjs-vs-es-modules) ·
[Import Issues](#why-were-my-imports-failing) ·
[Architecture](#architecture-im-using) ·
[Layers](#what-each-layer-does) ·
[First Bug](#my-first-real-bug) ·
[What I Built](#what-i-built-today) ·
[Challenges](#challenges-i-faced) ·
[Commands](#commands-i-learned) ·
[Next Steps](#next-steps) ·
[Reflection](#reflection)


# What I Learned Today

## What is Node.js?

Node.js is a JavaScript runtime environment that allows me to run JavaScript outside the browser.

It is built on Google's V8 engine and can be used to build:

- Web servers
- APIs
- Backend applications
- Command-line tools

Before learning Node.js, I only thought of JavaScript as a browser language. Now I understand that JavaScript can also run on the server side.

---

## Why Am I Using TypeScript?

Node.js understands JavaScript, but my project is written in TypeScript.

TypeScript is basically JavaScript with extra features such as:

- Types
- Interfaces
- Enums
- Generics

Example:

```ts
const name: string = "Mahmoud";
```

JavaScript does not understand the `: string` part, so TypeScript needs to be transformed into JavaScript before it can run.

---

## What is `tsc`?

`tsc` stands for:

```text
TypeScript Compiler
```

Its job is to convert TypeScript into JavaScript.

Example:

### TypeScript

```ts
const age: number = 20;
```

### JavaScript

```js
const age = 20;
```

So the flow looks like this:

```text
TypeScript
      ↓
     tsc
      ↓
JavaScript
```

---

## What is npm?

npm stands for:

```text
Node Package Manager
```

It helps me manage:

- Packages
- Dependencies
- Scripts
- Project configuration

To create my project I used:

```bash
npm init -y
```

This generated a file called:

```text
package.json
```

---

## What is package.json?

The `package.json` file is like the identity card of the project.

It contains information such as:

```json
{
  "name": "task-manager-api",
  "version": "1.0.0",
  "type": "module"
}
```

It also stores:

- Dependencies
- Development dependencies
- Scripts
- Project information

Example:

```json
"scripts": {
  "dev": "ts-node app.ts"
}
```

---

## What Happens When I Install Packages?

Example:

```bash
npm install express
```

npm will:

1. Download the package.
2. Add it to `node_modules`.
3. Update `package.json`.
4. Update `package-lock.json`.

---

## What is Express?

Express is a framework built on top of Node.js.

Node.js can create servers by itself, but Express makes everything much easier.

Example:

```ts
app.get("/", () => {});
```

Express provides:

- Routing
- Middleware
- Request handling
- Response handling

---

## Why Did I Install @types Packages?

Since I'm using TypeScript, I need type definitions.

I installed:

```bash
npm install -D @types/node
```

```bash
npm install -D @types/express
```

These packages help TypeScript understand things like:

```ts
Request
Response
process.env
```

and provide better autocomplete and error checking.

---

## What Does `-D` or `--save-dev` Mean?

I learned that npm packages can be installed in two ways.

### Regular Dependencies

```bash
npm install express
```

These are required when the application runs.

Examples:

- express
- dotenv
- bcrypt
- jsonwebtoken
- @prisma/client

---

### Development Dependencies

```bash
npm install -D typescript
```

These are only used while building the project.

Examples:

- typescript
- ts-node
- nodemon
- prisma
- eslint

A simple rule:

> If the application needs it to run, it's a dependency. If only developers use it, it's a dev dependency.

---

## What is npx?

`npx` allows me to run package commands directly.

Examples:

```bash
npx tsc --init
```

```bash
npx tsc --version
```

```bash
npx prisma init
```

This is useful because I don't need to install these tools globally.

---

## What is tsconfig.json?

The TypeScript compiler uses:

```text
tsconfig.json
```

This file controls how TypeScript behaves.

Example:

```json
{
  "compilerOptions": {
    "module": "nodenext",
    "target": "esnext"
  }
}
```

It controls things like:

- Module system
- Target JavaScript version
- Compiler rules
- Output settings

---

## CommonJS vs ES Modules

I learned that there are two module systems.

### CommonJS

Import:

```js
const express = require("express");
```

Export:

```js
module.exports = app;
```

---

### ES Modules

Import:

```ts
import express from "express";
```

Export:

```ts
export default app;
```

My project uses:

```json
"type": "module"
```

so I'm working with ES Modules.

---

## Why Were My Imports Failing?

This was one of the biggest issues I faced today.

Because my project uses:

```json
"type": "module"
```

and

```json
"module": "nodenext"
```

TypeScript expected imports like:

```ts
import taskRoutes from "./src/routes/task.routes.js";
```

even though the file is actually:

```text
task.routes.ts
```

At first this was very confusing, but now I understand that TypeScript eventually compiles the file into JavaScript.

---

# Architecture I'm Using

The project follows:

```text
Layered Architecture
```

which is often described as:

```text
MVC + Service Layer
```

Request flow:

```text
Request
 ↓
Route
 ↓
Controller
 ↓
Service
 ↓
Database
 ↓
Response
```

This separation makes the project easier to maintain and scale.

---

# What Each Layer Does

## Routes

Routes define API endpoints.

Example:

```ts
router.get("/", getTasksController);
```

---

## Controllers

Controllers receive requests and send responses.

Example:

```ts
res.json(tasks);
```

---

## Services

Services contain the business logic.

Examples:

```ts
getTasksService();
createTaskService();
deleteTaskService();
```

---

## Models

Models represent database entities.

Examples:

```text
User
Task
```

---

## Middleware

Middleware runs before controllers.

Examples:

- Authentication
- Logging
- Rate limiting

Most middleware passes control using:

```ts
next();
```

---

## Validators

Validators check incoming data.

Examples:

- Email validation
- Password validation
- Required fields validation

---

# My First Real Bug

Today I got this error:

```text
Cannot destructure property 'title' of 'req.body'
```

At first I thought my controller was broken.

After debugging I found out that:

```ts
req.body
```

was actually:

```ts
undefined
```

The reason was simple.

I forgot:

```ts
app.use(express.json());
```

After adding it, everything worked.

This helped me understand how middleware works and why Express needs JSON parsing before reaching controllers.

---

# What I Built Today

Current flow:

```text
Postman
   ↓
GET /api/tasks
   ↓
Route
   ↓
Controller
   ↓
Service
   ↓
JSON Response
```

I also built:

```http
POST /api/tasks
```

using fake data.

Even though there is no database yet, the architecture is working correctly.

---

# Challenges I Faced

## Import Problems

### Problem

TypeScript couldn't find route files.

### Cause

Issues related to ES Modules and NodeNext imports.

### Solution

Use the correct import format and understand how TypeScript resolves modules.

---

## Missing JSON Body

### Problem

```ts
req.body
```

was undefined.

### Cause

Missing:

```ts
app.use(express.json());
```

### Solution

Added the middleware before the routes.

---

# Commands I Learned

```bash
npm init -y
```

```bash
npm install express
```

```bash
npm install -D typescript
```

```bash
npm install -D ts-node
```

```bash
npm install -D @types/node
```

```bash
npm install -D @types/express
```

```bash
npx tsc --init
```

```bash
npm run dev
```

---

# Next Steps

Tomorrow I plan to:

- Install Prisma
- Connect PostgreSQL
- Design the database schema
- Create User and Task models
- Generate the first migration
- Replace fake data with a real database
- Continue implementing CRUD operations

---

# Reflection

Today felt more like learning how the backend ecosystem works than building features.

I learned how Node.js, TypeScript, npm, Express, and project architecture fit together. I also spent a lot of time debugging import issues and middleware problems, but honestly those were the moments that taught me the most.

By the end of the day, I successfully built my first API endpoints and finally understood the complete flow of a request from the route all the way to the response.
