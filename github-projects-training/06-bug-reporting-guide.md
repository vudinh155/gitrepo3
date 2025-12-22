# 06 - Bug Reporting Guide (Hướng dẫn báo cáo Bug chuẩn)

> **Mục tiêu**: Viết bug report mà Dev fix được ngay lần đầu, không cần hỏi lại

**Thời lượng**: 45 phút
**Đối tượng**: QA Engineer, QA Lead

---

## 🎯 Bug report tốt là như thế nào?

### 3 tiêu chí vàng

```
1. REPRODUCIBLE (Tái hiện được)
   → Dev đọc xong replicate được bug ngay

2. SPECIFIC (Cụ thể)
   → Rõ ràng: Expected vs Actual

3. ACTIONABLE (Hành động được)
   → Dev biết fix ở đâu, fix như thế nào
```

### Bug report tốt trả lời được 7 câu hỏi

```
1. Bug GÌ? (What) → Title + Description
2. Tái hiện NTN? (How) → Steps to Reproduce
3. MONG ĐỢI gì? (Expected) → Expected Behavior
4. THỰC TẾ gì? (Actual) → Actual Behavior
5. Ở ĐÂU? (Where) → Environment (browser, OS, URL)
6. KHI NÀO xảy ra? (When) → Frequency (always, sometimes, rarely)
7. Mức độ NGHIÊM TRỌNG? (Severity) → Critical, Major, Minor
```

---

## 📝 Template Bug Report chuẩn

```markdown
## Title
[Bug] <Tóm tắt bug ngắn gọn, cụ thể>

## Description
<Mô tả bug: hiện tượng gì, ảnh hưởng ra sao>

## Steps to Reproduce
1. <Bước 1: Cụ thể, chi tiết>
2. <Bước 2>
3. <Bước 3>
...
**Result:** <Kết quả sau bước cuối>

## Expected Behavior
<Hành vi đúng theo spec/design>

## Actual Behavior
<Hành vi thực tế (sai)>

## Environment
- **URL**: <https://staging.myapp.com/...>
- **Browser**: <Chrome 120.0.6099.129 (chính xác version)>
- **OS**: <macOS 14.1.1 / Windows 11 / iOS 17.1>
- **Device**: <MacBook Pro M1 / iPhone 14 Pro / Samsung Galaxy S23>
- **Screen size**: <1920x1080 / 375x812>
- **User account**: <test@example.com (nếu cần login)>

## Screenshots / Videos
<Attach screenshot hoặc video recording>
- Screenshot 1: <Mô tả>
- Video: <Link Loom/YouTube>

## Error Logs / Console
```
<Paste error logs từ browser console hoặc server logs>
```

## Frequency
<Always (100%) / Often (>50%) / Sometimes (10-50%) / Rare (<10%)>

## Severity
<Critical / High / Medium / Low> (xem bảng phía dưới)

## Additional Context
- Tested trên browsers khác: <Chrome OK, Safari FAIL>
- Related issues: <#123, #456>
- Root cause (nếu biết): <...>

---

**Labels**: bug, <severity>, <team>
**Priority**: <Critical/High/Medium/Low>
**Assignee**: <@dev-john (nếu biết ai phụ trách)>
**Related to**: <Issue #123 (nếu bug liên quan feature đang dev)>
```

---

## 🔥 Severity Level (Mức độ nghiêm trọng)

### Bảng phân loại Severity

| Severity | Định nghĩa | Ví dụ | SLA Fix |
|----------|-----------|-------|---------|
| **Critical** | Hệ thống crash, mất data, security issue | - App crash khi mở<br>- Data user bị mất<br>- SQL injection | ASAP (< 4h) |
| **High** | Core feature không work, block user | - Không thể login<br>- Không thể checkout<br>- Payment fail | < 1 ngày |
| **Medium** | Feature work nhưng sai logic/UI | - Tính toán sai<br>- UI lệch design<br>- Typo quan trọng | < 1 tuần |
| **Low** | Cosmetic, không ảnh hưởng UX nhiều | - Typo không quan trọng<br>- Màu sai 1 chút<br>- Text alignment | Backlog |

### ⚠️ Lưu ý phân loại Severity

**✅ Làm đúng:**
```
Bug: "User không thể login"
→ Severity: High (core feature không work)

Bug: "Button màu xanh nhạt hơn design 1 chút"
→ Severity: Low (cosmetic)
```

**❌ Làm sai:**
```
Bug: "Typo: 'Login' → 'Longin'"
→ Severity: Critical (sai! chỉ là typo)

Bug: "App crash khi mở"
→ Severity: Low (sai! phải Critical)
```

---

## ✅ Ví dụ Bug Report TỐT

