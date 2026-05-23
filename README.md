<div align="center">

# MC Crash Analyzer

**AI-Powered Minecraft Crash Report Analysis — Web Frontend**

[![Vue](https://img.shields.io/badge/Vue-3.5-4FC08D?logo=vuedotjs&logoColor=fff)](https://vuejs.org/)
[![Vite](https://img.shields.io/badge/Vite-5.4-646CFF?logo=vite&logoColor=fff)](https://vitejs.dev/)
[![Node](https://img.shields.io/badge/Node-%E2%89%A518-339933?logo=nodedotjs&logoColor=fff)](https://nodejs.org/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

</div>

---

## Overview

**MC Crash Analyzer** is a single-page web application that provides instant, AI-driven diagnosis of Minecraft crash reports. Users can upload crash log files or paste raw text, and within seconds receive a structured breakdown — error type, severity, root cause, and actionable solutions — all rendered in a clean developer-centric interface.

Built with Vue 3 Composition API and Vite, the frontend communicates with a Node.js backend over RESTful APIs. It supports file uploads, text input, user authentication, session persistence, and publicly shareable report links.

---

## Tech Stack

| Layer            | Technology                              |
| :--------------- | :-------------------------------------- |
| **Framework**    | [Vue 3.5](https://vuejs.org/) (Composition API, `<script setup>`) |
| **Bundler**      | [Vite 5](https://vitejs.dev/) (ESM-native, HMR) |
| **Routing**      | [Vue Router 4](https://router.vuejs.org/) (History mode) |
| **HTTP Client**  | [Axios](https://axios-http.com/) (interceptors, credentials) |
| **Typography**   | [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) + [IBM Plex Sans](https://fonts.google.com/specimen/IBM+Plex+Sans) |
| **Icons**        | Lucide-style SVG via Vue `h()` render functions |
| **Styling**      | Plain CSS with CSS custom properties (design tokens) |

---

## Project Structure

```
client/
├── index.html                    # SPA entry point, font preloading
├── vite.config.js                # Vite config with /api proxy
├── package.json
└── src/
    ├── main.js                   # createApp + router registration
    ├── App.vue                   # Shell: StickyHeader, RouterView, Footer, AuthModal
    ├── api/
    │   └── index.js              # All backend API calls (11 endpoints)
    ├── assets/
    │   ├── style.css             # Global design tokens & themed components
    │   └── icons.js              # 20+ SVG icon components (render-function based)
    ├── components/
    │   ├── AuthModal.vue         # Login/Register modal with form validation
    │   ├── CrashUploader.vue     # Drag-and-drop file upload zone
    │   ├── AnalysisResult.vue    # Bento-grid result cards (severity, cause, fix)
    │   └── HistoryList.vue       # Paginated history with relative timestamps
    ├── router/
    │   └── index.js              # Route definitions (2 routes)
    └── views/
        ├── Home.vue              # Main page: upload/paste tabs + result + history
        └── SharedReport.vue      # Public share view with TTL countdown
```

---

## Routing

| Path               | Component         | Auth Required | Description                     |
| :----------------- | :---------------- | :-----------: | :------------------------------ |
| `/`                | `Home`            |      No       | Upload, paste, analyze, history |
| `/crash/:shareId`  | `SharedReport`    |      No       | Public shared report with expiry |

---

## API Reference

All backend requests are proxied through `/api` in development. The `axios` instance is configured with `withCredentials: true` and a 120-second timeout for long-running AI analysis.

### Analysis Endpoints

| Method   | Path                    | Description              |
| :------- | :---------------------- | :----------------------- |
| `POST`   | `/api/analyze/file`     | Upload crash report file (multipart) |
| `POST`   | `/api/analyze/text`     | Submit crash report text |
| `GET`    | `/api/analyze/history`  | List user's analysis history |
| `GET`    | `/api/analyze/history/:id` | Get single analysis detail |
| `DELETE` | `/api/analyze/history/:id` | Delete history record |
| `GET`    | `/api/analyze/health`   | Health check |

### Auth Endpoints

| Method | Path                   | Description              |
| :----- | :--------------------- | :----------------------- |
| `POST` | `/api/auth/register`   | Create account           |
| `POST` | `/api/auth/login`      | Session login            |
| `POST` | `/api/auth/logout`     | Destroy session          |
| `GET`  | `/api/auth/me`         | Get current user         |
| `GET`  | `/api/auth/wechat/qrcode` | WeChat OAuth QR code  |
| `GET`  | `/api/auth/wechat/status`  | Poll WeChat login status |

### Share Endpoint

| Method | Path                  | Description             |
| :----- | :-------------------- | :---------------------- |
| `GET`  | `/api/crash/:shareId` | Fetch public shared report |

---

## Getting Started

### Prerequisites

- **Node.js** >= 18.0.0
- **npm** >= 9.0.0
- Backend service running at `http://localhost:3000`

### Installation

```bash
git clone https://github.com/JeffreyMing2004/Crash-Report-Web.git
cd Crash-Report-Web/client
npm install
```

### Development

```bash
npm run dev
```

The dev server starts at `http://localhost:5173` with Hot Module Replacement. All `/api/*` requests are automatically proxied to `http://localhost:3000`.

### Production Build

```bash
# Build for production
npm run build

# Preview the built output locally
npm run preview
```

The output is in `dist/` — a static bundle ready for any HTTP server or CDN.

---

## Component Reference

### `<CrashUploader>`

File upload zone with drag-and-drop support.

| Prop      | Type      | Default | Description              |
| :-------- | :-------- | :------ | :----------------------- |
| `loading` | `Boolean` | `false` | Disables interaction during analysis |

| Event     | Payload  | Description                 |
| :-------- | :------- | :-------------------------- |
| `analyze` | `File`   | Emitted when user triggers analysis |

### `<AnalysisResult>`

Structured display of AI analysis output.

| Prop     | Type     | Description              |
| :------- | :------- | :----------------------- |
| `result` | `Object` | Full analysis response from backend |

| Event   | Description                |
| :------ | :------------------------- |
| `close` | Dismiss the result display |

**`result` object shape:**

```typescript
interface AnalysisResult {
  fileName: string;
  analyzedAt: string;           // ISO 8601
  parsed?: {
    errorType?: string;
    errorMessage?: string;
    time?: string;
    javaVersion?: string;
    memory?: { heap?: string; allocated?: string };
    modCount?: number;
    stackTracePreview?: string[];
  };
  analysis?: {
    severity?: 'critical' | 'high' | 'medium' | 'low';
    summary?: string;
    rootCause?: string;
    solutions?: string[];
    relatedMods?: string[];
    technicalDetails?: string;
    estimatedFixTime?: string;
  };
}
```

### `<HistoryList>`

Analysis history with relative timestamps and severity indicators.

| Prop      | Type      | Description              |
| :-------- | :-------- | :----------------------- |
| `items`   | `Array`   | History records from API |
| `loading` | `Boolean` | Loading state            |

| Event     | Payload      | Description            |
| :-------- | :----------- | :--------------------- |
| `view`    | `id: string` | User clicked a record  |
| `delete`  | `id: string` | User requested delete  |
| `refresh` | —            | Manual refresh trigger |

### `<AuthModal>`

Login/Register modal with client-side validation.

| Prop      | Type      | Description              |
| :-------- | :-------- | :----------------------- |
| `visible` | `Boolean` | Controls modal visibility |

| Event           | Payload        | Description               |
| :-------------- | :------------- | :------------------------ |
| `close`         | —              | Modal dismissed           |
| `login-success` | `User: Object` | Successful authentication |

---

## Design System

The UI follows an **OLED Dark + Vibrant Block-based** design language optimized for developer tools.

| Token               | Value      | Usage              |
| :------------------ | :--------- | :----------------- |
| `--bg-deep`         | `#020617`  | Page background    |
| `--bg`              | `#0F172A`  | Section background |
| `--bg-card`         | `#1E293B`  | Card / modal bg    |
| `--primary`         | `#22C55E`  | CTA, accents, glow |
| `--text`            | `#F8FAFC`  | Primary text       |
| `--text-secondary`  | `#94A3B8`  | Secondary text     |
| `--text-muted`      | `#64748B`  | Placeholder / hint |
| `--font-mono`       | JetBrains Mono | Headings, code, badges |
| `--font-sans`       | IBM Plex Sans  | Body text, forms, UI |

### Accessibility

- All interactive elements have visible `:focus-visible` outlines
- `prefers-reduced-motion` is respected globally
- Color contrast meets WCAG AA (4.5:1 minimum)
- Icons are inline SVG, rendered via `h()` — no external icon font dependencies

### Responsive Breakpoints

| Breakpoint | Target          |
| :--------- | :-------------- |
| 375px      | Small phones    |
| 640px      | Large phones    |
| 768px      | Tablets         |
| 1024px     | Small desktops  |
| 1440px     | Large desktops  |

---

## Icon System

All icons are Lucide-style SVGs implemented as Vue functional components using the `h()` render function — no runtime template compiler required. This keeps the bundle lean and avoids external icon dependencies.

```js
// Example usage
import { IconUpload, IconSparkles } from './assets/icons.js';
```

Available icons: `IconAlertCircle`, `IconAlertTriangle`, `IconBarChart3`, `IconBookOpen`, `IconBot`, `IconClipboard`, `IconClock`, `IconFileText`, `IconFolderOpen`, `IconLightbulb`, `IconLogIn`, `IconLogOut`, `IconPuzzle`, `IconRefreshCw`, `IconScrollText`, `IconSearch`, `IconSettings`, `IconShare`, `IconSparkles`, `IconTarget`, `IconTrash2`, `IconUpload`, `IconUser`, `IconX`.

---

## Deployment

### Static Hosting

This is a pure SPA with client-side routing. Deploy the `dist/` directory to any static host:

```bash
npm run build
# Upload dist/ to your CDN or web server
```

**Important:** Configure your web server to serve `index.html` for all routes (SPA fallback) to support Vue Router history mode.

**Nginx example:**

```nginx
location / {
    try_files $uri $uri/ /index.html;
}
```

---

## License

MIT © 2024
