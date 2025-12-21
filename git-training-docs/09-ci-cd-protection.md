# 09 - CI/CD và Branch Protection

> **Đối tượng:** Git Master, Tech Lead
> **Thời gian học:** 1-2 giờ
> **Mục tiêu:** Setup CI/CD và bảo vệ repository

---

## 📖 Mục Lục

1. [Branch Protection Là Gì?](#1-branch-protection-là-gì)
2. [Setup Branch Protection Rules](#2-setup-branch-protection-rules)
3. [CI/CD Integration](#3-cicd-integration)
4. [Pull Request Workflow](#4-pull-request-workflow)
5. [Quyền Hạn Trong Team](#5-quyền-hạn-trong-team)
6. [GitHub Actions Examples](#6-github-actions-examples)
7. [GitLab CI Examples](#7-gitlab-ci-examples)

---

## 1. Branch Protection Là Gì?

### 1.1. Vấn đề cần giải quyết

```
┌─────────────────────────────────────────────────────────────────────┐
│             VẤN ĐỀ KHI KHÔNG CÓ PROTECTION                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ❌ Ai cũng push được vào main                                      │
│  ❌ Code lỗi deploy lên production                                  │
│  ❌ Force push xóa mất history                                      │
│  ❌ Merge code chưa review                                          │
│  ❌ Skip tests                                                      │
│  ❌ Không ai biết ai đã thay đổi gì                                 │
│                                                                     │
│  → Branch protection giải quyết TẤT CẢ vấn đề này!                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2. Branch Protection làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                BRANCH PROTECTION FEATURES                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  🔒 Cấm push trực tiếp                                              │
│     Mọi thay đổi phải qua Pull Request                             │
│                                                                     │
│  👥 Yêu cầu review                                                  │
│     Ít nhất N người phải approve                                   │
│                                                                     │
│  ✅ Yêu cầu CI pass                                                  │
│     Tests, lint, build phải thành công                             │
│                                                                     │
│  🚫 Cấm force push                                                  │
│     Không thể thay đổi history                                     │
│                                                                     │
│  📝 Yêu cầu signed commits                                         │
│     Xác thực người commit                                          │
│                                                                     │
│  🔄 Yêu cầu up-to-date branch                                      │
│     Branch phải sync với main trước khi merge                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Setup Branch Protection Rules

### 2.1. GitHub Setup

```
Settings → Branches → Add branch protection rule

┌─────────────────────────────────────────────────────────────────────┐
│                    GITHUB BRANCH PROTECTION                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Branch name pattern: main                                          │
│                                                                     │
│  ☑️ Require a pull request before merging                           │
│     ☑️ Require approvals: 1                                         │
│     ☑️ Dismiss stale pull request approvals when new commits pushed│
│     ☑️ Require review from Code Owners                              │
│                                                                     │
│  ☑️ Require status checks to pass before merging                   │
│     ☑️ Require branches to be up to date before merging            │
│     Status checks: ci/test, ci/build, ci/lint                      │
│                                                                     │
│  ☑️ Require conversation resolution before merging                 │
│                                                                     │
│  ☑️ Require signed commits                                          │
│                                                                     │
│  ☑️ Do not allow bypassing the above settings                      │
│                                                                     │
│  ☐ Allow force pushes                                               │
│  ☐ Allow deletions                                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.2. GitLab Setup

```
Settings → Repository → Protected Branches

┌─────────────────────────────────────────────────────────────────────┐
│                   GITLAB PROTECTED BRANCHES                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Branch: main                                                       │
│                                                                     │
│  Allowed to merge:  Maintainers                                    │
│  Allowed to push:   No one                                         │
│  Allowed to force push: ❌                                          │
│                                                                     │
│  ──────────────────────────────────────────────────                │
│                                                                     │
│  Settings → Merge requests                                          │
│                                                                     │
│  ☑️ Pipelines must succeed                                          │
│  ☑️ All discussions must be resolved                               │
│  Merge checks:                                                      │
│     ☑️ Approval rules                                               │
│     ☑️ Code owner approval                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3. CODEOWNERS File

```bash
# .github/CODEOWNERS (GitHub)
# .gitlab/CODEOWNERS (GitLab)

# Syntax: <pattern> <owners>

# Default owner cho tất cả files
*                       @tech-lead

# Frontend code
/src/components/        @frontend-team
*.tsx                   @frontend-team
*.css                   @frontend-team

# Backend code
/src/api/               @backend-team
/src/services/          @backend-team

# Infrastructure
/terraform/             @devops-team
/.github/               @devops-team
Dockerfile              @devops-team

# Sensitive files
/src/auth/              @security-team @tech-lead
*.env.example           @security-team
```

---

## 3. CI/CD Integration

### 3.1. CI Pipeline chuẩn

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CI PIPELINE FLOW                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Push/PR                                                            │
│     │                                                               │
│     ▼                                                               │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐             │
│  │  LINT   │──▶│  TEST   │──▶│  BUILD  │──▶│ DEPLOY  │             │
│  │         │   │         │   │         │   │ (main)  │             │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘             │
│                                                                     │
│  Parallel jobs where possible:                                     │
│                                                                     │
│  ┌─────────┐                                                        │
│  │  LINT   │────────────┐                                           │
│  └─────────┘            │                                           │
│                         ▼                                           │
│  ┌─────────┐       ┌─────────┐   ┌─────────┐                       │
│  │  TEST   │──────▶│  BUILD  │──▶│ DEPLOY  │                       │
│  │ (unit)  │       └─────────┘   └─────────┘                       │
│  └─────────┘            ▲                                           │
│                         │                                           │
│  ┌─────────┐            │                                           │
│  │  TEST   │────────────┘                                           │
│  │  (e2e)  │                                                        │
│  └─────────┘                                                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2. Status Checks

```
Mỗi job trong CI = 1 status check

┌─────────────────────────────────────────────────────────────────────┐
│  Pull Request #123                                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Status checks                                                      │
│                                                                     │
│  ✅ ci / lint                 Passed                                │
│  ✅ ci / test-unit            Passed                                │
│  ✅ ci / test-integration     Passed                                │
│  ✅ ci / build                Passed                                │
│  🔄 ci / deploy-preview       In progress...                       │
│                                                                     │
│  ─────────────────────────────────────────────────                 │
│  All checks must pass before merging                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 4. Pull Request Workflow

### 4.1. PR Lifecycle

```
┌─────────────────────────────────────────────────────────────────────┐
│                     PR LIFECYCLE                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. CREATE PR                                                       │
│     └── Draft PR nếu chưa ready                                    │
│                                                                     │
│  2. CI RUNS                                                         │
│     └── Lint, test, build                                          │
│                                                                     │
│  3. REQUEST REVIEW                                                  │
│     └── Assign reviewers                                           │
│                                                                     │
│  4. REVIEW                                                          │
│     ├── Approve ✅                                                   │
│     ├── Request changes ❌                                          │
│     └── Comment 💬                                                  │
│                                                                     │
│  5. ADDRESS FEEDBACK                                                │
│     └── Push fixes, CI runs again                                  │
│                                                                     │
│  6. APPROVAL                                                        │
│     └── All reviewers approve                                      │
│                                                                     │
│  7. MERGE                                                           │
│     └── Squash / Merge commit / Rebase                             │
│                                                                     │
│  8. CLEANUP                                                         │
│     └── Delete branch                                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2. PR Template

```markdown
<!-- .github/pull_request_template.md -->

## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation

## Related Issues
Closes #123

## Changes Made
- Change 1
- Change 2

## How to Test
1. Step 1
2. Step 2

## Screenshots (if applicable)

## Checklist
- [ ] Self-reviewed code
- [ ] Added tests
- [ ] Updated documentation
- [ ] No console.log/debug code
- [ ] PR title follows convention
```

### 4.3. Merge Strategies

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MERGE STRATEGIES                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. MERGE COMMIT (default)                                         │
│     - Giữ tất cả commits + merge commit                            │
│     - History đầy đủ nhưng phức tạp                                │
│                                                                     │
│  2. SQUASH AND MERGE (khuyên dùng cho features)                    │
│     - Gộp tất cả thành 1 commit                                    │
│     - History sạch                                                  │
│     - Mất chi tiết commits                                          │
│                                                                     │
│  3. REBASE AND MERGE                                                │
│     - Rebase commits lên target branch                             │
│     - History tuyến tính                                            │
│     - Giữ từng commit                                               │
│                                                                     │
│  Recommended:                                                       │
│  - Feature PR → Squash                                              │
│  - develop → main → Merge commit                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Quyền Hạn Trong Team

### 5.1. GitHub Roles

```
┌─────────────────────────────────────────────────────────────────────┐
│                     GITHUB ROLES                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  OWNER (Admin)                                                      │
│  ├── Full access                                                    │
│  ├── Manage settings, billing                                      │
│  ├── Delete repo                                                    │
│  └── Thường là: CTO, Tech Lead                                     │
│                                                                     │
│  MAINTAINER                                                         │
│  ├── Manage issues, PRs                                            │
│  ├── Merge PRs                                                      │
│  ├── Manage releases                                                │
│  └── Thường là: Git Master, Senior Dev                             │
│                                                                     │
│  WRITE                                                              │
│  ├── Push to non-protected branches                                │
│  ├── Create PRs                                                     │
│  ├── Comment, review                                                │
│  └── Thường là: Developer                                          │
│                                                                     │
│  READ                                                               │
│  ├── Clone repo                                                     │
│  ├── View code                                                      │
│  ├── Create issues                                                  │
│  └── Thường là: QA, PM, stakeholders                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2. Ai làm gì?

```
┌─────────────────────────────────────────────────────────────────────┐
│                   RESPONSIBILITIES                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Tech Lead / Owner:                                                │
│  ├── Setup branch protection                                       │
│  ├── Configure CI/CD                                                │
│  ├── Approve deploy to production                                  │
│  ├── Final review for main                                         │
│  └── Handle emergency rollback                                     │
│                                                                     │
│  Git Master / Maintainer:                                          │
│  ├── Review and merge PRs                                          │
│  ├── Maintain branch hygiene                                       │
│  ├── Help resolve complex conflicts                                │
│  ├── Tag releases                                                   │
│  └── Monitor CI/CD health                                          │
│                                                                     │
│  Developer:                                                         │
│  ├── Create feature branches                                       │
│  ├── Open PRs                                                       │
│  ├── Review peers' code                                            │
│  ├── Resolve conflicts on own branch                               │
│  └── Update branch before merge                                    │
│                                                                     │
│  Junior Developer:                                                  │
│  ├── Same as Developer                                              │
│  ├── Get mandatory review from Senior                              │
│  └── Ask for help with Git issues                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. GitHub Actions Examples

### 6.1. Basic CI Workflow

```yaml
# .github/workflows/ci.yml

name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm test

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]  # Chạy sau lint và test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-artifact@v4
        with:
          name: build
          path: dist/
```

### 6.2. Deploy Workflow

```yaml
# .github/workflows/deploy.yml

name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production  # Yêu cầu approval
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # Add deploy commands here

      - name: Notify on Slack
        if: always()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "Deploy ${{ job.status }}"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

### 6.3. PR Checks

```yaml
# .github/workflows/pr-checks.yml

name: PR Checks

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  # Check PR title follows convention
  pr-title:
    runs-on: ubuntu-latest
    steps:
      - name: Check PR title
        run: |
          TITLE="${{ github.event.pull_request.title }}"
          PATTERN="^\[(FEAT|FIX|DOCS|REFACTOR|TEST|CHORE)\]"
          if [[ ! $TITLE =~ $PATTERN ]]; then
            echo "❌ PR title must start with [FEAT], [FIX], etc."
            exit 1
          fi
          echo "✅ PR title is valid"

  # Check branch naming
  branch-name:
    runs-on: ubuntu-latest
    steps:
      - name: Check branch name
        run: |
          BRANCH="${{ github.head_ref }}"
          PATTERN="^(feat|fix|hotfix|refactor|docs|test|chore)/"
          if [[ ! $BRANCH =~ $PATTERN ]]; then
            echo "❌ Branch name must follow pattern: type/description"
            exit 1
          fi
          echo "✅ Branch name is valid"

  # Run tests
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm test -- --coverage
      - name: Upload coverage
        uses: codecov/codecov-action@v4
```

### 6.4. Auto-merge Dependabot

```yaml
# .github/workflows/auto-merge-dependabot.yml

name: Auto-merge Dependabot

on:
  pull_request:

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    if: github.actor == 'dependabot[bot]'
    steps:
      - name: Approve PR
        run: gh pr review --approve "$PR_URL"
        env:
          PR_URL: ${{ github.event.pull_request.html_url }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Enable auto-merge
        run: gh pr merge --auto --squash "$PR_URL"
        env:
          PR_URL: ${{ github.event.pull_request.html_url }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## 7. GitLab CI Examples

### 7.1. Basic Pipeline

```yaml
# .gitlab-ci.yml

stages:
  - lint
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "20"

.node-template: &node-template
  image: node:${NODE_VERSION}
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/

lint:
  <<: *node-template
  stage: lint
  script:
    - npm ci
    - npm run lint

test:
  <<: *node-template
  stage: test
  script:
    - npm ci
    - npm test -- --coverage
  coverage: '/Lines\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

build:
  <<: *node-template
  stage: build
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week

deploy-staging:
  stage: deploy
  script:
    - echo "Deploy to staging"
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy-production:
  stage: deploy
  script:
    - echo "Deploy to production"
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual  # Yêu cầu click để deploy
```

### 7.2. Merge Request Pipeline

```yaml
# .gitlab-ci.yml (thêm)

# Chỉ chạy cho MR
.mr-rules:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

mr-lint:
  extends: .mr-rules
  stage: lint
  script:
    - npm ci
    - npm run lint

mr-test:
  extends: .mr-rules
  stage: test
  script:
    - npm ci
    - npm test

# Review app cho mỗi MR
review-app:
  extends: .mr-rules
  stage: deploy
  script:
    - echo "Deploy review app"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.review.example.com
    on_stop: stop-review-app
    auto_stop_in: 1 week

stop-review-app:
  extends: .mr-rules
  stage: deploy
  script:
    - echo "Stop review app"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  when: manual
```

---

## 📋 Quick Reference

### Branch Protection Checklist

```
□ Require PR for all changes to main/develop
□ Require at least 1 approving review
□ Require status checks to pass
□ Require branches to be up-to-date
□ Disable force push
□ Set up CODEOWNERS
□ Configure required reviewers for sensitive files
```

### CI/CD Checklist

```
□ Lint runs on all PRs
□ Unit tests run on all PRs
□ Build verification on all PRs
□ E2E tests on develop/main
□ Auto-deploy to staging on develop
□ Manual approval for production deploy
□ Slack/Teams notifications
□ Coverage reports
```

---

## ✅ Checklist Cho Git Master

```
□ Setup branch protection cho main và develop
□ Configure required status checks
□ Setup CODEOWNERS
□ Configure CI pipeline
□ Document merge strategy
□ Setup automated deployments
□ Configure notifications
□ Review permissions regularly
```

---

## ➡️ Bước Tiếp Theo

Học file **10-anti-patterns.md** - Các Anti-patterns Cần Tránh

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
