# Portfolio Architecture & Setup Guide

This document provides a comprehensive overview of the portfolio structure, tech stack, styling approach, and architectural patterns. Use this as a blueprint for creating similar portfolio/blog applications.

## Tech Stack

### Core Framework
- **Astro** (v5.13.7) - Static site generator with component islands architecture
- **TypeScript** (v5.6.3) - Type safety and enhanced developer experience
- **Node.js** (ES Modules) - Modern JavaScript module system

### Styling
- **Tailwind CSS** (v3.4.14) - Utility-first CSS framework
- **@astrojs/tailwind** (v6.0.2) - Astro integration for Tailwind
- **Custom CSS** - Global styles in `src/styles/global.css`

### Content Management
- **@astrojs/mdx** (v4.3.5) - MDX support for content files
- **Astro Content Collections** - Type-safe content management with Zod schemas

### SEO & Metadata
- **@astrojs/sitemap** (v3.5.1) - Automatic sitemap generation
- **@astrojs/rss** (v4.0.12) - RSS feed generation
- **View Transitions** - Built-in Astro view transitions for smooth navigation

### Typography
- **@fontsource/nunito-sans** (v5.1.0) - Display font (Nunito)
- **@fontsource/open-sans** (v5.1.0) - Body font (Open Sans)

### Development Tools
- **@astrojs/check** (v0.9.4) - Type checking for Astro projects

## Project Structure

```
portfolio/
├── public/                    # Static assets (images, videos, favicons)
│   ├── images/
│   └── videos/
├── src/
│   ├── components/           # Reusable Astro components
│   │   ├── BaseHead.astro   # SEO metadata component
│   │   ├── Header.astro     # Navigation header
│   │   ├── Footer.astro     # Site footer
│   │   ├── Narrow.astro     # Content width constraint wrapper
│   │   ├── MediaGrid.astro  # Image/video grid component
│   │   └── HeaderLink.astro # Navigation link component
│   ├── content/             # Content collections
│   │   └── blog/            # Blog post MDX files
│   │   └── config.ts        # Content collection schemas
│   ├── layouts/             # Page layout templates
│   │   ├── BaseLayout.astro # Default page layout
│   │   └── PostLayout.astro # Blog post layout
│   ├── pages/               # File-based routing
│   │   ├── index.astro      # Homepage
│   │   ├── about.astro      # About page
│   │   ├── work.astro       # Work/portfolio page
│   │   ├── blog.astro       # Blog listing page
│   │   ├── [...slug].astro  # Dynamic blog post routes
│   │   └── rss.xml.js       # RSS feed endpoint
│   ├── styles/
│   │   └── global.css       # Global styles and Tailwind directives
│   └── consts.ts            # Site-wide constants
├── astro.config.mjs         # Astro configuration
├── tailwind.config.mjs      # Tailwind configuration
├── tsconfig.json            # TypeScript configuration
└── package.json             # Dependencies and scripts
```

## Configuration Files

### `astro.config.mjs`
```javascript
import { defineConfig } from "astro/config";
import mdx from "@astrojs/mdx";
import sitemap from "@astrojs/sitemap";
import tailwind from "@astrojs/tailwind";

export default defineConfig({
  site: "https://jareddevine.com",
  integrations: [mdx(), sitemap(), tailwind()],
});
```

### `tailwind.config.mjs`
```javascript
import defaultTheme from "tailwindcss/defaultTheme";

export default {
  content: ["./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}"],
  theme: {
    extend: {
      fontFamily: {
        display: ["Nunito", ...defaultTheme.fontFamily.sans],
        sans: ["Open Sans", ...defaultTheme.fontFamily.sans],
      },
    },
  },
  plugins: [],
};
```

### `tsconfig.json`
```json
{
  "extends": "astro/tsconfigs/strict",
  "compilerOptions": {
    "strictNullChecks": true
  }
}
```

## Styling Approach

### Global Styles (`src/styles/global.css`)
- Uses Tailwind `@apply` directive for component-level styles
- Typography hierarchy with display and body font families
- Custom utility classes:
  - `.rounded-large` - 80px border radius
  - `.rounded-small` - 24px border radius
