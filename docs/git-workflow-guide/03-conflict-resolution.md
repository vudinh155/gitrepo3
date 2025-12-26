# 03 - Conflict Resolution

> **Mục tiêu**: Xử lý conflict từng bước, biến conflict thành cơ hội

**Thời lượng**: 30 phút
**Đối tượng**: Developer, Tech Lead

---

## 🎯 Mindset: Conflict là cơ hội, không phải vấn đề

```
Conflict xảy ra
  ↓
= Hai người cùng sửa một chỗ
  ↓
= Hai người cùng quan tâm đến code này
  ↓
= CƠ HỘI để:
  - Review architecture
  - Improve communication
  - Học cách code của nhau
  - Tối ưu design
```

**❌ WRONG Mindset:**
```
Conflict = Phiền phức
  → Giải quyết cho xong
  → Giữ code của mình
  → Hoặc giữ code người khác
  → KHÔNG hiểu ý đồ
```

**✅ RIGHT Mindset:**
```
Conflict = Signal
  → Architecture cần review?
  → Communication cần improve?
  → Refactor cần thiết?
  → Ngồi cùng nhau, học hỏi
```

---

## 🚨 Quy tắc BẮT BUỘC

### 1. KHÔNG TỰ Ý RESOLVE

```bash
# ❌ CẤM làm này:
git merge dev
# CONFLICT in src/auth.js

# Tự ý chọn code của mình
git checkout --ours src/auth.js
git add src/auth.js
git commit -m "Resolve conflict"

# HOẶC tự ý chọn code người khác
git checkout --theirs src/auth.js
```

**Tại sao CẤM?**
- Không hiểu ý đồ code người khác
- Có thể break feature người khác
- Bỏ lỡ cơ hội refactor tốt hơn

---

### 2. BẮT BUỘC: Ngồi cùng nhau

```
Conflict xảy ra
  ↓
1. Xác định: Ai viết code conflict?
2. Liên hệ: Slack/Zoom/F2F
3. Hẹn: 15-30 phút ngồi cùng
4. Cùng resolve:
   - Hiểu ý đồ của cả hai
   - Thảo luận approach tốt nhất
   - Cùng code giải pháp
   - Cùng test
5. Document: Ghi decision vào commit message
```

---

### 3. KHÔNG Auto-Accept Theirs/Ours

```bash
# ❌ Các lệnh này CẤM dùng khi conflict:
git merge --strategy-option theirs
git merge --strategy-option ours
git checkout --theirs .
git checkout --ours .

# ✅ CHỈ accept từng dòng code sau khi HIỂU
# (Dùng merge tool, resolve manual)
```

---

## 🔍 Step-by-Step Conflict Resolution

### Step 1: Identify Conflict

```bash
# Scenario: Bạn đang làm feature/auth-policy
git checkout feature/auth-policy
git rebase dev

# Output:
# CONFLICT (content): Merge conflict in src/auth/AuthService.js
# CONFLICT (content): Merge conflict in src/auth/PolicyEngine.js
```

---

### Step 2: Examine Conflicts

```bash
# Xem files bị conflict
git status

# Output:
# Unmerged paths:
#   both modified:   src/auth/AuthService.js
#   both modified:   src/auth/PolicyEngine.js

# Xem chi tiết conflict
cat src/auth/AuthService.js
```

**Conflict markers:**

```javascript
<<<<<<< HEAD (Your changes)
class AuthService {
  authenticate(user, policy) {
    return this.policyEngine.evaluate(user, policy);
  }
}
=======
class AuthService {
  authenticate(user) {
    return this.validateCredentials(user);
  }
}
>>>>>>> dev (Their changes)
```

---

### Step 3: Find Who Wrote Conflicting Code

```bash
# Tìm commit gây conflict
git log dev --oneline -- src/auth/AuthService.js

# Output:
# a1b2c3d feat: Update auth to use credentials only (@bob)
# e4f5g6h refactor: Extract auth logic (@alice)

# Xem chi tiết commit
git show a1b2c3d

# Xem blame (ai viết dòng nào)
git blame src/auth/AuthService.js
```

**Kết quả:**
```
Người viết: @bob
Commit: a1b2c3d
Thời gian: 2 days ago
Mục đích: Simplify auth, remove policy dependency
```

---

### Step 4: Contact & Schedule

```markdown
# Message to @bob (Slack/Teams):

Hi @bob,

I'm working on `feature/auth-policy` and got a conflict
in `src/auth/AuthService.js` with your recent commit a1b2c3d.

Could we sit together for 15-30 min to resolve this?
I want to understand your approach and see how we can
integrate both changes properly.

My approach: Adding policy-based auth
Your approach: Simplified credentials-only auth

Available: Today 2-4pm, Tomorrow 9-11am
```

