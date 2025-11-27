---
title: Static Site for GitHub Pages with Astro
desc: How to create a static site with Tailwind CSS, daisyUI, and Typography for GitHub Pages deployment
---

## Recommended Directory Structure

```
astro_1/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions workflow
├── src/
│   ├── assets/
│   │   └── app.css            # Main CSS file
│   ├── components/
│   │   └── Header.astro       # Reusable components
│   ├── layouts/
│   │   └── Layout.astro       # Base layout
│   └── pages/
│       └── index.astro        # Homepage (routes)
├── public/
│   └── favicon.svg            # Static assets
├── astro.config.mjs           # Astro configuration
├── package.json
└── README.md
```

## Setup Instructions

### 1. Create a new Astro project

Create a new Astro project in the current directory:

```sh:Terminal
npm create astro@latest ./
```

When prompted:
- Choose "Empty" template
- Install dependencies: Yes
- TypeScript: Yes (or your preference)
- Git repository: Already initialized

### 2. Install Tailwind CSS, daisyUI, and Typography

```sh:Terminal
npm install -D tailwindcss@latest @tailwindcss/vite@latest daisyui@latest @tailwindcss/typography
```

### 3. Configure Astro for GitHub Pages

Update your Astro config for static site generation and GitHub Pages:

```js:astro.config.mjs
// @ts-check
import { defineConfig } from "astro/config";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  site: 'https://YOUR_USERNAME.github.io',
  base: '/astro_1',  // Replace with your repo name
  output: 'static',
  vite: {
    plugins: [tailwindcss()],
  },
});
```

### 4. Setup Tailwind CSS with daisyUI and Typography

Create your main CSS file with all plugins:

```postcss:src/assets/app.css
@import "tailwindcss";
@plugin "daisyui";
@plugin "@tailwindcss/typography";
```

### 5. Import CSS in your Layout

```astro:src/layouts/Layout.astro
---
import "../assets/app.css";

interface Props {
  title: string;
}

const { title } = Astro.props;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <title>{title}</title>
  </head>
  <body>
    <slot />
  </body>
</html>
```

### 6. Create GitHub Actions Workflow

Create the deployment workflow:

```yaml:.github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 7. Using Typography Plugin

The typography plugin provides beautiful typographic defaults for prose content:

```astro:src/pages/index.astro
---
import Layout from '../layouts/Layout.astro';
---

<Layout title="Welcome to My Site">
  <main class="container mx-auto px-4 py-8">
    <article class="prose lg:prose-xl mx-auto">
      <h1>Welcome to My Static Site</h1>
      <p>
        This is a beautiful static site built with Astro, styled with
        Tailwind CSS, daisyUI components, and typography plugin.
      </p>
      <h2>Features</h2>
      <ul>
        <li>Static site generation with Astro</li>
        <li>Tailwind CSS for styling</li>
        <li>daisyUI for components</li>
        <li>Typography plugin for beautiful text</li>
        <li>Automated deployment to GitHub Pages</li>
      </ul>
    </article>
  </main>
</Layout>
```

### 8. Enable GitHub Pages

1. Push your code to GitHub
2. Go to repository Settings → Pages
3. Under "Build and deployment", select "GitHub Actions" as the source
4. Push to main branch to trigger deployment

Now you can use daisyUI components and typography classes!