- Antialiased text rendering
- Heading styles applied globally

### Design System
- **Colors**: Primarily uses Tailwind's default zinc palette for grays
- **Typography**: 
  - Display font (Nunito) for headings
  - Body font (Open Sans) for content
- **Spacing**: Consistent use of Tailwind spacing scale
- **Layout**: Max-width container (7xl) with responsive padding
- **Images**: Aspect ratio [2/3], rounded corners, shadow-lg

### Responsive Design
- Mobile-first approach
- Breakpoints: `md:` prefix for medium screens and up
- Flexible grid layouts (1 column mobile, 2-3 columns desktop)
- Responsive navigation (hamburger menu on mobile)

## Component Architecture

### Layout Components

#### `BaseLayout.astro`
- Wraps all standard pages
- Includes Header and Footer
- Max-width container with padding
- Accepts `title` and `description` props for SEO

#### `PostLayout.astro`
- Specialized layout for blog posts
- Includes post title and description
- Article semantic HTML
- Border separator for header section

### UI Components

#### `BaseHead.astro`
- Handles all SEO metadata
- Open Graph tags for social sharing
- Twitter card metadata
- Canonical URLs
- View Transitions integration
- Imports global CSS

#### `Header.astro`
- Responsive navigation with hamburger menu
- Profile image and site title
- Navigation links (About, Work, Blog, Contact)
- Mobile menu toggle with animation
- Client-side JavaScript for menu interactions

#### `Footer.astro`
- Simple footer with site title link
- Minimal design

#### `Narrow.astro`
- Content width constraint wrapper (max-w-4xl)
- Used for long-form content sections
- Consistent spacing

#### `MediaGrid.astro`
- Flexible media display component
- Supports images and videos
- Responsive grid layout
- Optional reverse order
- Poster images for videos
- High-quality image rendering with multiple densities

## Content Management

### Content Collections
- Uses Astro's Content Collections API
- Type-safe with Zod schemas
- Collection: `blog`
- Schema defined in `src/content/config.ts`

### Blog Post Schema
```typescript
{
  title: string;
  description?: string;
  pubDate: Date (coerced from string);
  updatedDate?: Date (coerced from string);
  heroImage?: string;
  tags?: string[];
}
```

### Content Structure
- MDX files in `src/content/blog/`
- Frontmatter for metadata
- MDX content for rich text
- Can import and use Astro components within MDX

### Example Blog Post
```mdx
---
title: Post Title
description: Post description
pubDate: 'Jul 08 2024'
heroImage: '/images/image.jpg'
tags: ['Portfolio', 'Product Design']
---

import { Image } from 'astro:assets';
import Narrow from '../../components/Narrow.astro';
import MediaGrid from '../../components/MediaGrid.astro';

<Narrow>
Content here...
</Narrow>

<MediaGrid
  image1Src="/images/image1.jpg"
  image1Alt="Description"
  videoSrc="/videos/demo.mp4"
  videoPoster="/images/poster.png"
/>
```

## Routing & Pages

### File-Based Routing
- `src/pages/` directory maps to routes
- `index.astro` → `/`
- `about.astro` → `/about`
- `work.astro` → `/work`
- `blog.astro` → `/blog`
- `[...slug].astro` → Dynamic routes for blog posts

### Dynamic Routes
- `[...slug].astro` uses `getStaticPaths()` to generate routes
- Fetches all blog posts from content collection
- Renders each post with `PostLayout`

### RSS Feed
- `rss.xml.js` generates RSS feed
- Uses `@astrojs/rss` integration
- Includes all blog posts

## Key Features

### 1. Content Filtering
- Homepage: Shows posts with "Featured" tag
- Work page: Shows posts with "Portfolio" tag
- Blog page: Shows posts without "Portfolio" tag
- Posts sorted by publication date (newest first)

### 2. Image Optimization
- Uses Astro's `Image` component
- Automatic image optimization
- Multiple density support (1.5x, 2x)
- Lazy loading (except hero images)
- Quality: "max" for important images