---

### Step 5: Collaborative Resolution

#### Meeting Agenda (15-30 min)

```markdown
1. Hiểu ý đồ (5 min)
   - @bob explains: Tại sao remove policy?
   - @you explains: Tại sao add policy?

2. Discussion (10 min)
   - Cả hai approaches có value?
   - Có thể kết hợp?
   - Refactor cần thiết?
   - Trade-offs?

3. Decision (5 min)
   - Pick approach
   - OR: Design new approach kết hợp cả hai
   - OR: Refactor to support both

4. Code together (10 min)
   - Resolve conflict
   - Test
   - Commit
```

---

#### Example Resolution

**Scenario:**
- @bob removed policy vì "too complex, không ai dùng"
- @you added policy vì "client ABC cần custom auth rules"

**Discussion:**
```
@bob: Hiện tại chỉ 1 client cần policy, 5 clients khác dùng simple auth
@you: Đúng, nhưng roadmap có 3 clients mới cần policy

Decision: Support CẢ HAI
  → Simple auth = default
  → Policy auth = optional (via config)
```

**Resolution code:**

```javascript
// Kết hợp cả hai approaches
class AuthService {
  constructor(config) {
    this.config = config;
    this.policyEngine = config.enablePolicy
      ? new PolicyEngine(config.policyRules)
      : null;
  }

  authenticate(user, credentials) {
    // @bob's simple auth (default)
    const isValid = this.validateCredentials(user, credentials);

    // @you's policy auth (optional)
    if (this.policyEngine) {
      return isValid && this.policyEngine.evaluate(user);
    }

    return isValid;
  }
}
```

**Test both scenarios:**

```javascript
// Test simple auth (existing clients)
const simpleAuth = new AuthService({ enablePolicy: false });
expect(simpleAuth.authenticate(user, creds)).toBe(true);

// Test policy auth (new client ABC)
const policyAuth = new AuthService({
  enablePolicy: true,
  policyRules: [...]
});
expect(policyAuth.authenticate(user, creds)).toBe(true);
```

---

### Step 6: Resolve & Commit

```bash
# Edit file với solution đã thống nhất
code src/auth/AuthService.js

# Remove conflict markers
# Implement agreed solution

# Test
npm test

# Mark as resolved
git add src/auth/AuthService.js

# Continue rebase
git rebase --continue

# Commit message (DOCUMENT decision)
git commit -m "feat: Support both simple and policy-based auth

Resolved conflict with @bob's commit a1b2c3d.

Decision:
- Keep simple auth as default (for existing 5 clients)
- Add optional policy-based auth (for client ABC + 3 upcoming)
- Config-driven: enablePolicy flag

Trade-offs considered:
- Complexity vs Flexibility → Choose flexibility
- Performance impact → Minimal (lazy load policy engine)

Tested:
- Simple auth: All existing tests pass
- Policy auth: New tests added

Discussed with: @bob
Date: 2024-12-21"
```

---

## 🛠️ Conflict Resolution Tools

### VS Code (Recommended)

```
When conflict occurs:
  → VS Code highlights conflicts
  → Options:
    - Accept Current Change (yours)
    - Accept Incoming Change (theirs)
    - Accept Both Changes
    - Compare Changes

⚠️ DON'T just click "Accept"
✅ Read BOTH changes, understand context
✅ Discuss with author
✅ Then manually integrate
```

---

### Git Merge Tool

```bash
# Configure merge tool (one-time setup)
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# When conflict, launch merge tool
git mergetool

# This opens VS Code with 3-way merge view:
# - LEFT: Your changes
# - RIGHT: Their changes
# - CENTER: Result (edit this)
```

---

### Command Line (Manual)

```bash
# View conflict
cat src/file.js

# Edit manually
vim src/file.js

# Remove conflict markers:
# <<<<<<< HEAD
# =======
# >>>>>>> branch

# Save resolved version

# Mark resolved
git add src/file.js
```

---

## 📋 Conflict Types & Solutions

### Type 1: Simple Text Conflict

**Conflict:**
```javascript
<<<<<<< HEAD
const API_URL = 'https://api.staging.example.com';
=======
const API_URL = 'https://api.prod.example.com';
>>>>>>> dev
```

**Resolution:**
```javascript
// Config-driven instead of hard-code
const API_URL = process.env.API_URL || 'https://api.staging.example.com';
```

**Lesson:** Avoid hard-coding environment-specific values

---

### Type 2: Logic Conflict

**Conflict:**
```javascript
<<<<<<< HEAD (your feature)
function calculateDiscount(user) {
  if (user.isPremium) {
    return user.orderTotal * 0.2; // 20% for premium
  }
  return 0;
}
=======
function calculateDiscount(user) {
  if (user.loyaltyPoints > 1000) {
    return user.orderTotal * 0.15; // 15% for loyal customers
  }
  return 0;
}
>>>>>>> dev (their feature)
```

