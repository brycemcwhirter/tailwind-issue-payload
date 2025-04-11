# 🐛 Payload + TailwindCSS Init Failure Reproduction

This repository reproduces a persistent issue encountered when attempting to install and initialize **TailwindCSS** in a fresh [Payload CMS](https://payloadcms.com/) app using either `npm` or `pnpm`.

---

## 📦 Tech Stack

- Payload CMS (blank template)
- Node.js `v20.11.1`
- npm `v10.9.0`
- macOS (Darwin 24.1.0)

---

## ❗ The Problem

Running `npx tailwindcss init -p` results in errors such as:

- `could not determine executable to run`
- `Cannot read properties of null (reading 'matches')`
- Tailwind binary (`node_modules/.bin/tailwindcss`) is **never created**

This happens regardless of whether Tailwind v4.1.3 or v3.3.5 is used.

---

## 🧪 Steps to Reproduce

```bash
# 1. Create a fresh Payload app
pnpx create-payload-app@latest -t blank tailwind-repro
cd tailwind-repro

# 2. Install TailwindCSS and its dependencies
npm install -D tailwindcss postcss autoprefixer

# 3. Attempt to initialize Tailwind
npx tailwindcss init -p
```

> This command fails with:  
> `npm ERR! could not determine executable to run`

You may also see:

```bash
zsh: command not found: tailwindcss
npm ERR! Cannot read properties of null (reading 'matches')
```

---

## 🔍 What We Tried

| Attempted Fix                                   | Outcome                                                                                  |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `pnpm add -D tailwindcss`                       | Installed, but `tailwindcss` binary not found                                            |
| `npx tailwindcss init -p`                       | Fails with `could not determine executable to run`                                       |
| `pnpm exec tailwindcss init -p`                 | Fails: `Command "tailwindcss" not found`                                                 |
| `./node_modules/.bin/tailwindcss init -p`       | File does not exist                                                                      |
| Global install (`npm install -g tailwindcss`)   | CLI not found even after global install                                                  |
| Downgraded Tailwind to `v3.3.5`                 | Still fails with a different error: `Cannot read properties of null (reading 'matches')` |
| Deleted `node_modules` + lockfile + reinstalled | Did not help                                                                             |
| Tried with both `npm` and `pnpm`                | Same issues across both                                                                  |

---

## 💡 Suspected Cause

- Incompatibility between `tailwindcss@4.x` and `npm@10+`
- Payload's dependency structure might interfere with binary resolution
- Possibly a `libnpmexec` bug in how binaries are linked

---

## 📂 Logs

All relevant debug logs are available and can be shared in an issue:

- Install failures
- Missing binary errors
- `npm` stack traces

Let us know if you'd like them included.

---

## 🙏 What We're Hoping For

We’d love help from the Payload CMS maintainers or community on:

- Verifying this bug across different environments
- Understanding if Payload is expected to support Tailwind out-of-the-box
- Any workarounds or changes needed to enable Tailwind in Payload + Next.js projects

Thanks for taking a look! 💙
