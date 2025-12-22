# 03 - Task Status Workflow

> **Mục tiêu**: Chuẩn hóa trạng thái task để tracking chính xác

**Thời lượng**: 45 phút
**Đối tượng**: PO, DM

---

## 🎯 Tại sao cần chuẩn hóa Status?

### Vấn đề khi status KHÔNG chuẩn

```
❌ Dev tự đổi status tuỳ ý
❌ Không biết task đang ở đâu
❌ Không đo được cycle time
❌ Không phát hiện bottleneck
```

---

## 📊 Standard Workflow

### Workflow chuẩn (7 statuses)

```
┌─────────┐   ┌──────┐   ┌─────────┐   ┌────────┐   ┌────────┐   ┌──────┐
│ Backlog │ → │ Ready│ → │In Progre│ → │ Review │ → │ Testing│ → │ Done │
└─────────┘   └──────┘   └─────────┘   └────────┘   └────────┘   └──────┘
                                            ↓
                                       ┌────────┐
                                       │ Rework │
                                       └────────┘
```

### Định nghĩa từng Status

| Status | Ý nghĩa | Ai chịu trách nhiệm | Metrics |
|--------|---------|---------------------|---------|
| **Backlog** | Chưa lên kế hoạch | PO | Backlog size |
| **Ready** | Sẵn sàng làm sprint này | PO | Sprint capacity |
| **In Progress** | Đang code | Dev | WIP count |
| **Review** | Đang code review | Dev + Reviewer | Review time |
| **Rework** | Cần sửa lại | Dev | Rework rate |
| **Testing** | QA đang test | QA | Test time |
| **Done** | Hoàn thành | - | Cycle time |

---

## 🔐 Quyền đổi Status

### Rules

```
Backlog → Ready: CHỈ PO (sprint planning)
Ready → In Progress: Dev (tự pick)
In Progress → Review: Dev (tạo PR)
Review → Rework: Reviewer (request changes)
Review → Testing: Reviewer (approve + merge)
Rework → Review: Dev (fix xong)
Testing → Done: QA (test pass)
Testing → Rework: QA (test fail)
```

**❌ KHÔNG được:**
- PM đổi "In Progress" → "Done" thay dev
- Dev tự đổi "In Progress" → "Done" mà chưa test

---

## ✅ Checklist sau khi đọc

```markdown
- [ ] Hiểu 7 statuses chuẩn
- [ ] Biết ai đổi status nào
- [ ] Biết khi nào task "Done"
```

---

**🚀 Tiếp theo: [04-issue-branch-pr-connection.md](./04-issue-branch-pr-connection.md)**