### Ví dụ 1: Critical Bug

```markdown
Issue #789: [Bug] App crash khi user upload ảnh > 5MB trên iOS

## Description
App crash hoàn toàn khi user upload ảnh có kích thước > 5MB trên iOS.
User mất toàn bộ data đã nhập trong form.

**Impact:**
- Ảnh hưởng 100% iOS users khi upload ảnh lớn
- Data loss (user phải nhập lại form)
- App store reviews giảm từ 4.5 → 3.8 sao (10 reviews negative trong 2 ngày)

## Steps to Reproduce
1. Mở app trên iPhone 14 Pro (iOS 17.1.1)
2. Vào màn hình "Create Post"
3. Điền form: Title, Description (tốn 5 phút nhập)
4. Click "Add Photo"
5. Chọn ảnh có kích thước 6MB (ảnh test: attached)
6. App freeze → crash → về Home screen

**Result:** App crash, data bị mất

## Expected Behavior
- Option A: App resize ảnh xuống < 5MB trước khi upload
- Option B: Hiển thị error: "Photo too large (max 5MB)"

## Actual Behavior
- App crash hoàn toàn
- Không có error message
- Data đã nhập bị mất

## Environment
- **Device**: iPhone 14 Pro, iPhone 13, iPad Air (tất cả đều crash)
- **OS**: iOS 17.1.1
- **App version**: 2.5.0 (build 125)
- **NOT affected**: Android devices (handle ảnh lớn OK)

## Screenshots / Videos
- Video: https://loom.com/xyz (0:15 - reproduce crash)
- Crash log: (attached crash_log.txt)

## Error Logs
```
*** Terminating app due to uncaught exception 'NSInternalInconsistencyException'
*** reason: 'Image size exceeds maximum allowed (5MB)'
*** file: ImageUploader.swift, line 234
```

## Frequency
**Always (100%)** - Mọi lần upload ảnh > 5MB đều crash

## Severity
**Critical**
- App crash
- Data loss
- 100% iOS users affected

## Root Cause (Suspected)
File `ImageUploader.swift:234` không handle exception khi ảnh > 5MB
→ Throw exception → app crash

## Suggested Fix
Thêm try-catch:
```swift
do {
  try uploadImage(image)
} catch {
  showAlert("Image too large (max 5MB)")
}
```

---

**Labels**: bug, critical, team:ios, regression
**Priority**: Critical (fix ASAP)
**Assignee**: @dev-ios-team
**Related to**: #750 (Upload ảnh feature)
```

**✅ Tại sao bug report này TỐT:**
- Steps chi tiết → Dev replicate ngay được
- Impact rõ ràng → PM prioritize đúng
- Environment cụ thể → Biết chỉ iOS affected
- Có crash logs → Dev debug nhanh
- Có suggested fix → Dev có hướng giải quyết
- Severity đúng (Critical)

---

### Ví dụ 2: UI Bug (Medium Severity)

