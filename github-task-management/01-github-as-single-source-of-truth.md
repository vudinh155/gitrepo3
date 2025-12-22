# 01 - GitHub as Single Source of Truth

> **Mục tiêu**: Hiểu tại sao GitHub là hệ thống ghi nhận công việc trung tâm

**Thời lượng**: 30 phút
**Đối tượng**: PO, HR, DM, Team Lead (tất cả)

---

## 🎯 Vấn đề khi KHÔNG có Single Source of Truth

### Scenario thực tế

```
Team 10 người, không dùng GitHub đúng cách:

❌ PM track task trong Excel
❌ Dev code nhưng không tạo issue
❌ Meeting notes trong Slack
❌ Design spec trong Figma comments
❌ Bug tracking trong email

Kết quả:
→ 5 nguồn dữ liệu không sync
→ HR không biết ai làm gì
→ PO không biết tiến độ thật
→ End of month: tranh cãi về đóng góp
```

---

## ✅ GitHub là gì?

**GitHub** = Hệ thống quản lý code + công việc + collaboration

GitHub ghi nhận được:

### 1. **Issues** (Công việc)
```
- Feature request
- Bug report
- Task assignment
- Documentation
```

### 2. **Pull Requests** (Code thay đổi)
```
- Code changes
- Code review
- Discussion
- Approval
```

### 3. **Commits** (Đóng góp chi tiết)
```
- Ai viết code gì
- Khi nào
- File nào thay đổi
- Bao nhiêu dòng
```

### 4. **Reviews** (Đánh giá chéo)
```
- Ai review cho ai
- Comment quality
- Approve/Request changes
```

### 5. **Projects** (Tổng quan)
```
- Sprint planning
- Backlog management
- Progress tracking
- Timeline view
```

---

## 📊 GitHub ghi nhận được GÌ?

### A. **Contribution (Đóng góp)**

```
GitHub tự động track:

✓ Issues created
✓ Issues completed
✓ PRs created
✓ PRs merged
✓ Commits pushed
✓ Reviews given
✓ Comments made
```

**Ví dụ thực tế:**

```
Dev Alice (1 tháng):
  - Issues completed: 12 (8 features, 4 bugs)
  - PRs merged: 15
  - Commits: 87
  - Reviews: 23
  - Comments: 145

→ Data này 100% accurate, không cảm tính
```

---

### B. **Quality (Chất lượng)**

```
GitHub cho thấy:

✓ Rework rate (PR bị reject bao nhiêu lần)
✓ Bug rate (issue type distribution)
✓ Review quality (comment depth)
✓ Code churn (code viết rồi xóa)
```

**Ví dụ:**

```
Dev Bob:
  - 10 PRs created
  - 8 PRs merged lần đầu (80% first-time approval)
  - 2 PRs cần rework
  - Average review comments: 3 (ít issue)

→ Code quality tốt
```

vs.

```
Dev Charlie:
  - 10 PRs created
  - 4 PRs merged lần đầu (40% first-time approval)
  - 6 PRs cần rework nhiều lần
  - Average review comments: 15 (nhiều issue)

→ Code quality cần improve
```

---

### C. **Collaboration (Phối hợp)**

```
GitHub track:

✓ Ai review cho ai
✓ Ai giúp ai
✓ Ai block ai
✓ Response time
```

**Ví dụ:**

```
Dev David:
  - Reviews given: 30 (nhiều)
  - Helpful comments: 85%
  - Average review time: 2 hours (nhanh)

→ Team player, giúp đỡ team
```

---

## 🆚 So sánh: "Bận" vs "Có đóng góp" vs "Đóng góp chất lượng"

### Case Study: 3 Devs

#### **Dev A: "Bận" nhưng không productive**

```
Metrics:
  - Online 12h/ngày
  - Slack messages: 200/ngày
  - Meetings: 6 hours/ngày

GitHub:
  - Issues completed: 1/sprint
  - PRs merged: 2/sprint
  - Commits: 20

Đánh giá: ❌ BẬN nhưng ít đóng góp thực sự
```

---

#### **Dev B: "Có đóng góp" nhưng chất lượng thấp**

```
Metrics:
  - Issues completed: 10/sprint
  - PRs merged: 12/sprint
  - Commits: 150

BUT:
  - Rework rate: 60% (6/10 PRs cần làm lại)
  - Bug rate: 50% (5/10 issues là bug do mình gây ra)
  - Review comments: Avg 20/PR (nhiều issue)

GitHub cho thấy:
  - Nhiều commit nhưng code churn cao (viết rồi xóa)
  - Nhiều PR nhưng quality thấp

Đánh giá: ⚠️ Productive nhưng chất lượng cần improve
```