**Resolution:**
```javascript
// Support BOTH features
function calculateDiscount(user) {
  let discount = 0;

  // Premium discount (your feature)
  if (user.isPremium) {
    discount = Math.max(discount, user.orderTotal * 0.2);
  }

  // Loyalty discount (their feature)
  if (user.loyaltyPoints > 1000) {
    discount = Math.max(discount, user.orderTotal * 0.15);
  }

  // Note: User gets BEST discount (not cumulative)
  return discount;
}
```

**Lesson:** Design flexible logic that accommodates multiple rules

---

### Type 3: Refactor Conflict

**Conflict:**
```javascript
<<<<<<< HEAD (your refactor)
// You extracted to separate file
import { validateUser } from './validators';

function login(user) {
  validateUser(user);
  // ...
}
=======
function login(user) {
  // They added new validation inline
  if (!user.email || !user.password) {
    throw new Error('Invalid credentials');
  }
  if (user.isBlocked) {
    throw new Error('User is blocked');
  }
  // ...
}
>>>>>>> dev (their addition)
```

**Resolution:**
```javascript
// Move new validation to extracted file
import { validateUser } from './validators';

function login(user) {
  validateUser(user); // Now includes isBlocked check
  // ...
}

// In validators.js
export function validateUser(user) {
  if (!user.email || !user.password) {
    throw new Error('Invalid credentials');
  }
  // NEW: Add their validation
  if (user.isBlocked) {
    throw new Error('User is blocked');
  }
}
```

**Lesson:** Continue refactoring pattern, incorporate new logic

---

### Type 4: File Rename Conflict

**Scenario:**
- You: Renamed `AuthService.js` → `AuthenticationService.js`
- Them: Modified `AuthService.js` content

**Git behavior:**
```
Git may NOT detect rename
  → Shows as: deleted AuthService.js + added AuthenticationService.js
  → Their changes to AuthService.js appear as conflict
```

**Resolution:**

```bash
# 1. Accept your rename
git rm src/auth/AuthService.js

# 2. Apply their changes to renamed file
git show <their-commit-hash>:src/auth/AuthService.js > temp.js
# Manually merge their changes into AuthenticationService.js

# 3. Commit
git add src/auth/AuthenticationService.js
git commit -m "Merge rename + changes from @author

- Renamed AuthService → AuthenticationService
- Applied their changes: <describe changes>
"
```

---

## 🔁 Common Conflict Scenarios

### Scenario 1: Merge dev → feature branch

```bash
# Update feature branch with latest dev
git checkout feature/your-branch
git merge dev

# CONFLICT!

# Resolve (với collaboration)
# ...

# Complete merge
git add .
git commit -m "Merge dev into feature/your-branch

Conflicts resolved in:
- src/file1.js (discussed with @bob)
- src/file2.js (accepted their refactor)
"
```

---

### Scenario 2: Rebase feature branch on dev

```bash
# Cleaner history with rebase
git checkout feature/your-branch
git rebase dev

# CONFLICT in commit abc123

# Resolve conflict
# ...

# Continue rebase
git add .
git rebase --continue

# If multiple conflicts
# Resolve each commit one by one
# git rebase --continue after each

# When done
git push origin feature/your-branch --force-with-lease
```

**Rebase vs Merge:**
```
Rebase:
  ✅ Clean linear history
  ✅ Easy to review
  ❌ Rewrites history (need force push)
  ❌ More conflicts (resolve per commit)

Merge:
  ✅ Preserves history
  ✅ Single conflict resolution
  ❌ Messy history with merge commits
  ✅ Safer (no force push)
```

---

### Scenario 3: Multiple files conflict

```bash
git rebase dev

# CONFLICT in 5 files!
# src/auth/AuthService.js
# src/auth/PolicyEngine.js
# src/config/auth.config.js
# src/types/auth.types.js
# tests/auth.test.js

# DON'T panic!

# 1. Triage
# Which files are related?
# → All auth-related

# 2. Resolve in order
# Start with types (foundation)
git add src/types/auth.types.js

# Then config
git add src/config/auth.config.js

# Then core logic
git add src/auth/PolicyEngine.js
git add src/auth/AuthService.js

# Finally tests
git add tests/auth.test.js

# 3. Continue
git rebase --continue
```

---

### Scenario 4: Conflict during cherry-pick

```bash
# Scenario: Want one commit from another branch
git cherry-pick abc123

# CONFLICT!

# Resolve
# ...

# Continue
git add .
git cherry-pick --continue

# Or abort if too complex
git cherry-pick --abort
```

---

## 🚫 What NOT to Do

### ❌ Auto-resolve without understanding

