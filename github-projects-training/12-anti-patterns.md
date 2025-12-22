# 12 - Anti-patterns & Best Practices

> **Mục tiêu**: Tránh những lỗi phổ biến, áp dụng best practices

**Thời lượng**: 45 phút
**Đối tượng**: PM, QA, Dev (tất cả)

---

## 🚫 Top 10 Anti-patterns

### 1. PM viết Issue mơ hồ

#### ❌ Sai

```markdown
Issue #123: Improve UX

Description: Users say UX is bad, fix it.

Labels: feature
```

**Vấn đề:**
- Dev không biết làm gì
- QA không biết test gì
- Waste time hỏi lại

#### ✅ Đúng

```markdown
Issue #123: [Feature] Add loading spinner khi upload file

Description:
User phàn nàn: Upload file lâu, không biết đã upload chưa.

User Story:
As a user,
I want to see loading progress when uploading,
So that I know upload is in progress.

AC:
- [ ] Hiển thị spinner khi click "Upload"
- [ ] Progress bar (0-100%)
- [ ] Success message khi done

Design: (link Figma)
```

---

### 2. Dev merge code không notify QA

#### ❌ Sai

```
Dev: Merge PR → không comment gì
QA: Không biết code đã merge
→ Không test
→ Bug lọt production
```

#### ✅ Đúng

```markdown
Dev merge PR #124:
  Comment trong issue: "@qa-alice PR merged, please test"
  Mention QA trong PR: "Ready for QA ✓"

QA:
  Nhận notification
  Test ngay
  Report result
```

---

### 3. QA chỉ test Happy Path

#### ❌ Sai

```
QA test:
  ✅ Login với account hợp lệ → Success

KHÔNG test:
  ❌ Login với password sai
  ❌ Login với email không tồn tại
  ❌ SQL injection
  ❌ XSS attack

→ Bugs lọt production
```

#### ✅ Đúng

```markdown
QA test:
  ✅ Happy path: Login success
  ✅ Error: Wrong password → error message
  ✅ Edge: Empty email → validation
  ✅ Edge: Special characters in email
  ✅ Security: SQL injection → blocked
  ✅ Security: XSS → sanitized
  ✅ Regression: Other features still work
```

---

### 4. Thay đổi Scope giữa Sprint

#### ❌ Sai

```
Sprint đang chạy:
  Stakeholder: "Tôi cần thêm feature X ngay"
  PM: "OK" → Add vào sprint
  Dev: Overload → miss sprint goal
```

#### ✅ Đúng

```
Stakeholder request:
  PM: "Noted! Tôi sẽ prioritize cho sprint sau"
  (Hoặc: Swap với issue khác nếu critical)

PM protect sprint scope
→ Team focused
→ Hit sprint goal
```

---

### 5. Issue không link PR

#### ❌ Sai

```
Dev tạo PR:
  - Không mention issue number
  - PM/QA không biết PR này fix issue nào
  - Khó trace: requirement → code

Issue #123:
  - Code đã merge
  - Nhưng không ai biết merge ở đâu
```

#### ✅ Đúng

```markdown
PR #124 body:

Closes #123
Fixes #125
Related to #100

---

GitHub auto-link:
  Issue #123 → PR #124
  PR #124 → Issue #123

Easy trace: Requirement → Code
```

---

### 6. Bug report thiếu Steps to Reproduce

#### ❌ Sai

```markdown
Issue #999: Login broken

Description: Login doesn't work

Labels: bug
```

**Vấn đề:**
- Dev không replicate được
- Hỏi lại QA → waste time
- Bug không được fix

#### ✅ Đúng

```markdown
Issue #999: [Bug] Login fails với Google trên Safari

Steps to Reproduce:
1. Open Safari 17
2. Go to /login
3. Click "Login with Google"
4. Select account
5. Error: redirect_uri_mismatch

Expected: Login success
Actual: Error

Environment: Safari 17, macOS 14
Logs: (attached)
```

---

### 7. Không track Regression

#### ❌ Sai

```
QA test feature mới:
  - Test feature mới → PASS
  - KHÔNG test feature cũ
  - Feature mới làm hỏng login
  - Bug lọt production
```

#### ✅ Đúng

```
QA test:
  ✅ Feature mới: PASS
  ✅ Regression: Login, Checkout, Dashboard → PASS

Nếu regression fail:
  → Report bug
  → Dev fix ngay
```

---

### 8. PM micromanage Dev

#### ❌ Sai

```
PM:
  - Hỏi dev mỗi 2 tiếng: "Xong chưa?"
  - Đổi status issue thay dev
  - Assign issue không hỏi dev

Dev:
  - Cảm thấy không được trust
  - Morale giảm
  - Productivity giảm
```

#### ✅ Đúng

```
PM:
  - Trust dev estimate
  - Check board 1-2 lần/ngày
  - Hỏi nếu issue > 3 ngày: "Cần help gì không?"
  - Dev tự assign issue

Dev:
  - Tự quản lý công việc
  - Proactive update progress
  - Happy & productive
```

---

### 9. Issue "Done" nhưng chưa test

#### ❌ Sai

```
Dev:
  - Code xong → merge PR
  - Move issue: In Progress → Done
  - Không notify QA

QA:
  - Không biết issue đã done
  - Không test
  - Bug lọt production
```

