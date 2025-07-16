# How to Migrate to Tailwind CSS v4 with HeroUI: Move Your Theme to globals.css the Right Way

Tailwind CSS v4 introduced a major shift in how we define themes: instead of configuring everything in `tailwind.config.js`, you can now define your entire design system directly in CSS using the new `@theme` layer.

In this guide, I’ll walk you through how to migrate your design tokens — like colors, spacing, radius, and fonts — into a modern `globals.css` structure. We’ll first explore how to do it in a typiсal Tailwind project, and then show how it fits beautifully with HeroUI.

---

## 🧠 What Changed in Tailwind v4?

Traditionally, your design system lived in JavaScript:

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      colors: {
        primary: '#2563eb',
        background: '#f9fafb',
      },
      borderRadius: {
        lg: '1rem',
      },
    },
  },
};
```

With Tailwind v4, you can define these tokens in your CSS directly:

```css
@theme {
  --color-primary: #2563eb;
  --color-background: #f9fafb;
  --radius-lg: 1rem;
}
```

✅ Tailwind picks these up automatically.
✅ You don't need to duplicate values in config.
✅ Easier for designers/devs to collaborate using shared CSS files.

---

## 🔍 How File Scanning Works Now

Tailwind now **[automatically scans](https://tailwindcss.com/docs/detecting-classes-in-source-files#which-files-are-scanned)** your project files — except a few excluded types:

* `.gitignore`-d files
* `node_modules` directory
* Binary files like images/videos
* CSS files (unless registered explicitly)
* Lockfiles (`yarn.lock`, `package-lock.json`, etc.)

That means you no longer need to add paths like `src/**/*.{ts,tsx}` in `content`, but **you still need to explicitly register** files outside this scope — like HeroUI’s theme:

```css
@source '../../node_modules/@heroui/theme/dist/**/*.{js,ts,jsx,tsx}';
```

---

## 🧱 Base Project Structure (No HeroUI)

Before we introduce HeroUI, let’s start with a minimal setup to move your tokens into `globals.css`:

```bash
my-project/
├── node_modules/
├── src/
│   ├── styles/
│   │   └── globals.css
└── ...
```

### Sample `globals.css`

```css
@import "tailwindcss";
@layer theme, base, components, utilities;

@theme {
  --color-primary: #2563eb;
  --color-background: #f9fafb;
  --radius-lg: 1rem;
}
```

In the next article, we’ll show how to split these into dedicated files like `colors.css`, `typography.css`, etc. For now, this single `globals.css` works great.

---

## ✨ HeroUI Integration

If you’re using HeroUI you’ll need to do two small things:

### 1. Create a HeroUI plugin file

```ts
// src/styles/hero.ts
import { heroui } from "@heroui/react";
export default heroui();
```

### 2. Update `globals.css`

```css
@import "tailwindcss";
@layer theme, base, components, utilities;

@plugin './hero.ts';
@source '../../node_modules/@heroui/theme/dist/**/*.{js,ts,jsx,tsx}';

@theme {
  --color-primary: #2563eb;
  --color-primary-foreground: #ffffff;

  --color-secondary: #475569;
  --color-secondary-foreground: #ffffff;

  --color-success: #10b981;
  --color-danger: #ef4444;
  --color-warning: #facc15;

  --radius-lg: 1rem;
}
```

> 🟥 **Warning:** The path in `@source` depends on the location of your `globals.css`. If it’s in `src/styles/`, the correct path is likely `../../node_modules/...`

> 🟨 **Note:** Tailwind v4 no longer requires you to register project files like `src/**/*.tsx` manually — it scans everything except `node_modules`, binary assets, and CSS by default.

---

## 💡 What’s Next?

This guide focused on how to migrate your Tailwind theme tokens from `tailwind.config.js` to `globals.css`, with and without HeroUI.

🧩 In the next article, I’ll show how to introduce **multiple themes** (e.g. light/dark or brand variations), organize tokens into logical CSS modules, and support runtime theming with native Tailwind v4 capabilities.

> 🟦 **Let me know in the comments** if you're interested in that! Once we hit 50 requests, I’ll publish the full multi-theme architecture guide. 😄

---

## 🤝 About the Author

Hey! I’m Bohdan — a full-stack developer who’s passionate about clean tools, thoughtful workflows, and making development just a little more enjoyable every day.

I share practical insights, experiments, and lessons learned along the way. You’ll find some of my open-source work here:

👉 [github.com/ITBoomDev](https://github.com/ITBoomDev)
🌐 someday: [itboom.dev](https://itboom.dev)
💬 Discord: `@itboom`

Let’s connect — always happy to chat about code, tools, and better ways to build things.

---

📝 Check out more articles on Medium: [medium.com/@itboom.dev](https://medium.com/@itboom.dev)

If you liked this article — feel free to share it with someone who's upgrading their Tailwind stack! ❤️
