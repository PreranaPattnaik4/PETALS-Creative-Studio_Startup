# PETALS Studio — Project Report

**Project Name:** PETALS Studio  
**Tagline:** *Crafting illustrated stories that unfold like petals, blooming layer by layer.*  
**Framework:** Next.js 15.5.9 (App Router) + React 19 + TypeScript  
**AI Platform:** Firebase Genkit (`@genkit-ai/google-genai`)  

---

## 1. Executive Summary

PETALS Studio is a responsive web platform for an illustration-first fantasy storytelling, publishing, and animation studio. The application showcases original storybooks, character lore, an interactive masonry art gallery, animated video previews, and a creative studio suite (including an interactive Poster & Wall Art Creator with PNG export and a roadmap of Genkit-powered AI tools).

---

## 2. Technology Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Core Framework** | Next.js `15.5.9` (App Router), React `19.2.1`, TypeScript `5` | Routing, SSR/CSR rendering, and strict type safety |
| **Styling & UI** | Tailwind CSS `3.4.1`, `tailwindcss-animate`, Radix UI / Shadcn UI | Design system, accessible modals, accordions, forms, and toasts |
| **Motion & Icons** | Framer Motion `11.11.17`, Lucide React `0.475.0` | Scroll reveals, animated transitions, and iconography |
| **Canvas Export** | `html-to-image` `1.11.11` | Exporting composed posters as PNG downloads |
| **AI Orchestration** | Genkit `1.28.0`, `@genkit-ai/google-genai`, Zod `3.24.2` | Server-side flows for story concepts, lore, art descriptions, and image generation |
| **Hosting Config** | `next.config.ts` (`output: 'standalone'`), `apphosting.yaml` | Production standalone server & Firebase App Hosting readiness |

---

## 3. Architecture & File Structure

```text
src/
├── ai/
│   ├── genkit.ts                        # Genkit initialization (googleai/gemini-2.5-flash)
│   ├── dev.ts                           # Local Genkit dev server entry
│   └── flows/
│       ├── generate-art-description-flow.ts
│       ├── generate-creator-art-flow.ts # Wall art, birthday card, personalized card flows
│       ├── generate-lore-flow.ts
│       └── generate-story-concepts-flow.ts
├── app/
│   ├── layout.tsx                       # Root layout, Google Fonts, ScrollToTop, Toaster
│   ├── globals.css                      # Tailwind base + glass-morphism utilities
│   ├── page.tsx                         # Home landing page
│   ├── books/page.tsx                   # Storybook showcase & featured lore modal
│   ├── videos/page.tsx                  # Filterable animation library & video modal player
│   ├── creator/page.tsx                 # AI Creative Suite roadmap + PosterCreator
│   ├── gallery/page.tsx                 # ArtGallery + CharacterShowcase
│   ├── characters/page.tsx              # Client-side redirect to /gallery
│   ├── about/page.tsx                   # Studio mission, vision, audience & offerings
│   ├── contact/page.tsx                 # Contact form & newsletter subscription
│   ├── customer-survey/page.tsx         # Reader feedback survey
│   ├── faqs/page.tsx                    # Accordion FAQ page
│   ├── support/page.tsx                 # Help & Support directory
│   ├── patents/page.tsx                 # Intellectual property & patents notice
│   ├── privacy/page.tsx                 # Privacy Policy
│   ├── terms/page.tsx                   # Terms of Service
│   └── lib/placeholder-images.json      # Curated image asset catalog (48 entries)
├── components/
│   ├── Petals/                          # Domain components (Hero, Navbar, Footer, PosterCreator, etc.)
│   └── ui/                              # Reusable Shadcn/Radix UI primitives
├── hooks/
│   ├── use-mobile.tsx
│   └── use-toast.ts
└── lib/
    ├── placeholder-images.ts            # Typed wrapper over placeholder-images.json
    └── utils.ts                         # cn() className utility
```

---

## 4. Key Features & Modules

### 4.1 Editorial Landing Experience (`/`)
- **Cinematic Hero (`Hero.tsx`)**: Full-viewport hero with radial edge blur, floating petal particles (`FloatingPetals.tsx`), and smooth-scroll anchor.
- **Studio Story & Mission**: Brand introduction, four-pillar Studio Expertise grid (Storybooks, Animation, Character Design, Kids Entertainment), and a *Stories Woven with Stardust* narrative block.
- **Creator Studio Preview & Dreamweaver Portal (`CreatorIntro.tsx`, `DreamweaverPortal.tsx`)**: Showcases upcoming AI creative tools and a "Coming Soon" teaser for story concept generation.
- **Social Sharing (`ShareSection.tsx`) & Collection Grid**: Copy-to-clipboard link sharing, multi-platform share intents, and featured storybook previews.

### 4.2 Interactive Poster & Wall Art Creator (`/creator`, `PosterCreator.tsx`)
- **Manual Designer Mode**:
  - 10 decorative stickers (`sticker-*`) draggable on a `3:4` canvas via Framer Motion.
  - Background preset selector plus custom file upload (`.jpg`, `.jpeg`, `.png`, `.webp`, max 5 MB) with client-side canvas downscaling up to 2500px.
  - Editable poster title and lore snippet overlay.
  - 20-step Undo / Redo history stack.
  - High-resolution PNG download via `html-to-image` (`toPng`).
- **AI Studio Modes (Coming Soon UI)**: Placeholders for *Lore Weaver*, *Wall Art*, and *Birthday Card* (signature & personalized photo card) backed by server-side Genkit flows in `src/ai/flows/`.

