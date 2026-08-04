# Dara Web App — AI-Powered Educational Platform (Frontend)

[![Nuxt](https://img.shields.io/badge/Nuxt-3.9+-green.svg)](https://nuxt.com/)
[![Vue](https://img.shields.io/badge/Vue-3.3+-brightgreen.svg)](https://vuejs.org/)
[![Tailwind](https://img.shields.io/badge/Tailwind-3+-cyan.svg)](https://tailwindcss.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5+-blue.svg)](https://typescriptlang.org/)
[![Vercel](https://img.shields.io/badge/Analytics-Vercel-black.svg)](https://vercel.com/analytics)
[![PWA](https://img.shields.io/badge/PWA-enabled-purple.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

> **Dara Frontend** — Nuxt 3 educational platform with AI tools: Summarizer, Question Generator, and Chat Assistant. Connects to separate [Dara Backend](https://github.com/oovaa/dara) for AI processing.

---

## 🎯 Overview

**Dara** (درة = "Pearl" in Arabic) is an AI-powered educational platform for students. This repository contains the **frontend application** built with Nuxt 3. It provides three core AI-driven study tools that communicate with a separate backend service.

### Three Core Tools

| Tool | Route | Description |
|------|-------|-------------|
| **Summarizer** | `/tools/summarize` | Condenses lengthy texts into concise summaries |
| **Question Generator** | `/tools/questions-extractor` | Generates comprehension questions from content |
| **Chat with Dara** | `/tools/chat` | Interactive AI tutor for study questions |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Dara Frontend (Nuxt 3)                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │  Summarize  │  │  Questions  │  │    Chat     │             │
│  │   Page      │  │  Extractor  │  │   Page      │             │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘             │
│         │                │                │                     │
│         └────────────────┼────────────────┘                     │
│                          ▼                                     │
│                 ┌─────────────────┐                            │
│                 │  API Composables│                            │
│                 │  (useApi, etc.) │                            │
│                 └────────┬────────┘                            │
└───────────────────────────┼─────────────────────────────────────┘
                            │ HTTPS API Calls
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Dara Backend (Separate Repo)                │
│  https://github.com/oovaa/dara                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │  Express    │  │  LangChain  │  │   Cohere    │             │
│  │  + Bun      │  │  Chains     │  │  Embeddings │             │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
dara-webapp/
├── app.vue                    # Root layout with nav, footer, analytics
├── app.config.ts              # App configuration
├── nuxt.config.ts             # Nuxt 3 configuration
├── tsconfig.json              # TypeScript config
├── tailwind.config.js         # Tailwind CSS config
├── package.json               # Dependencies & scripts
├── Dockerfile                 # Container build
├── ecosystem.config.cjs       # PM2 process config
├── components/                # Shared UI components
│   ├── Loader.vue             # Loading spinner
│   ├── PreLoader.vue          # Page transition loader
│   ├── Toast.vue              # Notification toast
│   └── dropzone.vue           # File upload zone
├── composables/               # Vue composables (API, utilities)
├── layouts/                   # Page layouts
├── pages/                     # File-based routing (Nuxt)
│   ├── index.vue              # Landing page
│   ├── about.vue              # About page + team
│   └── tools/                 # AI tool pages
│       ├── summarize.vue      # Text summarization
│       ├── questions-extractor.vue  # Question generation
│       └── chat.vue           # AI chat interface
├── plugins/                   # Nuxt plugins
├── public/                    # Static assets (logos, waves, favicon)
├── server/                    # Server-side API routes (Nuxt Nitro)
└── assets/                    # Global styles, fonts
```

---

## 🛠 Tech Stack

| Category | Technologies |
|----------|--------------|
| **Framework** | Nuxt 3.9+ (Vue 3, Nitro, Vite) |
| **Language** | TypeScript 5+ |
| **Styling** | Tailwind CSS 3, @tailwindcss/typography |
| **State/Composables** | Vue 3 Composition API, `useAsyncData`, `useFetch` |
| **Animations** | GSAP 3.12+ |
| **Markdown** | markdown-it + plugins (emoji, footnote, deflist, etc.) |
| **File Upload** | vue3-dropzone |
| **PWA** | @vite-pwa/nuxt |
| **Analytics** | @vercel/analytics/nuxt |
| **Image Optimization** | @nuxt/image |
| **DevTools** | @nuxt/devtools |
| **Runtime** | Node.js 18+ (or Bun) |
| **Package Manager** | npm / pnpm / yarn |

---

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ (or Bun 1.0+)
- Backend running at `http://localhost:3000` (or configured URL) — see [Dara Backend](https://github.com/oovaa/dara)

### Installation

```bash
# Clone the repository
git clone https://github.com/oovaa/dara-webapp.git
cd dara-webapp

# Install dependencies
npm install
# or: pnpm install / yarn install / bun install
```

### Development

```bash
# Start development server on http://localhost:3000
npm run dev
# or: pnpm run dev / yarn dev / bun run dev
```

### Production Build

```bash
# Build for production
npm run build

# Preview production build locally
npm run preview
```

### Docker

```bash
# Build image
docker build -t dara-webapp .

# Run container
docker run -p 3000:3000 dara-webapp
```

---

## ⚙️ Configuration

### Environment Variables

Create a `.env` file (or use Nuxt runtime config):

```bash
# .env
NUXT_PUBLIC_API_BASE=http://localhost:3000/api  # Backend API URL
NUXT_PUBLIC_DOMAIN=http://localhost:3000         # Frontend domain
```

### Nuxt Config Highlights (`nuxt.config.ts`)

- **Modules**: `@nuxtjs/tailwindcss`, `@vite-pwa/nuxt`, `@nuxt/image`, `@vercel/analytics/nuxt`
- **Runtime Config**: Exposes `public.DOMAIN` and `public.API_BASE` to client
- **SEO**: Default meta tags, Open Graph, Twitter Card
- **HTML Lang**: Arabic (`ar`) as default

---

## 📱 Features

### Landing Page (`/`)
- Hero section with animated background (GSAP particles)
- Feature overview cards
- Navigation to tools and about page

### Tools
- **Summarize** (`/tools/summarize`): Paste text → get AI summary
- **Questions Extractor** (`/tools/questions-extractor`): Generate study questions
- **Chat with Dara** (`/tools/chat`): Conversational AI tutor

### About Page (`/about`)
- Project vision and mission
- Team members with GitHub/Twitter links
- Technology acknowledgments

### UI/UX
- Responsive design (mobile-first)
- Dark/light theme support via Tailwind
- Toast notifications for feedback
- Loading states and skeleton screens
- PWA support (offline-capable)
- Arabic RTL support (`dir="rtl"` on HTML)

---

## 🔗 Backend Integration

The frontend expects a REST API at the configured `API_BASE` with these endpoints:

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/summarize` | POST | Summarize text |
| `/api/questions` | POST | Generate questions |
| `/api/chat` | POST | Chat completion |

See [Dara Backend](https://github.com/oovaa/dara) for backend implementation.

---

## 🧪 Development Commands

```bash
# Type checking
npx nuxi typecheck

# Lint (if ESLint configured)
npm run lint

# Generate static site (SSG)
npm run generate

# Prepare for deployment
npm run postinstall  # Runs nuxt prepare
```

---

## 📦 Deployment

### Vercel (Recommended)
1. Connect repository to Vercel
2. Set environment variables in Vercel dashboard
3. Deploy — auto-detects Nuxt 3

### Docker
```bash
docker build -t dara-webapp .
docker run -p 3000:3000 --env-file .env dara-webapp
```

### PM2 (Production)
```bash
npm run build
pm2 start ecosystem.config.cjs
```

### Static Hosting (SSG)
```bash
npm run generate
# Deploy .output/public/ to Netlify, Cloudflare Pages, etc.
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make changes with clear commits
4. Ensure `npm run build` passes
5. Submit a Pull Request

### Code Style
- TypeScript strict mode
- Vue 3 Composition API with `<script setup>`
- Tailwind utility classes
- Component names in PascalCase
- Composables prefixed with `use`

---

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

---

## 👥 Authors

| Role | Name | GitHub |
|------|------|--------|
| **Frontend Lead** | Shakir Abdulrahim | [@shakir-abdo](https://github.com/shakir-abdo) |
| **Frontend** | Isra Hatim | [@EsraaGD](https://github.com/EsraaGD) |
| **Backend Lead** | Omar Abdulrahim | [@oovaa](https://github.com/oovaa) |
| **Backend** | Ahmed Ehab | [@ahmedehab0](https://github.com/ahmedehab0) |

---

## 🔗 Related Repositories

- **Backend API**: [github.com/oovaa/dara](https://github.com/oovaa/dara)
- **VS Code Extension**: [github.com/oovaa/hasanah](https://github.com/oovaa/hasanah)
- **Browser Extension**: [github.com/oovaa/hasanah-website](https://github.com/oovaa/hasanah-website)