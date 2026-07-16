# CipherSense

> A production-quality cybersecurity password intelligence dashboard — built for developers and security-conscious users who want real insight into password strength, not just a colored bar.

![CipherSense Dashboard](https://img.shields.io/badge/Status-Live-brightgreen?style=flat-square) ![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square) ![Privacy](https://img.shields.io/badge/Privacy-100%25%20Client--Side-cyan?style=flat-square)

---

## Overview

CipherSense analyzes passwords in real time using cryptographic principles and security heuristics — entirely in your browser. No passwords are ever transmitted, stored, or logged. The only external request is a privacy-preserving breach check using the HaveIBeenPwned k-Anonymity model.

Built as a portfolio-grade cybersecurity tool that demonstrates real security engineering concepts, not just UI chrome around a string-length check.

---

## Screenshots

> _Dashboard — real-time analysis with animated score gauge_

![Dashboard Screenshot](https://placehold.co/1200x600/0d1117/00b4d8?text=CipherSense+Dashboard)

> _Password Generator — cryptographically secure with character roll animation_

![Generator Screenshot](https://placehold.co/1200x600/0d1117/00b4d8?text=Password+Generator)

> _Educational Section — expandable concept cards_

![Learn Screenshot](https://placehold.co/1200x600/0d1117/00b4d8?text=Educational+Cards)

---

## Features

### Real-Time Password Analysis
- **Strength Score** (0–100) — composite score using length, charset diversity, entropy, pattern penalties, and common-password checks
- **Animated Circular Gauge** — smooth color-transitioning SVG speedometer (Very Weak → Very Strong)
- **Security Grade** — A+ through F with color-coded display

### Entropy & Search Space
- Entropy calculated as `length × log₂(charsetSize)`
- Estimated combinations displayed in scientific notation
- Complexity level: Low / Medium / High / Extreme

### Crack Time Estimation
Five real-world attack scenarios:

| Scenario | Hash Rate |
|---|---|
| Consumer Laptop | 1M/s |
| Gaming PC | 1B/s |
| High-end GPU | 10B/s |
| GPU Cluster | 100B/s |
| Offline Attack | 1T/s |

Times displayed from "Instant" to "Millions of years".

### Pattern Detection
Automatically flags:
- Sequential numbers (`123456`, `654321`)
- Sequential letters (`abcdef`, `zyxwvu`)
- Keyboard walks (`qwerty`, `asdf`, `zxcv`, `qazwsx`)
- Common passwords and dictionary words
- Repeated character groups (`aaa`, `111`)
- Birthdate-like patterns (`DDMMYYYY`, `YYYYMMDD`)

### HaveIBeenPwned Breach Checker
Implements the **k-Anonymity model**:
1. SHA-1 hash computed in-browser via the Web Crypto API
2. Only the **first 5 hex characters** are sent to the HIBP API
3. Returned hash suffixes are compared locally
4. The full password and full hash never leave the browser

### Character Checklist
Live checklist covering length, uppercase, lowercase, digits, symbols, entropy threshold, pattern-free, and common-password checks — with animated check/cross transitions.

### Password Generator
- Cryptographically secure via `crypto.getRandomValues`
- Length slider: 8–64 characters
- Toggles: Uppercase, Lowercase, Numbers, Symbols
- Exclude ambiguous characters (`0`, `O`, `l`, `I`, `1`)
- Character rolling animation on generate
- One-click clipboard copy

### Educational Section
Ten expandable cards covering:
- Password Entropy
- SHA-1 Hashing
- k-Anonymity
- Brute Force Attacks
- Dictionary Attacks
- Rainbow Tables & Salting
- Credential Stuffing
- Password Managers
- Passphrases
- Multi-Factor Authentication

### Three Themes
| Theme | Description |
|---|---|
| **Dark** | Deep navy, glassmorphism cards, subtle glows |
| **Cyber** | Near-black with electric cyan/neon accents |
| **High Contrast** | Maximum contrast, accessibility-first |

Theme preference is persisted in `localStorage`.

---

## Technology Stack

| Layer | Technology |
|---|---|
| Framework | React 18 + Vite |
| Styling | Tailwind CSS v4, CSS custom properties |
| Animations | Framer Motion |
| Charts | Recharts |
| Icons | Lucide React |
| Routing | Wouter |
| Crypto | Web Crypto API (native browser) |
| Breach API | HaveIBeenPwned Passwords API (k-Anonymity) |
| Build | Vite + TypeScript |

**No backend. No database. No accounts. No tracking.**

---

## Privacy

Everything in CipherSense runs locally in your browser:

- Passwords are **never stored** anywhere
- Passwords are **never logged** to the console or any service
- Passwords are **never transmitted** over the network
- The only external call is the HIBP breach check, which sends **only the first 5 characters of the SHA-1 hash** — making it mathematically impossible to reconstruct the original password from the request

### How k-Anonymity Works

```
Your password ──► SHA-1 hash ──► First 5 hex chars ──► HIBP API
                                                              │
                                    Local comparison ◄── Returns ~500 suffixes
                                          │
                          "Found in X breaches" or "Not found"
```

Your full password and full hash never leave the browser at any point.

---

## Installation

```bash
# Clone the repository
git clone https://github.com/KS22-bot/CipherSense.git
cd CipherSense

# Install dependencies (requires pnpm)
pnpm install

# Start the development server
pnpm --filter @workspace/ciphersense run dev
```

The app will be available at `http://localhost:<PORT>`.

---

## Deployment to GitHub Pages

CipherSense is a fully static application and can be deployed anywhere static files are served.

### GitHub Pages

1. Build the app:
   ```bash
   pnpm --filter @workspace/ciphersense run build
   ```
2. The output is in `artifacts/ciphersense/dist/public/`
3. Deploy the contents of that folder to GitHub Pages via your preferred method (GitHub Actions, `gh-pages` branch, etc.)

### GitHub Actions (example workflow)

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
        with:
          version: 9
      - run: pnpm install --frozen-lockfile
      - run: pnpm --filter @workspace/ciphersense run build
      - uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: artifacts/ciphersense/dist/public
```

### Other Static Hosts

| Host | Command |
|---|---|
| Netlify | Drop `dist/public/` folder into Netlify dashboard |
| Vercel | `vercel --cwd artifacts/ciphersense` |
| Cloudflare Pages | Connect repo, set build output to `artifacts/ciphersense/dist/public` |

---

## Project Structure

```
artifacts/ciphersense/src/
├── lib/
│   ├── strength.ts      # Composite strength scoring (0–100)
│   ├── entropy.ts       # Entropy estimation in bits
│   ├── cracktime.ts     # Crack time across attack scenarios
│   ├── hibp.ts          # HaveIBeenPwned k-Anonymity checker
│   ├── patterns.ts      # Keyboard walks, sequences, common words
│   └── generator.ts     # Cryptographically secure password generator
├── components/
│   ├── Nav.tsx           # Sticky navigation + theme toggle
│   ├── ScoreGauge.tsx    # Animated SVG circular gauge
│   ├── ChecklistPanel.tsx
│   ├── CrackTimeTable.tsx
│   ├── BreachChecker.tsx
│   ├── PatternPanel.tsx
│   ├── SecurityTips.tsx
│   ├── WidgetCard.tsx
│   └── ThemeProvider.tsx # Theme context + localStorage persistence
└── pages/
    ├── Dashboard.tsx     # Main password analysis page
    ├── Generator.tsx     # Password generator
    ├── Learn.tsx         # Educational accordion cards
    └── Privacy.tsx       # Privacy model explanation
```

---

## Accessibility

- Full keyboard navigation
- ARIA labels on all interactive elements
- Focus-visible indicators
- High Contrast theme for low-vision users
- Screen reader-friendly structure

---

## Future Improvements

- [ ] Passphrase generator (Diceware-style)
- [ ] Password history comparison (local-only, `localStorage`)
- [ ] Export analysis report as PDF
- [ ] Offline PWA support (Service Worker)
- [ ] zxcvbn integration for machine-learning-based strength estimation
- [ ] Multiple language support for educational content
- [ ] Animated k-Anonymity explainer visualization

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Acknowledgements

- [HaveIBeenPwned](https://haveibeenpwned.com/) by Troy Hunt for the k-Anonymity Passwords API
- [Lucide Icons](https://lucide.dev/) for the icon set
- [Framer Motion](https://www.framer.com/motion/) for animations
- [Recharts](https://recharts.org/) for the character diversity chart
