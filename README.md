# CI/CD Pipeline Implementation — GitHub Actions

> Automating the full software development lifecycle using GitHub Actions — from code integration and deployment to dependency management, code review, and documentation publishing.

---

## Overview

This repository demonstrates a comprehensive **CI/CD pipeline** built on top of the [Heavens Above](https://github.com/stevenjoezhang/heavens-above) Node.js project. It implements **7 GitHub Actions workflows** covering every major phase of a modern development lifecycle — continuous integration, automated deployment, scheduled maintenance, dependency monitoring, code quality enforcement, documentation publishing, and custom release automation.

---

## Project Structure

```
.
├── .github/
│   ├── dependabot.yml                    # Dependabot configuration
│   └── workflows/
│       ├── ci.yml                        # Continuous Integration
│       ├── deploy.yml                    # Deployment Pipeline (Vercel)
│       ├── scheduled-tasks.yml           # Scheduled Maintenance
│       ├── dependency-updates.yml        # Automated Dependency Updates
│       ├── dependabot.yml                # Dependabot workflow config
│       ├── code-review.yml               # Code Review & Security Audit
│       ├── documentation-deploy.yml      # GitHub Pages Docs Deployment
│       └── custom-workflow.yml           # Release Notes Generator
├── public/
│   ├── css/
│   ├── data/
│   ├── index.html
│   └── download.html
├── src/
│   ├── satellite.js
│   ├── iridium.js
│   └── utils.js
├── run.js
├── package.json
└── README.md
```

---

## Workflows

### 1. Continuous Integration (`ci.yml`)
**Trigger:** Every push to `main` branch, or manually via `workflow_dispatch`

Runs automated checks to ensure every code change meets quality standards before it is merged.

**Steps:**
- Checks out code
- Sets up Node.js 20
- Installs dependencies via `npm install`
- Runs linting and saves output to `lint-report.txt`
- Runs tests and saves output to `test-report.txt`
- Uploads both reports as downloadable artifacts

**How to interpret results:** Green check = all checks passed. If the workflow fails, download the `lint-results` or `test-results` artifact from the Actions tab for details.

---

### 2. Deployment Pipeline (`deploy.yml`)
**Trigger:** Every push to `main` branch

Automates deployment of the application to **Vercel** on every successful push to main.

**Steps:**
- Checks out the repository
- Deploys to Vercel using the `amondnet/vercel-action` with production flags (`--prod --yes`)

**Required Secrets:**
| Secret | Description |
|--------|-------------|
| `VERCEL_TOKEN` | Your Vercel API token |
| `VERCEL_ORG_ID` | Your Vercel organization ID |
| `VERCEL_PROJECT_ID` | Your Vercel project ID |

---

### 3. Scheduled Tasks (`scheduled-tasks.yml`)
**Trigger:** Daily at midnight UTC (`0 0 * * *`), or manually

Runs routine maintenance tasks automatically on a schedule.

**Steps:**
- Checks out the repository
- Sets up Node.js 20
- Executes maintenance script (logs date, runs backup simulation)
- Sends a notification on completion (extensible to Slack/Discord/Email)

**How to interpret results:** Check the Actions tab for daily run logs. Extend the `Run scheduled maintenance script` step with your actual backup or cleanup commands.

---

### 4. Dependency Updates (`dependency-updates.yml` + `dependabot.yml`)
**Trigger:** Every Sunday at midnight UTC, or manually

Monitors and updates npm dependencies automatically, then opens a Pull Request for review.

**Steps:**
- Checks out code
- Installs current dependencies
- Runs `npm outdated` to list stale packages
- Runs `npm update` to apply updates
- Executes tests to verify compatibility
- Opens a PR titled **"Automated Dependency Updates"** to `main`

**Dependabot Configuration:** Also configured via `.github/dependabot.yml` to:
- Monitor `npm` packages weekly
- Monitor `github-actions` weekly
- Auto-label PRs with `dependencies` and `automated-update`
- Assign reviewer `Sheharyar171`

---

### 5. Code Review (`code-review.yml`)
**Trigger:** Pull requests to `main`, pushes to `main`, or manually

Enforces coding standards and security before any code is merged.

**Steps:**
- Checks out code
- Installs dependencies
- Runs ESLint (linting) — reports pass/fail
- Runs test suite — reports pass/fail
- Runs `npm audit` for security vulnerability scanning (moderate severity threshold)

**How to interpret results:** Each step reports ✅ or ⚠️ in the log. The security audit step will flag any known CVEs in dependencies.

---

### 6. Documentation Deployment (`documentation-deploy.yml`)
**Trigger:** Push to `main` when files in `docs/` are changed, or manually

Automatically builds and publishes project documentation to **GitHub Pages**.

**Steps:**
- Checks out the repository
- Installs dependencies
- Copies all files from `docs/` into `docs-build/`
- Deploys `docs-build/` to GitHub Pages using `peaceiris/actions-gh-pages`

**Note:** Create a `docs/` folder at the root of the project and add your Markdown or HTML documentation files. They will be automatically published on every update.

---

### 7. Custom Workflow — Release Notes Generator (`custom-workflow.yml`)
**Trigger:** When a version tag matching `v*.*.*` is pushed, or manually

Automates generation of release notes whenever a new version is tagged.

**Steps:**
- Checks out the repository
- Sets up Node.js 20
- Installs dependencies
- Generates a `notes.txt` file inside `release-notes/` with the tag reference
- Commits and force-pushes to a dedicated `release-notes` branch
- Sends a notification confirming the release notes were generated

**Usage:**
```bash
git tag v1.0.0
git push origin v1.0.0
```

---

## Getting Started

### Prerequisites
- Node.js >= 12.10.0
- npm
- A GitHub account with Actions enabled

### Clone and Run Locally

```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
npm install
node run.js
```

### Setting Up Secrets

Go to your repository → **Settings** → **Secrets and variables** → **Actions** and add:

| Secret Name | Required For |
|-------------|--------------|
| `VERCEL_TOKEN` | Deployment workflow |
| `VERCEL_ORG_ID` | Deployment workflow |
| `VERCEL_PROJECT_ID` | Deployment workflow |
| `GITHUB_TOKEN` | Auto-provided by GitHub |

---

## Workflow Summary Table

| Workflow | File | Trigger | Purpose |
|----------|------|---------|---------|
| Continuous Integration | `ci.yml` | Push to main | Lint + Test |
| Deployment Pipeline | `deploy.yml` | Push to main | Deploy to Vercel |
| Scheduled Tasks | `scheduled-tasks.yml` | Daily (cron) | Maintenance |
| Dependency Updates | `dependency-updates.yml` | Weekly (cron) | Update packages + PR |
| Code Review | `code-review.yml` | PR / Push to main | Lint + Audit |
| Documentation Deploy | `documentation-deploy.yml` | Push to docs/ | GitHub Pages |
| Release Notes | `custom-workflow.yml` | Tag push `v*.*.*` | Generate release notes |

---

## Tech Stack

- **Runtime:** Node.js 20
- **CI/CD:** GitHub Actions
- **Deployment:** Vercel
- **Docs Hosting:** GitHub Pages
- **Dependency Management:** Dependabot + npm
- **Security Scanning:** npm audit

---

## License

Released under the [GNU General Public License v3](http://www.gnu.org/licenses/gpl-3.0.html)
=======
# Heavens Above

爬取 Heavens Above 网站，获取卫星过境等信息。基于 Node.js，需使用 `npm` 安装依赖。  
Scripts for scraping the Heavens Above website. It's based on Node.js. You may need to install some dependency packages using npm.

**警告：不支持 IE。**  
**WARNING: DO NOT SUPPORT IE.**

## To Use

To clone and run this repository you'll need [Git](https://git-scm.com) and [Node.js](https://nodejs.org/en/download) (which comes with [npm](http://npmjs.com)) installed on your computer. `Promise.allSettled` requires Node.js 12.10.0 or later. From your command line:
```bash
# Clone this repository
git clone https://github.com/PKUPI/heavens-above.git
# Go into the repository
cd heavens-above
# Install dependencies
npm install
# Run the app
node run.js
```

`public` 目录下是一个完整的网站，爬取的数据会存储在 `public/data` 目录下，通过网络服务器访问 `public/index.html` 即可查看数据的内容。  
将 `node run.js` 添加到 `crontab`，以每周或每月一次的频率执行，则可以始终获取最新数据。

## Credits

* [Mimi](https://zhangshuqiao.org) Developer of this project.  
* Inspired by https://github.com/chengxinlun/haQuery

## License

Released under the GNU General Public License v3  
http://www.gnu.org/licenses/gpl-3.0.html

## Todo List

=\_=
User Customize
>>>>>>> master