---

#### **Dev C: "Đóng góp chất lượng cao"**

```
Metrics:
  - Issues completed: 6/sprint (ít hơn Dev B)
  - PRs merged: 8/sprint
  - Commits: 80

BUT:
  - Rework rate: 10% (chỉ 1/10 PR cần rework)
  - Bug rate: 5% (gần như không gây bug)
  - Review comments: Avg 3/PR (ít issue)
  - Impact: Handle core modules (high complexity)
  - Reviews given: 25/sprint (giúp team)

GitHub cho thấy:
  - Commits ít nhưng meaningful (low churn)
  - PRs quality cao (first-time approval)
  - Code reviews thoughtful (giúp improve team)

Đánh giá: ✅ High-quality contributor
```

---

## 📈 GitHub là "Hệ thống ghi nhận tự động"

### Lợi ích cho từng vai trò

#### **Product Owner**

```
✓ Xem tiến độ real-time
✓ Biết issue nào bị stuck
✓ Forecast delivery date
✓ Report stakeholders dựa trên data

Không cần:
  ❌ Hỏi dev "xong chưa?" mỗi ngày
  ❌ Sync meeting 2h để update status
  ❌ Excel tracking thủ công
```

---

#### **HR**

```
✓ Đánh giá performance dựa trên data
✓ Phân biệt busy vs productive
✓ Identify high performers
✓ Coaching dựa trên metrics cụ thể

Không cần:
  ❌ Đánh giá dựa trên "cảm giác"
  ❌ So sánh máy móc số lượng commit
  ❌ Tranh cãi về đóng góp
```

---

#### **Delivery Manager**

```
✓ Track sprint velocity
✓ Detect bottlenecks sớm
✓ Optimize team workflow
✓ Predict & mitigate risks

Không cần:
  ❌ Micromanage từng task
  ❌ Daily status update meetings
  ❌ Manual tracking spreadsheets
```

---

## ⚠️ Cảnh báo: Data không phải TẤT CẢ

### ✅ Làm đúng

```
GitHub data + Context

Ví dụ:
  Dev A: 50 commits
  Dev B: 100 commits

  ❌ SAI: Dev B productive hơn gấp đôi
  ✅ ĐÚNG: Cần xem context:
    - Dev A: Core backend (complex, ít code)
    - Dev B: UI frontend (nhiều code, less complex)

→ Không thể so sánh trực tiếp
```

---

### ❌ Làm sai

```
❌ Chỉ nhìn commit count
❌ So sánh máy móc giữa roles khác nhau
❌ Dùng metrics để punish
❌ Chase số lượng, bỏ qua chất lượng
```

---

## 🎯 Nguyên tắc sử dụng GitHub Data

### 1. **Data để HỖ TRỢ, không để GIÁM SÁT**

```
✅ Dùng data:
  - Phát hiện ai cần help
  - Coaching & improve
  - Recognize contributions
  - Optimize process

❌ KHÔNG dùng data:
  - Micromanage real-time
  - Punish vì số liệu thấp
  - So sánh sai context
```

---

### 2. **Kết hợp: Data + Human judgment**

```
Formula:

Evaluation = GitHub Data (60%) + Tech Lead Review (30%) + Team Feedback (10%)

Không được:
  ❌ 100% dựa vào data (máy móc)
  ❌ 100% dựa vào cảm giác (thiên vị)
```

---

### 3. **Minh bạch: Team thấy được metrics**

```
✅ Team nên:
  - Thấy được dashboard của họ
  - Hiểu cách metrics được tính
  - Có cơ hội giải thích

❌ KHÔNG:
  - Metrics bí mật, chỉ HR biết
  - Đột ngột dùng metrics để đánh giá
  - Team không hiểu mình bị đánh giá thế nào
```

---

## 📊 Ví dụ: GitHub Insights

### GitHub cung cấp sẵn Insights tab

```
Repository → Insights → Contributors

Bạn sẽ thấy:
  - Commits over time
  - Code frequency (additions vs deletions)
  - Top contributors
  - Commit activity
```

**Lưu ý:**
- Đây chỉ là starting point
- Cần kết hợp với custom metrics (file 05)

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu tại sao GitHub là single source of truth
- [ ] Biết GitHub ghi nhận được gì (Issues, PRs, Commits, Reviews)
- [ ] Phân biệt được: "Bận" vs "Productive" vs "Quality"
- [ ] Hiểu nguyên tắc: Data để hỗ trợ, không để giám sát
- [ ] Biết phải kết hợp Data + Human judgment
```

---

**🚀 Tiếp theo: [02-task-creation-standard.md](./02-task-creation-standard.md) - Chuẩn hóa tạo task để đo được**