### 4.3 Portfolio & Content Pages
- **Books (`/books`)**: 6-card thematic feature grid, featured *RoseBella & Lunaria* lore dialog, and an 8-book collection grid with ratings and purchase/community actions.
- **Videos (`/videos`)**: Category filter (`All`, `Short Films`, `Character Promos`, `Story Teasers`) and modal dialog supporting embedded HTML5 `<video>` playback as well as "Coming Soon" previews.
- **Gallery (`/gallery`)**: Category-filtered masonry layout (`All`, `Characters`, `Portraits`, `Watercolor`, `Landscapes`), full-screen artwork lore dialogs, and `CharacterShowcase` profiles for *Alora*, *RoseBella*, *Nerina*, and *Vespera*.
- **Studio Info & Legal**: `/about`, `/contact`, `/customer-survey`, `/faqs`, `/support`, `/patents`, `/privacy`, and `/terms`.

### 4.4 AI Flows (`src/ai/flows/`)
All flows run server-side (`'use server'`) using `ai` configured with `GOOGLE_GENAI_API_KEY`:
- `generateStoryConcepts`: Generates 1–5 structured story concepts (`title`, `plotOutline`) via `googleai/gemini-2.5-flash`.
- `generateLore`: Generates a poetic short story (`title`, `content`) from a concept string.
- `generateArtDescription`: Produces a single-paragraph poetic lore snippet from art keywords.
- `generateWallArt` & `generateBirthdayCard`: Uses `googleai/imagen-4.0-fast-generate-001` for studio-branded illustrations.
- `generatePersonalizedBirthdayCard`: Uses `googleai/gemini-2.5-flash-image` with an uploaded photo data URI and recipient name.

---

## 5. Design System

- **Color Palette (`tailwind.config.ts`, `globals.css`)**:
  - Pearl White (`#FFF9F5`) canvas background
  - Camel Rose / Rose Pink (`#A67B5B`) & Rose Gold (`#C19A6B`) accents
  - Blush Peach (`#E5C9A1`), Soft Lavender (`#DCCFFF`), Moonlight Blue (`#D9ECFF`), and Fairy Gold (`#F5D76E`)
- **Typography**:
  - `Playfair Display` (`font-headline`) for editorial serif headings and lore prose
  - `PT Sans` (`font-body`) for clean UI copy
  - `Pinyon Script` (`font-script`) for decorative calligraphic highlights

---

## 6. Issues Identified & Resolved

| Area | Issue | Resolution |
| :--- | :--- | :--- |
| **Hydration (`src/app/layout.tsx`)** | Browser extension attributes on `<body>` triggered React hydration mismatch errors | Added `suppressHydrationWarning` to `<body>` |
| **Hydration (`sidebar.tsx`, `Footer.tsx`, `SidebarNav.tsx`)** | `Math.random()` in `SidebarMenuSkeleton` and `new Date().getFullYear()` risked SSR/client mismatch | Replaced `Math.random()` with deterministic `"70%"`; added `suppressHydrationWarning` to year text |
| **TypeScript (`generate-creator-art-flow.ts`)** | Referenced undefined `GeneratePersonalizedBirthdayCardInput` | Fixed to `GeneratePersonalizedCardInput` |
| **TypeScript (`PosterCreator.tsx`)** | React 19 `RefObject<HTMLDivElement \| null>` mismatch on `downloadAsset`, `(window as any).Image` cast, unguarded empty `bgImage` | Updated parameter type, used `window.Image`, and guarded `<Image>` render |
| **TypeScript (`calendar.tsx`)** | `IconLeft`/`IconRight` incompatible with `react-day-picker` v9 | Migrated to v9 `Chevron` custom component |
| **Accessibility (`books/page.tsx`, `ArtGallery.tsx`)** | Missing `DialogTitle` inside `DialogContent` caused Radix UI runtime console errors | Added `DialogTitle` (`sr-only`) to all modal dialogs |
| **Forms (`contact/page.tsx`, `customer-survey/page.tsx`)** | Forms lacked `onSubmit` handlers, causing full-page reloads on submit | Added controlled `onSubmit` handlers with `useToast` confirmation |
| **Linting & Static Assets** | Missing ESLint config/unescaped apostrophes; `favicon.ico` path | Configured `.eslintrc.json` (`eslint@8.57.0` + `eslint-config-next@15.5.9`), escaped JSX apostrophes, moved `favicon.ico` to `public/` |

---

## 7. Verification & Current Status

- **`npm run typecheck` (`tsc --noEmit`)**: Passed with 0 errors.
- **`npm run lint` (`next lint`)**: Passed with 0 errors (1 informational warning regarding Google Fonts `<link>` in `app/layout.tsx`).
- **`npm run build` (`next build`)**: Compiled cleanly (`output: 'standalone'`).

---

## 8. Recommended Next Steps

1. **Connect Backend Persistence**: Wire the `/contact` and `/customer-survey` forms (and newsletter signups) to Firestore or an email/CRM API endpoint so submissions are persisted rather than only acknowledged via toast.
2. **Enable Live AI Modes**: Connect the `DreamweaverPortal` and `PosterCreator` AI tabs to the existing Genkit server actions (`generateStoryConcepts`, `generateLore`, `generateWallArt`, `generateBirthdayCard`, `generatePersonalizedBirthdayCard`) once `GOOGLE_GENAI_API_KEY` is provisioned in the deployment environment.
3. **Replace Placeholder Links & Media**: Update `#` placeholder URLs for social links and Amazon/WhatsApp book buttons, and populate remaining video entries in `/videos` with final media URLs as they are produced.
4. **Consolidate Image JSON**: Remove the unused empty `src/lib/placeholder-images.json` file (the app imports from `src/app/lib/placeholder-images.json`) to avoid confusion.
5. **Enforce Build Checks**: Consider removing `ignoreBuildErrors: true` and `ignoreDuringBuilds: true` from `next.config.ts` now that typecheck and lint pass cleanly.
