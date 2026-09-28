<div align="center">

# OPHION 

### A curated technical knowledge vault

*Discover → Explore → Learn → Use → Share*

**Live site:** [ophion-tool-vault.grimmoir.workers.dev](https://ophion-tool-vault.grimmoir.workers.dev)

![Status](https://img.shields.io/badge/status-live-red)
![Next.js](https://img.shields.io/badge/Next.js-16-black)
![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-orange)
![Turso](https://img.shields.io/badge/Turso-libSQL-4FF8D2)
![Lighthouse](https://img.shields.io/badge/Lighthouse-99%20%7C%2096%20%7C%20100%20%7C%20100-brightgreen)

</div>

---

> **📄 About this repository**
> This is the **documentation and report repository** for Ophion. It is published for public viewing only. The source code lives in a separate private repository. See [License](#-license--copyright).

---

## 📖 Table of Contents

1. [What is Ophion?](#-what-is-ophion)
2. [The Story Behind the Name](#-the-story-behind-the-name)
3. [Core Content Types](#-core-content-types)
4. [Features](#-features)
5. [Tech Stack](#-tech-stack)
6. [Architecture](#-architecture)
7. [Security](#-security)
8. [Performance & Quality](#-performance--quality)
9. [Project Journey](#-project-journey)
10. [Roadmap](#-roadmap)
11. [Contact](#-contact)
12. [License & Copyright](#-license--copyright)

---

## 🔮 What is Ophion?

Ophion is a **curated, expandable technical knowledge vault**: one place to find useful software, practical technical tricks, and learning resources, with each entry reviewed before it is published.

It is not a random link dump. Every listing is organised into folders, tagged, and marked with a **trust level**, so a visitor can tell at a glance how much to rely on it.

**Visual identity:** red, black and white, with a technical-archive / digital-vault / gothic feel. Light and dark themes are both supported.

---

## 🕯️ The Story Behind the Name

The name comes from a personal mythology called **Paradox Genesis**.

Paradox Genesis is an organisation founded by those who believed in it: an existence known as *"The One Who Is Everything and Nothing"*. At its heart stand **seven beings, the Seven Paradoxes**.

**Ophion, the Blind Seer, is the Second Paradox.** He stands for one contradiction: *knowledge does not remove ignorance, and sometimes it only shows how vast it is.*

> *"I have seen everything, yet I have never seen myself."*

The vault follows the same path. It began small and scattered, learning to see outward before understanding itself, and it keeps growing toward **an archive of absolute knowledge**.

*The Blind Seer still watches.*

---

## 🗂️ Core Content Types

| Type | What it holds | Examples |
|---|---|---|
| 🛠️ **Tools** | Software, utilities, AI tools, productivity, design and security apps | Downloaders, dev tools, defensive security tools |
| ⚡ **Tricks** | Short technical tips, commands, workflows, shortcuts | Search operators, CLI commands, keyboard shortcuts |
| 📚 **Resources** | Tutorials, documentation, guides, repositories | Learning paths, reference docs, open-source repos |

Each type is grouped into **folders**. Empty folders are hidden from public view.

---

## ✨ Features

### For visitors
- **Browse** Tools, Tricks and Resources by folder
- **Global search** across names, titles, descriptions, categories and **tags**
- **Trust badges** on every tool: `Trusted` · `Verify` · `Untrusted`
- **Risk level** (`Low` / `Medium` / `High`) and **pricing** (`Free` · `Freemium` · `Paid` · `Open Source` · `Unknown`)
- **Light / dark theme**
- Works on phones, tablets and desktops
- Old links keep working when an item is moved between folders

### For members
- **Sign up / log in** with real accounts
- **Bookmarks** across all three content types
- **Submit** new tools, tricks and resources
- **My contributions:** edit or withdraw your own submissions
- **Profile:** avatar, password change, and account deletion
- **Duplicate detection** warns you before you submit something already in the vault

### For the administrator
- **Review queue:** approve, reject, or request changes to submissions
- Full **content management** for Tools, Tricks, Resources and Folders
- **User management**
- **Activity log** of moderation actions
- A second, separate **Manage Mode** password guarding every destructive action

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| **Framework** | Next.js (App Router) · React 19 · TypeScript |
| **Styling** | Tailwind CSS v4 |
| **Database** | Turso (libSQL / SQLite) with Drizzle ORM |
| **Image storage** | Cloudinary |
| **Email** | EmailJS (server-side only) |
| **Hosting** | Cloudflare Workers via OpenNext |
| **Auth** | bcryptjs · DB-backed sessions · httpOnly cookies |
| **Testing / QA** | Puppeteer against real Chrome · Lighthouse |

---

## 🏗️ Architecture

```
            Visitor / Member / Admin
                     │
                     ▼
        ┌──────────────────────────┐
        │   Cloudflare Workers     │   ← Next.js app (OpenNext)
        │  pages + API routes      │
        └──────┬─────────┬─────────┘
               │         │
     ┌─────────▼──┐  ┌───▼──────────┐  ┌────────────┐
     │   Turso    │  │  Cloudinary  │  │  EmailJS   │
     │ (database) │  │   (images)   │  │ (contact)  │
     └────────────┘  └──────────────┘  └────────────┘
```

**Routing**
- Tools use flat routes: `/tools/[slug]`
- Tricks and Resources use nested routes: `/tricks/[folder]/[slug]`, `/resources/[folder]/[slug]`

**Data model (main tables):** users · admins · sessions · folders · tools · tricks · resources · submissions · bookmarks · activity log · uploads · rate limits

**Submission workflow:** a member submits → duplicate check → admin reviews → approved items are published in a single database transaction, so a failed publish leaves the submission pending rather than half-done.

---

## 🛡️ Security

Security was treated as a feature, not an afterthought. It went through a structured audit, and every issue found was fixed and re-verified.

- **Passwords:** hashed with bcrypt; sessions are opaque random tokens, not decodable
- **Sessions:** httpOnly cookies, expiring, invalidated on password change
- **Two-level admin access:** admin login plus a separate Manage Mode password, **enforced on the server**, not just in the interface
- **Ownership checks:** users can only delete or replace images they own
- **Rate limiting:** a two-layer system on login (edge + database), and database-backed limits on signup, upload, contact and submissions. It keys by IP plus the login identifier, so one person's failures can't block others on a shared network
- **Input validation:** parameterised queries, upload type and size checks, XSS and protocol-injection rejection
- **Safe redirects:** strict validation on post-login redirects
- **Headers:** Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
- **Secrets:** server-side only, never in the browser bundle or in git

---

## 📊 Performance & Quality

Lighthouse results on the production site:

| Performance | Accessibility | Best Practices | SEO |
|:-:|:-:|:-:|:-:|
| **99** | **96** | **100** | **100** |

- Login runs in about 30 ms of CPU, well under Cloudflare's 50 ms limit
- A full click-through matrix of **31 routes × 4 roles** (visitor, user, admin with Manage Mode off, admin with Manage Mode on) passed 100%
- An internal link, 404 and canonical-tag crawl of the whole site came back clean
- TypeScript strict check and production build both pass

---

## 🧭 Project Journey

| Phase | What happened |
|---|---|
| **Design & build** | Full UI/UX with the gothic identity; tools, tricks, resources, submissions, admin panel |
| **Backend migration** | Moved from browser storage to a real database: Turso, real auth, API routes, Cloudinary |
| **Deployment** | Launched on Cloudflare Workers |
| **Hardening** | Fixed CPU-limit errors, then ran a full security audit and closed every finding |
| **Codebase audit** | Found and fixed leftover browser-storage bugs across the admin area |
| **Content loading** | Bulk import of curated entries, reviewed one by one |
| **Polish** | Logo choice, tag search, moved-content redirects, accessibility and motion |

---

## 🚀 Roadmap

- [ ] Large-scale load test (target: 1,000 simultaneous users)
- [ ] Google Search Console submission
- [ ] Custom domain
- [ ] Automatic image/favicon fetching for new entries
- [ ] Superuser role with per-feature permissions for trusted helpers
- [ ] Automated password reset

---

## 📬 Contact

**Support / permissions:** mr.grimmoir@gmail.com

---

## 📜 License & Copyright

© 2026 Rufus (Dineshkarthik N), Grimmoir. All rights reserved. See [LICENSE](LICENSE).

This repository is published for **viewing and reference only**. No part of this project, including its code, content, design, text, imagery and branding (the Ophion name and the Grimmoir crest), may be copied, reproduced or reused without prior written permission.

<div align="center">

*An Archive of Absolute Knowledge.*

**The Blind Seer still watches.** 👁️

</div>