#### ✅ Đúng

```
Flow:
  Dev code → PR merge → Status: Testing
  QA test → PASS → Status: Done
  PM verify → Close issue

Issue CHỈ "Done" khi:
  ✓ Code merged
  ✓ QA tested & passed
  ✓ Deployed (hoặc ready to deploy)
```

---

### 10. Estimate quá lạc quan

#### ❌ Sai

```
Dev estimate:
  "Issue này 1 ngày làm xong"

Reality:
  - Code: 1 ngày
  - Debug: 0.5 ngày
  - Code review: 0.5 ngày
  - Fix review comments: 0.5 ngày
  - QA found bug: 0.5 ngày
  - Fix bug: 0.5 ngày
  Total: 3.5 ngày

→ Miss deadline
```

#### ✅ Đúng

```
Dev estimate:
  - Code: 1 ngày
  - Buffer (debug, review, bug fix): +50% = 1.5 ngày
  - Total: 2.5 ngày (safe estimate)

Or use Fibonacci: 1, 2, 3, 5, 8, 13...
  → Larger numbers có buffer built-in
```

---

## ✅ Best Practices Summary

### Cho PM

```markdown
✅ DO:
  - Viết issue rõ ràng (AC, design, context)
  - Protect sprint scope
  - Trust dev estimate
  - Communicate proactively với stakeholder
  - Review bugs, prioritize nhanh

❌ DON'T:
  - Viết issue mơ hồ
  - Thêm scope mid-sprint
  - Micromanage dev
  - Assign issue không hỏi dev
  - Close issue trước khi QA test
```

---

### Cho Dev

```markdown
✅ DO:
  - Đọc issue + AC trước khi code
  - Link PR với issue (Closes #123)
  - Request review sớm
  - Mention QA khi merge PR
  - Communicate blockers sớm

❌ DON'T:
  - Code mà không đọc issue
  - Merge PR không notify QA
  - Estimate quá lạc quan
  - Stuck 2 ngày mà không raise
  - Close issue trước khi QA test
```

---

### Cho QA

```markdown
✅ DO:
  - Đọc issue + PR trước khi test
  - Test: Happy path + Edge cases + Regression
  - Report bug chi tiết (steps, logs, screenshots)
  - Comment result rõ ràng (PASS/FAIL)
  - Retest sau khi dev fix

❌ DON'T:
  - Chỉ test happy path
  - Bug report thiếu info
  - Test mà không đọc PR
  - PASS issue mà không test regression
  - Đợi ai đó assign, không proactive
```

---

### Cho Team

```markdown
✅ DO:
  - Single source of truth = GitHub Issue
  - Link issues ↔ PRs
  - Communicate trong issue comments
  - Daily standup focused (< 15 min)
  - Retrospective: commit action items

❌ DON'T:
  - Spec trong Google Doc, code trong GitHub (2 nơi)
  - PR không link issue
  - Discussion trong Slack mà không summarize trong issue
  - Standup > 30 min (talking too much)
  - Retrospective không có action items
```

---

## 🎯 Red Flags (Dấu hiệu cảnh báo)

### Project Health Red Flags

```markdown
🚩 Sprint completion rate < 60%
   → Team overcommit hoặc poor estimation

🚩 Carry-over rate > 30%
   → Issues quá lớn hoặc scope creep

🚩 Bug escape rate > 10%
   → QA testing không đủ

🚩 Issues trong "Blocked" > 3 ngày
   → PM không unblock kịp

🚩 PR review time > 2 ngày
   → Reviewers quá busy hoặc quá nhiều PRs

🚩 Issue comment count > 15
   → Issue unclear hoặc scope thay đổi nhiều

🚩 Velocity fluctuates > 30%
   → Team unstable hoặc poor planning
```

**Actions khi thấy red flags:**
- PM: Review process, unblock, adjust planning
- Team: Retrospective, identify root cause
- Adjust: Process, estimation, communication

---

## 📚 Lessons Learned từ Team thực tế

### Case Study 1: Startup X (10 người)

**Problem:**
- Bug escape rate 30%
- User complain nhiều

**Root cause:**
- QA chỉ test happy path
- Không test regression
- Issue không có AC rõ ràng

**Solution:**
- PM: Viết AC chi tiết hơn
- QA: Checklist regression test
- Team: Definition of Done bao gồm "QA test pass"

**Result:**
- Bug escape rate giảm: 30% → 8%
- Customer satisfaction tăng: 3.2 → 4.5 sao

---

### Case Study 2: Company Y (50 người)

**Problem:**
- Sprint completion rate 50%
- Team morale thấp

**Root cause:**
- PM thêm issues mid-sprint
- Dev overcommit
- Không protect sprint scope

**Solution:**
- PM: Protect sprint scope nghiêm ngặt
- Dev: Estimate với buffer
- Team: Sprint planning realistic hơn

**Result:**
- Sprint completion: 50% → 85%
- Team morale tăng
- Predictability tăng → stakeholder happy

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu top 10 anti-patterns
- [ ] Biết best practices cho role của mình
- [ ] Biết các red flags cần watch out
- [ ] Commit cải thiện 1 điều trong công việc hàng ngày
```

---

**🚀 Tiếp theo: [13-quick-reference.md](./13-quick-reference.md) - Tài liệu tra cứu nhanh**
