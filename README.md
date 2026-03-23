<div align="center">

<br/>

# ⚙️ CI Pipeline — Simple Node.js App

**A simple Node.js web project with a fully automated GitHub Actions CI Pipeline — runs on every push and pull request to `master`.**

<br/>

[![CI Pipeline](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)](../../actions)
[![Node.js](https://img.shields.io/badge/Node.js-20.x-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![npm](https://img.shields.io/badge/npm-package-CB3837?style=for-the-badge&logo=npm&logoColor=white)](https://www.npmjs.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

<br/>

[🔍 View Actions](../../actions) · [🐛 Report Bug](../../issues) · [✨ Request Feature](../../issues)

---

</div>

## 📋 Table of Contents

- [📖 Description](#-description)
- [📁 Project Structure](#-project-structure)
- [⚙️ CI Pipeline](#️-ci-pipeline)
  - [Triggers](#triggers)
  - [Pipeline Steps](#pipeline-steps)
  - [Pipeline Flow Diagram](#pipeline-flow-diagram)
- [🚀 Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Run Tests](#run-tests)
- [🌐 Project Files](#-project-files)
- [🤝 Contributing](#-contributing)
- [📜 License](#-license)
- [📬 Contact](#-contact)

---

## 📖 Description

**CI Pipeline** is a lightweight Node.js web project that demonstrates a complete **Continuous Integration (CI)** workflow using **GitHub Actions**.

Every time code is pushed or a pull request is opened against the `master` branch, the pipeline automatically:
- Checks out the latest code
- Caches `npm` dependencies for faster builds
- Sets up Node.js 20
- Installs dependencies (only if cache missed)
- Runs the test suite

This makes sure that broken code never reaches the `master` branch — the pipeline acts as an automated quality gate.

---

## 📁 Project Structure

```
ci-pipeline/
│
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions CI pipeline definition
│
├── src/
│   ├── app.js                  # Main Node.js application entry point
│   ├── styles.css              # Frontend styles (light blue background)
│   └── test.sh                 # Bash test script (run via npm test)
│
├── index.html                  # Main HTML page ("This is my Git project")
├── package.json                # Project metadata, version, and npm scripts
└── README.md
```

---

## ⚙️ CI Pipeline

The CI pipeline is defined in `.github/workflows/ci.yml`.

### Triggers

The pipeline runs automatically on **two events**:

| Event | Branch | When |
|-------|--------|------|
| `push` | `master` | Every time code is pushed directly to master |
| `pull_request` | `master` | Every time a PR is opened, updated, or reopened targeting master |

```yaml
on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
```

---

### Pipeline Steps

The job `build-and-test` runs on **Ubuntu Latest** (`ubuntu-latest`) and executes the following steps in order:

#### 1. 📥 Checkout Code
```yaml
- name: Checkout code
  uses: actions/checkout@v4
```
Clones the repository into the GitHub Actions runner so the rest of the steps can access the project files.

---

#### 2. 💾 Cache Dependencies
```yaml
- name: Cache dependencies
  uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```
Caches the `~/.npm` directory using a key based on the OS and a hash of `package-lock.json`.

- ✅ **Cache hit** → dependencies are restored instantly, `npm install` is skipped
- ❌ **Cache miss** → `npm install` runs and the cache is saved for the next run
- This significantly speeds up the pipeline on repeated runs

---

#### 3. 🟢 Set Up Node.js
```yaml
- name: Set up Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
```
Installs **Node.js version 20** on the runner — the version used to build and test the project.

---

#### 4. 📦 Install Dependencies
```yaml
- name: Install dependencies
  run: npm install
  if: steps.cache-dependencies.outputs.cache-hit != 'true'
```
Runs `npm install` **only if the cache was not hit** in step 2. This avoids redundant installs when all dependencies are already cached.

---

#### 5. 🧪 Run Tests
```yaml
- name: Run tests
  run: npm test
  if: success()
```
Executes `npm test`, which internally runs `bash src/test.sh`.

- Only runs **if all previous steps succeeded** (`if: success()`)
- If any earlier step fails, the test step is skipped automatically
- A failing test causes the entire pipeline to fail and blocks the PR from merging

---

### Pipeline Flow Diagram

```
Push / Pull Request to master
            │
            ▼
    ┌───────────────┐
    │ Checkout Code │  ← actions/checkout@v4
    └──────┬────────┘
           │
           ▼
    ┌──────────────────────┐
    │  Cache Dependencies  │  ← ~/.npm cached by package-lock.json hash
    └──────┬───────────────┘
           │
     Cache Hit?
      ┌────┴────┐
     YES        NO
      │          │
      │    ┌─────▼──────────────┐
      │    │ Install Dependencies│  ← npm install
      │    └─────┬──────────────┘
      │          │
      └────┬─────┘
           │
           ▼
    ┌──────────────────┐
    │  Set Up Node 20  │  ← actions/setup-node@v4
    └──────┬───────────┘
           │
           ▼
    ┌──────────────┐
    │  Run Tests   │  ← npm test → bash src/test.sh
    └──────┬───────┘
           │
     Tests Pass?
      ┌────┴────┐
     YES        NO
      │          │
      ▼          ▼
   ✅ Pass    ❌ Fail
  PR can      PR is
  merge       blocked
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js 20+**
  ```
  https://nodejs.org/en/download
  ```
- **npm** (comes with Node.js)
- **Git**

Verify installations:
```bash
node --version   # v20.x.x
npm --version    # 10.x.x
```

---

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/your-username/ci-pipeline.git
cd ci-pipeline
```

**2. Install dependencies**
```bash
npm install
```

---

### Run Tests

```bash
npm test
```

This executes `bash src/test.sh` as defined in `package.json`:

```json
"scripts": {
  "test": "bash src/test.sh"
}
```

---

## 🌐 Project Files

### `index.html`
The main HTML page of the project:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Git Project</title>
  <link rel="stylesheet" href="src/styles.css">
</head>
<body>
  <h2>This is my Git project</h2>
</body>
</html>
```

### `src/styles.css`
Simple stylesheet applied to the HTML page:
```css
body {
  background-color: lightblue;
}
```

### `package.json`
```json
{
  "name": "simple-node-app",
  "version": "1.0.0",
  "description": "A simple Node.js app for GitHub Actions PR testing",
  "main": "src/app.js",
  "scripts": {
    "test": "bash src/test.sh"
  },
  "dependencies": {}
}
```

---

## 🤝 Contributing

Contributions are welcome! 🙌

1. **Fork** the repository
2. **Create** your feature branch
   ```bash
   git checkout -b feature/YourFeature
   ```
3. **Commit** your changes
   ```bash
   git commit -m "feat: add YourFeature"
   ```
4. **Push** to your branch
   ```bash
   git push origin feature/YourFeature
   ```
5. **Open a Pull Request** → the CI pipeline will run automatically on your PR

> ✅ Make sure your PR passes all CI checks before requesting a review.

### Commit Convention

| Prefix | Use for |
|--------|---------|
| `feat:` | New feature |
| `fix:` | Bug fix |
| `ci:` | CI/CD pipeline changes |
| `docs:` | Documentation updates |
| `style:` | Code style / formatting |
| `test:` | Adding or updating tests |

---

## 📜 License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for more information.

---

## 📬 Contact

> 📌 *Replace with your real information.*

**Developer** — [@your-username](https://github.com/your-username)

📧 Email: `your.email@example.com`

🔗 Project: [https://github.com/your-username/ci-pipeline](https://github.com/your-username/ci-pipeline)

---

<div align="center">

<br/>

Built with 🟢 Node.js and 🤖 GitHub Actions

<br/>

⭐ If this project helped you understand CI pipelines, give it a star!

</div>