### 3. SEO Optimization
- Semantic HTML structure
- Meta tags for all pages
- Open Graph and Twitter cards
- Canonical URLs
- Automatic sitemap generation

### 4. Performance
- Static site generation
- View Transitions for smooth navigation
- Optimized images
- Minimal JavaScript (only for mobile menu)

### 5. Responsive Design
- Mobile-first approach
- Flexible grid layouts
- Responsive typography
- Mobile navigation menu

## NPM Scripts

```json
{
  "dev": "astro dev",        // Development server
  "start": "astro dev",       // Alias for dev
  "build": "astro check && astro build",  // Type check + build
  "preview": "astro preview", // Preview production build
  "astro": "astro"            // Astro CLI
}
```

## Design Patterns

### 1. Component Composition
- Small, focused components
- Props-based configuration
- Slot-based content injection

### 2. Layout Inheritance
- Base layout for common structure
- Specialized layouts for specific content types
- Consistent header/footer across pages

### 3. Content-Driven Pages
- Pages generated from content collections
- Type-safe content access
- Flexible filtering and sorting

### 4. Progressive Enhancement
- Works without JavaScript
- JavaScript enhances UX (mobile menu)
- Graceful degradation

## Asset Organization

### Public Assets
- `public/images/` - Static images
- `public/videos/` - Video files
- `public/favicon.svg` - Site favicon
- `public/images/favicons/` - Additional favicon formats

### Image Usage
- Hero images referenced in frontmatter
- Component images use Astro's Image component
- Videos use standard HTML5 video tags

## Development Workflow

1. **Setup**
   ```bash
   npm install
   ```

2. **Development**
   ```bash
   npm run dev
   ```
   - Starts dev server at `http://localhost:4321`
   - Hot module replacement
   - Type checking

3. **Build**
   ```bash
   npm run build
   ```
   - Type checks code
   - Generates static site in `dist/`
   - Optimizes assets

4. **Preview**
   ```bash
   npm run preview
   ```
   - Serves production build locally

## Best Practices

### Component Design
- Keep components focused and reusable
- Use TypeScript interfaces for props
- Leverage Astro's component islands architecture

### Content Management
- Use content collections for type safety
- Define clear schemas for frontmatter
- Organize content logically

### Styling
- Prefer Tailwind utilities over custom CSS
- Use `@apply` sparingly for repeated patterns
- Maintain consistent spacing and typography

### Performance
- Optimize images with Astro's Image component
- Use lazy loading for below-the-fold content
- Minimize JavaScript usage

### SEO
- Provide meaningful titles and descriptions
- Use semantic HTML
- Include Open Graph and Twitter cards
- Generate sitemaps automatically

## Customization Points

### To Adapt for Another Project:

1. **Update Site Constants** (`src/consts.ts`)
   - Site title
   - Site description

2. **Modify Astro Config** (`astro.config.mjs`)
   - Update site URL
   - Add/remove integrations as needed

3. **Customize Tailwind** (`tailwind.config.mjs`)
   - Change fonts
   - Add custom colors
   - Extend theme as needed

4. **Update Content Schema** (`src/content/config.ts`)
   - Modify frontmatter schema
   - Add/remove fields

5. **Customize Components**
   - Update Header navigation
   - Modify Footer content
   - Adjust layout components

6. **Replace Assets**
   - Update images in `public/images/`
   - Replace favicons
   - Update social media banners

## Dependencies Summary

### Production Dependencies
- `astro` - Core framework
- `@astrojs/mdx` - MDX support
- `@astrojs/rss` - RSS feed
- `@astrojs/sitemap` - Sitemap generation
- `@astrojs/tailwind` - Tailwind integration
- `@fontsource/nunito-sans` - Display font
- `@fontsource/open-sans` - Body font
- `tailwindcss` - CSS framework
- `typescript` - Type safety

### Development Dependencies
- `@astrojs/check` - Type checking

## Notes

- Uses ES Modules (`"type": "module"` in package.json)
- Strict TypeScript configuration
- No build-time framework (React, Vue, etc.) - pure Astro components
- Minimal client-side JavaScript (only for mobile menu)
- Static site generation for optimal performance
- Content-driven architecture for easy content management

