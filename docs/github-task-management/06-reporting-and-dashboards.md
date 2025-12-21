# 06 - Reporting and Dashboards

> **Mục tiêu**: Tạo báo cáo và dashboard để tracking

**Thời lượng**: 45 phút
**Đối tượng**: PO, HR, DM

---

## 📊 Dashboard Views

### View 1: PO Dashboard

**Filter:**
```
Sprint = Current
```

**Columns:**
- Title
- Status
- Owner
- Story Points
- Module
- Days in Progress

**Purpose:** Track sprint progress

---

### View 2: HR Dashboard (Individual Performance)

**Filter:**
```
Assignee = @username
Closed >= Last Quarter
```

**Metrics:**
- Issues completed
- Story points
- Rework rate
- Bug rate

**Purpose:** Performance review

---

### View 3: DM Dashboard (Team Health)

**Metrics:**
- Sprint velocity (trend)
- Sprint completion rate
- Bug escape rate
- Average cycle time

**Purpose:** Team performance tracking

---

## 📈 Monthly Report Template

```markdown
## Team Performance - [Month] 202X

### Velocity
- Avg velocity: X points/sprint
- Trend: [Stable / Growing / Declining]

### Quality
- Rework rate: X%
- Bug rate: X%

### Top Contributors
1. [Name]: X points, Y reviews
2. [Name]: X points, Y reviews
3. [Name]: X points, Y reviews

### Action Items
- [Action 1]
- [Action 2]
```

---

## 🔧 Export Data

### Export to CSV

```
GitHub Projects → Table view → ... → Export CSV
```

### Use in Google Sheets

1. Import CSV
2. Create pivot tables
3. Create charts

---

## ✅ Checklist sau khi đọc

```markdown
- [ ] Biết 3 dashboard views
- [ ] Biết monthly report template
- [ ] Biết cách export data
```

---

**🚀 Tiếp theo: [07-fair-evaluation-guidelines.md](./07-fair-evaluation-guidelines.md)**
