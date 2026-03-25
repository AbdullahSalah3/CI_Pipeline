<div align="center">

# CI Pipeline — Simple Node.js App

**A simple Node.js app with an automated GitHub Actions CI Pipeline.**  
Runs automatically on every push and pull request to `master`.

[![CI Pipeline](https://github.com/AbdullahSalah3/CI_Pipeline/actions/workflows/ci.yml/badge.svg)](https://github.com/AbdullahSalah3/CI_Pipeline/actions)
[![Node.js](https://img.shields.io/badge/Node.js-20.x-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

---

## What This Project Does

This project demonstrates a complete **Continuous Integration (CI)** workflow using **GitHub Actions**.

Every time code is pushed or a pull request is opened against `master`, the pipeline automatically:
- Checks out the latest code
- Caches npm dependencies for faster builds
- Sets up Node.js 20
- Installs dependencies (only on cache miss)
- Runs the test suite

If the test fails, the pipeline fails — keeping broken code out of `master`.

---

## Project Structure

```
ci-pipeline/
├── .github/
│   └── workflows/
│       └── ci.yml        # GitHub Actions pipeline
├── src/
│   ├── app.js            # Main app — greet(name) function
│   ├── test.sh           # Bash test script
│   └── styles.css        # Frontend styles
├── index.html            # Main HTML page
└── package.json
```

---

## Getting Started

### Prerequisites
- Node.js 20+
- npm
- Git

### Installation

```bash
git clone https://github.com/AbdullahSalah3/CI_Pipeline.git
cd CI_Pipeline
npm install
```

### Run Tests

```bash
npm test
```

Expected output:
```
Test passed!
```

---

## CI Pipeline

The pipeline is defined in `.github/workflows/ci.yml` and runs on:

| Trigger | Branch |
|---------|--------|
| `push` | `master` |
| `pull_request` | `master` |

### Pipeline Steps

```
Push / Pull Request to master
          │
          ▼
   Checkout Code
          │
          ▼
   Cache Dependencies
          │
     Cache Hit?
    ┌─────┴─────┐
   YES          NO
    │            │
    │     Install Dependencies
    │            │
    └─────┬──────┘
          │
          ▼
   Set up Node.js 20
          │
          ▼
      Run Tests
          │
     Pass or Fail?
    ┌─────┴─────┐
   ✅ Pass    ❌ Fail
  PR merges   PR blocked
```

---

## The Bug That Was Fixed

The original `app.js` used single quotes instead of backticks:

```js
// ❌ Before — broken
return 'Hello, ${name}!';   // outputs: Hello, ${name}!

// ✅ After — fixed
return `Hello, ${name}!`;   // outputs: Hello, Test!
```

This caused the CI test to always fail because `${name}` was returned as a literal string.

---

## License

MIT License — feel free to use and modify.

---

<div align="center">
Built with Node.js and GitHub Actions
</div>
