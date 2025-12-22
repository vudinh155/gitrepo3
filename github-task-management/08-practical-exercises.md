# 08 - Practical Exercises (Bài tập thực hành)

> **Mục tiêu**: Thực hành kỹ năng quản lý và đánh giá

**Thời lượng**: 2-3 giờ
**Đối tượng**: PO, HR, DM

---

## 📝 Bài tập cho Product Owner

### Bài tập 1: Tạo Backlog chuẩn

**Đề bài:**

> Stakeholder yêu cầu: "Chúng ta cần thêm tính năng Payment Gateway mới"

**Nhiệm vụ:**

1. Chia thành 5-7 issues
2. Mỗi issue có đầy đủ:
   - Type, Module, Estimate
   - Owner
   - AC & DoD
3. Setup trong GitHub Projects

**Checklist:**
```markdown
- [ ] Epic issue tạo
- [ ] 5-7 child issues
- [ ] Mỗi issue có 1 owner
- [ ] Estimate hợp lý
- [ ] Project board setup
```

---

### Bài tập 2: Sprint Planning

**Đề bài:**

> Team capacity: 60 points
> Backlog: 15 issues (tổng 120 points)
> Chọn issues cho sprint

**Nhiệm vụ:**

1. Prioritize issues
2. Chọn ~ 60 points
3. Ensure balance (feature/bug/task)

---

## 📊 Bài tập cho HR

### Bài tập 3: Đánh giá 3 Devs

**Scenario:**

```
Sprint 15 data:

Dev Alice:
  - Issues: 8 (40 points)
  - Rework rate: 15%
  - Reviews: 12
  - Module: Core backend (complex)

Dev Bob:
  - Issues: 12 (36 points)
  - Rework rate: 35%
  - Reviews: 5
  - Module: Frontend UI (simple)

Dev Charlie (Junior, 6 months):
  - Issues: 4 (20 points)
  - Rework rate: 40%
  - Reviews: 8 (learning)
  - Module: Bug fixes
```

**Questions:**

1. Rank performance (1-3)?
2. Who deserves bonus?
3. Who needs coaching?
4. Explain reasoning

**Answer key:**

```
Ranking:
1. Alice (Exceptional)
   - High complexity work
   - Low rework, high quality
   - Strong collaboration

2. Charlie (Meets expectations for Junior)
   - Growing (20 points good for junior)
   - Rework OK for level
   - Good collaboration

3. Bob (Needs improvement)
   - High rework rate (quality issue)
   - Low collaboration
   - Simple work but still issues

Coaching:
  - Bob: Focus on code quality, review before submit
  - Charlie: Continue growth (on track)
```

---

## 🎯 Case Study: Real Scenarios

### Case 1: Velocity Drop Mystery

```
Dev David velocity:
  Sprint 10: 40 points
  Sprint 11: 40 points
  Sprint 12: 20 points (DROP!)

Manager concerned: "Performance issue?"
```

**Your task:**

1. What questions would you ask?
2. What data would you check?
3. What actions might you take?

**Possible answers:**

```
Questions:
  - What happened in Sprint 12?
  - Any blockers?
  - Any non-sprint work?

Data to check:
  - On-call incidents
  - Production bugs fixed
  - Mentoring activities
  - Meeting time

Possible findings:
  - Sprint 12: David on-call, 5 prod incidents
  - Mentored 2 new juniors
  - Led architecture discussion

Action:
  - NO performance issue
  - Expected drop due to valid reasons
  - Recognize extra contribution
```

---

### Case 2: High Performer or Gamer?

```
Dev Eva:
  - Commits: 300/month (2x team avg)
  - PRs: 40/month (2x team avg)
  - Issues: 20/month (2x team avg)

Looks excellent on paper!
```

**Your task:**

Dig deeper. What else would you check?

**Answer:**

```
Check:
  - Code churn (lines added then deleted)
  - Rework rate
  - PR size (many tiny PRs?)
  - Commit quality (meaningful?)
  - Review feedback (many issues?)

Findings (example):
  - Code churn: 5.0 (very high)
  - Rework: 60% (poor)
  - PRs: Avg 10 lines each (splitting unnecessarily)
  - Commits: "fix", "update", "change" (not meaningful)

Conclusion:
  - Gaming metrics
  - Focus on quantity, not quality
  - Need coaching on quality
```

---

## ✅ Đáp án và học điểm

### Key Takeaways

1. **Always add context**
   - Numbers alone mislead
   - Ask "why" before judging

2. **Compare apples to apples**
   - Same role, same level
   - Adjust for complexity

3. **Look for patterns**
   - One bad sprint ≠ poor performer
   - Trend > single data point

4. **Human first**
   - Metrics support decisions
   - Not replace judgment

---

## ✅ Checklist sau khi đọc

```markdown
- [ ] Làm bài tập PO (backlog, planning)
- [ ] Làm bài tập HR (evaluation)
- [ ] Phân tích case studies
- [ ] Hiểu key takeaways
```

---

**🎉 Hoàn thành bài tập! → Review [09-anti-patterns.md](./09-anti-patterns.md)**
