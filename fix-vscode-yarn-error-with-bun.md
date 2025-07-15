# Fixing "command not found: yarn" in VSCode / Cursor - and How to Make NPM Scripts Panel Work with Bun

If you've switched to **Bun** but Visual Studio Code (or Cursor) keeps trying to run your scripts using `yarn`, you're not alone. This common problem pops up especially when you click on scripts inside the **NPM Scripts panel** and see errors like:

```bash
> yarn run dev
/bin/sh: yarn: command not found
```

In this guide, you'll learn how to:

* Fix the issue where VSCode or Cursor runs Yarn instead of Bun
* Reveal the hidden NPM Scripts panel (many devs don't even know it exists)
* Configure the correct script runner **without editing raw JSON**

Whether you're migrating from Yarn or just started using Bun, this guide will help you make the most of your developer tools.

---

## 👀 Where Is the "NPM Scripts" Panel?

Surprisingly, many developers don't even know this panel exists - and it's hidden by default.

But first, make sure you've opened a project that includes a `package.json` file at its root. The NPM Scripts panel will not appear otherwise.

### 🔍 How to reveal it in **Cursor**:

1. **Open a project folder** that contains `package.json`
2. In the sidebar, locate the area with headers like `Timeline`, `Outline`, or similar
3. Right-click (two-finger tap on trackpad) on any of these headers **or** on the project name at the top of the sidebar
4. From the context menu, enable **"NPM Scripts"**

### 🔍 In **VSCode**:

1. Same first step: open a project folder with `package.json`
2. Go to the **Explorer Sidebar**
3. Click the **horizontal three dots (...)** in the top-right corner of the sidebar
4. Select **"NPM Scripts"** from the dropdown

📸 **Screenshot Slot 1**: *In Cursor, context menu open on sidebar header, showing "NPM Scripts" toggle.*

📸 **Screenshot Slot 2**: *The NPM Scripts panel fully visible and expanded in Cursor, showing scripts like **`dev`**, **`build`**, etc.*

Once enabled, this panel lets you click scripts directly to run them - and that's where the wrong package manager often kicks in.

---

## 🔥 The Problem: VSCode or Cursor Tries to Use Yarn

Imagine clicking `dev` and seeing:

```bash
> yarn run dev
/bin/sh: yarn: command not found
```

You never installed Yarn. You're using Bun. So why is this happening?

VSCode and Cursor try to detect your package manager automatically, based on lockfiles and context. Here's the priority order:

1. `yarn.lock` ➔ assumes Yarn
2. `package-lock.json` ➔ assumes npm
3. `bun.lock` ➔ currently **ignored** by both editors

💡 Even without a `yarn.lock` file, VSCode might fall back to Yarn based on:

* Workspace history or cache
* Previously used settings
* Presence of Yarn in sibling workspaces (monorepo setups)

> ### ❓ But what about `bun.lock`?
>
> Bun now creates a `bun.lock` file (not `bun.lockb` anymore), which is readable and version-controllable.
>
> However, **VSCode and Cursor do not recognize it** as an indicator for which package manager to use.
>
> So unless you manually configure the editor to use Bun, it might default to Yarn or npm anyway.

---

## ✅ Solution 1: Project-level override

To fix this for a single project, create or edit `.vscode/settings.json`:

```json
{
"npm.packageManager": "bun",
"npm.scriptRunner": "bun"
}
```

This tells Cursor and VSCode:

* Use Bun for running scripts
* Show `bun run <script>` in the NPM Scripts panel instead of `yarn run <script>`

📸 **Screenshot Slot 3**: *Open **`.vscode/settings.json`** with Bun settings in place, inside Cursor.*

> 🔁 Restart the editor to make sure the changes apply.

---

## 🌍 Solution 2: Global Editor Settings (No JSON Required)

If you want Bun as the default across all projects, set it globally through the editor's UI.

### 🌐 In **Cursor**:

1. Open the **Cursor** menu `Preferences` `VSCode Settings`
2. In the sidebar: `Extensions` `Npm`
3. Scroll or search for:

* **"Package Manager"** ➔ select `bun`
* **"Script Runner"** ➔ select `bun`

### 🌐 In **VSCode**:

1. Go to **Code** menu `Preferences` `Settings`
2. Navigate to: `Extensions` `Npm`
3. Set:

* **Package Manager** = `bun`
* **Script Runner** = `bun`

Or just open the Settings and type `npm` in the search bar to find both options.

📸 **Screenshot Slot 4**: *Cursor settings showing Package Manager and Script Runner dropdowns set to **`bun`**.*

---

## ⚠️ Common Pitfalls to Avoid

### 🚨 Phantom Yarn detection

Even after deleting `yarn.lock`, the editor might still default to Yarn. Try:

* Removing or resetting `.vscode/settings.json`
* Restarting the editor (not just Reload Window)

### 🔀 Monorepo quirks

Opening a subfolder of a monorepo that has Yarn configured elsewhere? VSCode might inherit those settings.

💡 Use per-folder `.vscode/settings.json` overrides to stay in control.

---

## 🧪 Verify That It Works

Once set up:

1. Open the NPM Scripts panel
2. Click `dev`
3. You should now see:

```bash
> bun run dev
```

📅 No more `yarn: command not found` errors. You're now fully in Bun territory.

---

## 🧹 Final Thoughts

Switching to Bun is all about speed and simplicity. But VSCode and Cursor still expect Yarn unless you tell them otherwise.

Now you know how to:

* Reveal and use the hidden NPM Scripts panel
* Stop VSCode from defaulting to Yarn
* Configure Bun visually, without editing raw config files
* Handle tricky cases like monorepos and lingering cache

With just a few tweaks, you'll enjoy a much smoother Bun-powered dev experience - right from your editor.

---

## 🤝 About the Author

Hey! I'm Bohdan - full-stack dev who's been building stuff with TypeScript, databases, and web tech for over 10 years. I love great developer experience and recently switched most of my tooling to Bun - and honestly, it's been a blast so far 😎

I enjoy creating handy tools and utilities around Next.js, Drizzle ORM, and scriptable dev environments. You can find some of my open-source work at:

👉 [github.com/ITBoomDev](https://github.com/ITBoomDev)
🌐 someday: [itboom.dev](https://itboom.dev)
💬 Discord: `@itboom`

Let's connect on Discord or GitHub if you're automating your Bun workflow too!

📝 Check out more articles on Medium: [medium.com/@itboom.dev](https://medium.com/@itboom.dev)
