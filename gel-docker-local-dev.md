# Seamless Local Development with Gel + Docker + Drizzle

If you've ever tried setting up a local database environment that Just Works with Docker, migrations, and schema-driven development, you're going to love this.

But here's the catch: **most developers struggle with getting Gel CLI to properly talk to a Docker container**. It's not obvious how to link the running container, initialize the project correctly, or avoid phantom instances like `localhost:10701`. If you've hit these weird behaviors or just want a foolproof setup that works every time-this guide is for you.

**Gel** is a modern database that speaks SQL but brings in the developer-friendliness of EdgeDB. In this article, I'll show you how to set up a clean, repeatable workflow to:

* Run Gel in Docker
* Link it correctly with your project
* Run migrations
* Use it with Drizzle ORM

---

## 🔁 TL;DR

```bash
# 1. Start your Docker container
# docker-compose.yml should expose port 5656

gel instance link --docker --container <docker_container_name> <gel_instance_name>

# 2a. If gel.toml already exists

gel init --link --server-instance <gel_instance_name>

# 2b. If gel.toml does not exist

gel init --interactive
# During prompts: use the same instance name as above
```

> ❗️**Most issues with Gel CLI come from running `gel project init` too early, or without linking the Docker container first.** Read on to understand why.

---

## 🏢 Step 1: Running Gel via Docker

Your `docker-compose.yml` should look like this:

```yaml
version: '3.8'

services:
gel:
image: geldata/gel
container_name: gel_your_project
ports:
- "5656:5656"
volumes:
- "./packages/db/dbschema:/dbschema"
- "./volumes/gel:/var/lib/gel"
restart: unless-stopped
```

Start it with:

```bash
docker-compose up -d
```

Make sure the container is actually running:

```bash
docker ps
```

---

## 🔗 Step 2: Link the Docker container

> This is the **critical step** most developers miss.

Before you run `gel init`, you must let the CLI know that there's a Docker container running Gel.

```bash
gel instance link --docker --container gel_your_project your_project_instance
```

> This registers a named instance (`your_project_instance`) in your local CLI config that points to the Docker container.

Without this step, Gel might create a phantom instance running on `localhost:10701` or similar, which is not your Docker one. That's how confusion starts.

---

## 🌍 Step 3: Connect your project

Now we link your project folder to the instance. Depending on whether `gel.toml` exists or not, choose one of the following:

### ✅ If you don't have a `gel.toml` yet:

Run:

```bash
gel init --interactive
```

> ⚠️ **Warning:** If you skip `--interactive`, Gel will silently create a new default instance (named `db`) even if you already linked your Docker instance. This results in the infamous `localhost:10701` confusion.
>
> Always use `--interactive` to ensure you explicitly choose the Docker-linked instance during setup.

Follow the prompts:

```
Do you want to initialize a new project? [Y/n] Y
Specify the name of the Gel instance to use with this project [default: db]: your_project_instance
Do you want to use existing instance "your_project_instance" for the project? [y/n] y
```

Boom - your `gel.toml` is created and project is linked.

#### 🚫 What if the wrong (phantom) instance was linked?

If you accidentally linked a wrong or phantom instance like `db`, you can safely unlink it:

```bash
gel project unlink
```

If you want to delete the instance and all its data:

```bash
gel project unlink -D
```

> 🟥 **DO NOT DELETE `gel.toml` manually** if your project is linked to any instance.
>
> Doing so can cause Gel to lose track of the link, and you may have to manually clean up the instance metadata under Gel's internal directories.
>
> Always unlink the project first using the commands above!

---

### ✅ If you already have a `gel.toml`:

Just run:

```bash
gel init --link --server-instance your_project_instance
```

> ❗️This only works if `gel.toml` is already present.
> If not, you'll get an error like:
> `gel error: 'gel.toml' not found, unable to link...`

---

## 💻 Bonus: Use with Drizzle ORM

After your Gel instance is live and your project is linked:

```bash
gel migrate
npx drizzle-kit pull
```

This will generate your Drizzle schema types based on Gel's schema.

---

## 🚀 That's it!

Now you have:

* a database running in Docker
* CLI-aware schema and migrations
* full support for Drizzle codegen

You can commit your `gel.toml`, schema files, and even add:

```json
// package.json
"scripts": {
"db:init": "gel instance link ... && gel init ... && gel migrate && drizzle-kit pull"
}
```

Now your team can onboard with one command ✨

---

## ✨ Final Tips

* Always start with `gel instance link` before anything else
* Never run `gel project init` unless you're sure what instance you're linking to
* Use `--interactive` to avoid accidental default instance creation
* Clean up with `gel project unlink` or `gel project unlink -D` instead of deleting files manually
* 🔴 **Never delete `gel.toml` if your project is linked to an instance!**

---

## 🤝 About the Author

Hey! I'm Bohdan - full-stack dev who's been building stuff with TypeScript, databases, and web tech for over 10 years. I love PostgreSQL but decided to give Gel a try - and honestly, it's been a blast so far 😎

I enjoy creating handy tools and utilities around Next.js and database workflows. You can find some of my open-source work at:

👉 [github.com/ITBoomDev](https://github.com/ITBoomDev)
🌐 someday: [itboom.dev](https://itboom.dev)
💬 Discord: `@itboom`

Let's connect on Discord or GitHub if you're playing with Gel too!