```bash
# DON'T:
git merge dev
# Conflicts...
git checkout --ours .  # Keep all my changes
git add .
git commit -m "Resolve conflicts"

# WHY BAD:
# - Lose their changes completely
# - May break their feature
# - No learning opportunity
```

---

### ❌ Resolve conflicts alone (complex cases)

```bash
# DON'T:
# Complex conflict in core auth logic
# → Spend 2 hours figuring out their intent
# → Guess and resolve

# DO:
# → Spend 20 min discussing with author
# → Understand intent immediately
# → Resolve correctly in 10 min
```

---

### ❌ Ignore conflicts and commit broken code

```bash
# DON'T:
git add .
git commit -m "Fix conflicts"
# (But code doesn't compile or tests fail)

# DO:
# After resolving:
npm test          # ✅ All tests pass
npm run lint      # ✅ No lint errors
npm run build     # ✅ Build succeeds
# THEN commit
```

---

### ❌ Blame others for conflicts

```
❌ "Their code caused conflicts"
❌ "They should have checked before merging"
❌ "This is their fault"

✅ "We both touched same area - let's align"
✅ "Good opportunity to review architecture"
✅ "Let's make sure both features work well"
```

---

## ✅ Conflict Prevention

### 1. Communicate Early

```markdown
# In Slack/Teams before coding:

"Planning to refactor AuthService to support policies.
Will touch:
- src/auth/AuthService.js
- src/auth/PolicyEngine.js (new)
- tests/auth/*.test.js

Anyone working on auth code this sprint?"
```

**Effect:** Others aware → coordinate → avoid conflicts

---

### 2. Small, Frequent PRs

```
❌ BAD:
  1 PR với 50 files changed (3 weeks work)
  → High conflict probability
  → Hard to review
  → Merge nightmare

✅ GOOD:
  5 PRs, each 10 files (merge each week)
  → Low conflict probability
  → Easy to review
  → Smooth merges
```

---

### 3. Sync Often

```bash
# Every morning
git checkout dev
git pull origin dev

git checkout feature/your-branch
git rebase dev  # or git merge dev

# Before lunch
git pull origin dev --rebase

# Before end of day
git pull origin dev --rebase
```

**Effect:** Catch conflicts early when small

---

### 4. Modular Code

```javascript
// ❌ BAD: Everyone edits God class
class Application {
  // 2000 lines
  // Auth, DB, API, UI, everything
}

// ✅ GOOD: Separate concerns
class AuthService { }
class DatabaseService { }
class APIService { }
class UIManager { }
```

**Effect:** Different people work on different files

---

### 5. Code Review Culture

```
Fast PR reviews
  → Code merged quickly
  → Less time for conflicts to develop
  → Smaller merge windows

Slow PR reviews
  → Code sits in PR for days
  → Meanwhile dev branch moves ahead
  → Huge conflicts when finally merge
```

**Best practice:** Review PRs within 24 hours

---

## 📋 Conflict Resolution Checklist

```markdown
When conflict happens:

- [ ] Don't panic
- [ ] Identify conflicting files
- [ ] Find who wrote conflicting code (git blame/log)
- [ ] Contact them (Slack/F2F)
- [ ] Schedule 15-30 min meeting
- [ ] Discuss both approaches
- [ ] Understand intent of both changes
- [ ] Decide resolution strategy together
- [ ] Code resolution together (or solo after agreement)
- [ ] Test thoroughly (both features work)
- [ ] Document decision in commit message
- [ ] Tag collaborator in commit message
- [ ] Mark resolved and commit
- [ ] Push & continue
```

---

## 🎓 Learning from Conflicts

### After resolving conflict, ask:

```markdown
1. Architecture question:
   - Why did we conflict?
   - Is code too coupled?
   - Should we refactor for modularity?

2. Communication question:
   - Could we have known earlier?
   - Do we need better coordination?
   - Should we have design review before coding?

3. Process question:
   - Are PRs too large?
   - Do we sync often enough?
   - Is code review fast enough?

4. Positive outcome:
   - What did I learn from their approach?
   - Is the merged solution better than both originals?
   - Can we apply this pattern elsewhere?
```

---

## 📊 Conflict Metrics (For Improvement)

### Track over time:

```
- Conflicts per sprint
- Time to resolve conflicts (should decrease)
- Conflicts requiring rework (should decrease)
- Conflicts leading to improvements (should increase)
```

**Healthy trend:**
```
Sprint 1: 15 conflicts, avg 2h resolution, 5 reworks
Sprint 5: 8 conflicts, avg 30min resolution, 1 rework
Sprint 10: 3 conflicts, avg 20min resolution, 0 reworks
  → Better communication, modular code, frequent sync
```

---

**🔙 Quay lại: [00-README.md](./00-README.md)**