```markdown
Issue #890: [Bug] Button "Save for Later" bị truncate trên mobile < 375px

## Description
Button text "Save for Later" bị cắt thành "Save for L..." trên màn hình mobile nhỏ (< 375px width).

**Impact:**
- UI không khớp design
- Ảnh hưởng ~5% users (iPhone SE, small Android phones)
- Không block functionality (button vẫn click được)

## Steps to Reproduce
1. Mở https://staging.myapp.com/cart trên Chrome
2. Mở DevTools → Toggle device toolbar
3. Set width = 360px (iPhone SE)
4. Scroll xuống cart item
5. Observe button "Save for Later"

**Result:** Text bị cắt thành "Save for L..."

## Expected Behavior
- Button text hiển thị đủ: "Save for Later"
- Hoặc: Wrap text xuống 2 dòng
- Hoặc: Icon + text ngắn hơn ("Save")

Design reference: Figma frame "Mobile Cart"

## Actual Behavior
- Text bị truncate với ellipsis: "Save for L..."

## Environment
- **URL**: https://staging.myapp.com/cart
- **Browser**: Chrome 120 (mobile mode), Safari iOS (iPhone SE)
- **Screen size**: < 375px width (360px, 320px)
- **NOT affected**: > 375px width (iPhone 12+, Android modern phones)

## Screenshots
- Screenshot 1: (attached) - 360px width - text truncate
- Screenshot 2: (attached) - 375px width - text OK
- Expected (Figma): (attached) - design spec

## Frequency
**Always (100%)** - Luôn xảy ra khi width < 375px

## Severity
**Medium**
- UI không khớp design
- Không block core functionality
- Ảnh hưởng minority users (~5%)

## Suggested Fix
CSS update trong `CartItem.css`:

```css
/* Current */
.save-button {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Fix Option 1: Wrap text */
.save-button {
  white-space: normal;
  word-wrap: break-word;
}

/* Fix Option 2: Icon + shorter text */
.save-button {
  /* "❤️ Save" instead of "Save for Later" */
}
```

---

**Labels**: bug, medium, team:frontend, ui
**Priority**: Medium
**Milestone**: Sprint 21 (không urgent)
```

**✅ Tại sao bug report này TỐT:**
- Steps reproduce rõ ràng (dùng DevTools)
- Screenshot so sánh: actual vs expected
- Severity đúng (Medium - UI bug không critical)
- Suggested fix với code sample
- Impact realistic (~5% users)

---

## ❌ Ví dụ Bug Report TỆ

### Ví dụ 1: Thiếu thông tin

```markdown
Issue #999: [Bug] Login broken

Description: Login doesn't work

Labels: bug
```

**❌ Tại sao bug report này TỆ:**
- Không có steps to reproduce
- Không rõ expected vs actual
- Không có environment
- Không có screenshot/logs
- Dev không thể replicate

**🔧 Cách fix:** Viết lại theo template chuẩn (xem trên)

---

### Ví dụ 2: Steps không rõ ràng

```markdown
Issue #888: [Bug] Checkout fail

Steps to Reproduce:
1. Go to checkout
2. Click button
3. Bug happens

Expected: Should work
Actual: Doesn't work
```

**❌ Tại sao tệ:**
- Steps mơ hồ: "Click button" → button nào?
- "Bug happens" → bug gì?
- "Should work" → work như thế nào?

**🔧 Fix:**

```markdown
Steps to Reproduce:
1. Login as test@example.com
2. Add 2 items to cart (Product #123, #456)
3. Click "Checkout" button (top right)
4. Fill shipping info: (Name, Address, Phone)
5. Click "Continue to Payment"
6. Select payment method: Credit Card
7. Fill card: 4242 4242 4242 4242, Exp: 12/25, CVV: 123
8. Click "Pay Now"

Expected: Order created, redirect to /order-confirmation
Actual: Error "Payment failed", no error message why
```

---

## 🧪 Best Practices

### 1. Steps to Reproduce - Viết như thế nào?

**✅ Steps tốt:**
```markdown
1. Login as test@example.com / password123
2. Vào page: https://staging.myapp.com/profile
3. Click button "Edit Profile" (top right, blue button)
4. Change "Display Name" từ "Alice" → "Bob"
5. Click "Save" (bottom, green button)
6. Observe: Name không update, vẫn hiển thị "Alice"
```

**❌ Steps tệ:**
```markdown
1. Go to profile
2. Edit name
3. Bug
```

**Rules:**
- Mỗi bước cụ thể, rõ ràng
- Include test account (nếu cần login)
- Include test data (cụ thể: "Alice" → "Bob", không phải "change name")
- Describe UI element rõ: "blue button top right", không phải "click button"

---

### 2. Expected vs Actual - Viết như thế nào?

**✅ Tốt:**
```markdown
Expected:
  - Name update thành "Bob"
  - Header hiển thị "Welcome, Bob"
  - Database record updated

Actual:
  - Name vẫn là "Alice"
  - Header vẫn "Welcome, Alice"
  - API call return 200 OK (nhưng không update DB)
```

**❌ Tệ:**
```markdown
Expected: Should work
Actual: Doesn't work
```

---

### 3. Environment - Thông tin nào cần thiết?

**Luôn luôn include:**
- ✅ Browser + version chính xác (Chrome 120.0.6099.129, không phải "Chrome mới nhất")
- ✅ OS + version (macOS 14.1.1, Windows 11, iOS 17.2)
- ✅ URL cụ thể (https://staging.myapp.com/cart, không phải "staging")
- ✅ Screen size (nếu responsive issue: 1920x1080, 360px width)

**Include nếu relevant:**
- User account (nếu bug specific to user data)
- Test data (product ID, order ID, etc.)
- Network condition (3G slow, WiFi, offline)

---

### 4. Screenshot / Video - Khi nào cần?

**✅ Luôn luôn attach screenshot nếu:**
- UI bug (layout, color, text)
- Error message hiển thị
- Crash screen

**✅ Attach video (Loom/YouTube) nếu:**
- Bug cần nhiều bước reproduce
- Animation/transition bug
- Performance issue (lag, freeze)

**Tools gợi ý:**
- Screenshot: macOS (Cmd+Shift+4), Windows (Win+Shift+S)
- Video: Loom, CloudApp, macOS QuickTime
- GIF: LICEcap, Kap

---

### 5. Logs - Lấy ở đâu?

**Browser Console:**
```
Chrome DevTools → Console tab
(Cmd+Option+J on Mac, Ctrl+Shift+J on Windows)
```

**Network logs:**
```
Chrome DevTools → Network tab
→ Filter: XHR
→ Click failed request → Preview response
```

**Mobile app logs:**
```
iOS: Xcode → Devices → View Device Logs
Android: adb logcat
```

---

## 📊 QA Workflow: Tìm bug → Report → Track

### Workflow

```
┌─────────────┐
│ QA tìm bug  │
└──────┬──────┘
       ↓
┌─────────────────────┐
│ Classify severity   │ (Critical/High/Medium/Low)
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ Tạo bug issue       │ (theo template)
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ Mention PM + Dev    │ (@pm-bob @dev-john please check)
└──────┬──────────────┘
       ↓
   ┌───────────────┐
   │ Severity?     │
   └───┬───────────┘
       ↓
  Critical/High → Mention ngay, đừng đợi
  Medium/Low → Report bình thường
       ↓
┌─────────────────────┐
│ PM prioritize       │ (vào sprint nào)
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ Dev fix → PR merge  │
└──────┬──────────────┘
       ↓
┌─────────────────────┐
│ QA retest           │ (verify fix)
└──────┬──────────────┘
       ↓
   ┌──────────┐
   │ Pass?    │
   └───┬──────┘
       ↓
   Yes │ No
       │  └──→ Comment "Still failing", reassign Dev
       ↓
┌─────────────────────┐
│ Close bug issue     │
└─────────────────────┘
```

---

## 🎯 Checklist tự review Bug Report

Trước khi submit bug issue, QA tự hỏi:

```markdown
## Reproducibility (Tái hiện được)
- [ ] Tôi đã reproduce bug ít nhất 2 lần
- [ ] Steps đủ rõ để Dev replicate
- [ ] Tested trên nhiều browsers/devices (nếu relevant)

## Specificity (Cụ thể)
- [ ] Title rõ ràng, đủ nghĩa
- [ ] Expected vs Actual rõ ràng
- [ ] Environment đầy đủ (browser, OS, URL)

## Evidence (Bằng chứng)
- [ ] Có screenshot/video
- [ ] Có error logs (nếu có)
- [ ] Có console logs (nếu có)

## Severity (Mức độ)
- [ ] Severity đúng (Critical/High/Medium/Low)
- [ ] Impact mô tả rõ (ảnh hưởng bao nhiêu % users)

## Actionable (Hành động được)
- [ ] Dev đọc xong biết fix ở đâu
- [ ] Linked issue liên quan (nếu có)
- [ ] Suggested fix (nếu biết)
```

---

## ❓ FAQ

### **Q1: Bug chỉ xảy ra 1 lần, có report không?**

**A**: **Nên report**, nhưng note rõ:
```markdown
Frequency: Rare (<10%) - Chỉ reproduce được 1 lần

Note: Tried 10 times, only happened once.
Might be race condition or network issue.
```

### **Q2: Không biết expected behavior, report thế nào?**

**A**: Hỏi PM:
```markdown
@pm-bob Issue này không rõ expected behavior.

Actual: User click "Save" → không có feedback gì

Expected: Nên hiển thị gì?
  - Success message?
  - Loading spinner?
  - Redirect về đâu?
```

### **Q3: Bug đã fix rồi, nhưng retest vẫn fail. Làm gì?**

**A**:
```markdown
Comment trong bug issue:

**Retest Result: ❌ Still FAIL**

Retested on: 2024-06-16 10:00
Environment: staging (after PR #456 merged)

Original bug: Vẫn tái hiện
Steps: (same as above)

Please recheck fix.
Reassigning to @dev-john.
```

### **Q4: Tìm nhiều bugs cùng lúc, tạo 1 issue hay nhiều issues?**

**A**: **Nhiều issues** (1 bug = 1 issue).
- Dễ track
- Dễ prioritize riêng
- Dev có thể fix parallel

**Exception:** Nếu cùng root cause → tạo 1 issue, list all bugs.

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu 3 tiêu chí bug report tốt: Reproducible, Specific, Actionable
- [ ] Biết template bug report chuẩn
- [ ] Biết phân loại Severity (Critical/High/Medium/Low)
- [ ] Biết viết Steps to Reproduce rõ ràng
- [ ] Biết viết Expected vs Actual
- [ ] Biết cách attach screenshot/video/logs
- [ ] Có checklist tự review bug report

---

**🚀 Tiếp theo: [07-pm-dev-qa-collaboration.md](./07-pm-dev-qa-collaboration.md) - Phối hợp PM-Dev-QA**
