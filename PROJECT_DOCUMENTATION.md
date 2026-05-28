# Resumind - AI-Powered Resume Analyzer

Resumind is a production-grade, decentralized, and serverless AI-powered Resume Analyzer built on **React Router v7**, **Vite**, and **Tailwind CSS**, powered entirely by **Puter.js** for cloud features. It offers job seekers instant, intelligent feedback on how well their resume aligns with specific job descriptions, highlighting matching skills, identifying gaps, and calculating an ATS compatibility score.

---

## 🚀 Key Features

1. **AI-Driven Resume Analysis**: Matches resume content against job titles and descriptions using advanced language models (e.g., Claude 3.7 Sonnet) to deliver contextual insights.
2. **ATS Compatibility Scoring**: Generates an automated score based on resume structure, content, and job alignment, complete with action-oriented tips for improvement.
3. **Interactive Visual Dashboard**: A modern, responsive user interface where applicants can track all uploaded resumes, target roles, and performance scores.
4. **On-the-Fly PDF Rendering**: Converts PDF resumes to images on the client side using `pdfjs-dist` to provide a visual preview.
5. **Decentralized Storage & Auth**: Leverages Puter's secure sandboxed cloud filesystem (`puter.fs`) and passwordless OAuth (`puter.auth`) to operate entirely backend-free.
6. **Key-Value Metadata Sync**: Persists historical analysis and application details in Puter's fast Key-Value database (`puter.kv`).
7. **Developer/Admin Reset**: Includes a clean administrative path (`/wipe`) to wipe user storage and reset KV databases.

---

## 🛠️ Complete Tech Stack

### Frontend & Core Architecture
- **Framework**: [React Router v7](https://reactrouter.com/) (formerly Remix, leveraging the file-system router & modern React features)
- **Build Tool**: [Vite 6](https://vite.dev/)
- **State Management**: [Zustand](https://zustand.docs.pmnd.rs/) (for global state sync with Puter.js services)
- **Styling**: [Tailwind CSS v4](https://tailwindcss.com/) (with custom styling system in `app.css` & glassmorphism filters)
- **Language**: [TypeScript](https://www.typescriptlang.org/) (Strictly typed schemas for components, state, and API structures)

### PDF Processing
- **Library**: `pdfjs-dist` (for client-side PDF loading, parsing, and rendering to canvas/images)

### Backend-as-a-Service (BaaS) / Serverless Layer (Puter.js)
- **Identity & Authentication**: `puter.auth` (integrated OAuth, sign-in, sign-out, session handling)
- **Cloud File System**: `puter.fs` (securely saves original PDFs and rendered preview images)
- **Storage & Database**: `puter.kv` (structured JSON document persistence mapped via UUIDs)
- **Artificial Intelligence**: `puter.ai.chat` & `puter.ai.feedback` (calling cutting-edge models like `claude-3-7-sonnet` to execute structured visual and text analysis)

---

## 📂 Codebase Directory Layout

```
.
├── app/
│   ├── components/       # Reusable UI components (Score gauges, Accordion, Resume cards, navbar, etc.)
│   │   ├── ATS.tsx       # Renders the ATS score badge and tips
│   │   ├── Accordion.tsx # Collapsible detailed review section
│   │   ├── Details.tsx   # Detailed analysis of keywords, styling, and sections
│   │   ├── Navbar.tsx    # Header with Auth trigger
│   │   ├── ResumeCard.tsx# Mini summary card on dashboard
│   │   └── Summary.tsx   # AI-generated key takeaways
│   ├── lib/              # Core libraries & integrations
│   │   ├── pdf2img.ts    # Client-side PDF page 1 to image converter
│   │   ├── puter.ts      # Zustand wrapper for Puter.js SDK APIs
│   │   └── utils.ts      # General utility helpers (UUID generator)
│   ├── routes/           # File-system routes
│   │   ├── auth.tsx      # Authentication screen
│   │   ├── home.tsx      # Main application dashboard
│   │   ├── resume.tsx    # Detailed resume analysis view
│   │   ├── upload.tsx    # Multi-step resume details submit form
│   │   └── wipe.tsx      # Admin data cleanup utility
│   ├── app.css           # Global Tailwind & Custom Styles
│   ├── root.tsx          # Root Layout & Puter SDK injector script
│   └── routes.ts         # Route configuration
├── constants/            # Prompts and system instruction templates
├── public/               # Static assets (images, icons, etc.)
├── package.json          # Dependency and script manager
├── tsconfig.json         # TypeScript configuration
└── vite.config.ts        # Vite environment configurator
```

---

## ⚙️ How It Works (Sequence of Operations)

1. **User Auth**: Upon visiting, the app checks if the user is authenticated via `puter.auth.isSignedIn()`. Unauthenticated users are redirected to `/auth`.
2. **Details & File Upload**: In `/upload`, the user fills in the Job Title, Job Description, and uploads their PDF.
3. **Client-side PDF-to-Image**: `pdfjs-dist` converts page 1 of the PDF into a Blob (PNG image) on the client side.
4. **Cloud Storage Sync**:
   - The original PDF is uploaded to `fs.upload()`.
   - The converted PNG is uploaded to `fs.upload()`.
5. **Persist Context**: A JSON record containing file paths, job metadata, and UUID is written to `kv.set()`.
6. **AI Analysis**: The app requests analysis using `ai.feedback()`, feeding the Claude model with:
   - The uploaded PDF file path.
   - Tailored system prompt instructing it to return valid structured JSON feedback.
7. **Redirection & Rendering**: The JSON feedback is saved back to the KV store, and the user is redirected to `/resume/:uuid`, which loads and presents the analysis beautifully using interactive gauges and metrics.
