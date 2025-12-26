# 01 - Core/Client Separation (Nguyên tắc giữ Core sạch)

> **Mục tiêu**: Hiểu và áp dụng đúng nguyên tắc tách biệt Core và Client code

**Thời lượng**: 60 phút
**Đối tượng**: Dev, Tech Lead, Architect
**Áp dụng cho**: Product/Agency có nhiều khách hàng (MangoAds, MGS, SaaS)

---

## 🎯 Triết lý cốt lõi (BẮT BUỘC PHẢI NHỚ)

### Core là sản phẩm – KHÔNG phải dự án khách hàng

```
Core Product = Giải pháp phổ quát cho NHIỀU khách hàng

Core tồn tại để:
  ✓ Phục vụ nhiều khách hàng
  ✓ Giải quyết bài toán chung
  ✓ Có thể open-source / bán SaaS / reuse lâu dài
  ✓ Scale team & business mà không vỡ Git

→ Core PHẢI sạch, generic, không dính vết khách hàng
```

---

## 🏗️ Mô hình kiến trúc

### Architecture Pattern: Core + Extensions

```
┌─────────────────────────────────────────────────┐
│              CORE PRODUCT                       │
│  (MangoAds / MGS / MongoREST)                   │
│                                                 │
│  - Generic features                             │
│  - Common business logic                        │
│  - Extensible architecture                      │
│  - Configuration-driven                         │
└────────────┬────────────────────────────────────┘
             │
             │  Imported as dependency/submodule
             │
    ┌────────┴────────┬─────────────┬──────────────┐
    │                 │             │              │
┌───▼────┐      ┌────▼─────┐  ┌───▼──────┐  ┌───▼──────┐
│Client A│      │Client B  │  │Client C  │  │Client D  │
│Custom  │      │Custom    │  │Custom    │  │Custom    │
└────────┘      └──────────┘  └──────────┘  └──────────┘
```

**Ví dụ thực tế:**

```
mgs-core/              (Core product - generic)
  ├── src/
  │   ├── auth/        (Generic auth)
  │   ├── policy/      (Generic policy engine)
  │   └── schema/      (Generic schema)
  └── config/          (Default config)

mgs-client-abc/        (Client A customization)
  ├── core/            (mgs-core as submodule)
  ├── custom/
  │   ├── logo-abc.png
  │   ├── theme-abc.css
  │   └── business-rule-abc.js
  └── config.abc.json

mgs-client-xyz/        (Client B customization)
  ├── core/            (mgs-core as submodule)
  ├── custom/
  │   ├── logo-xyz.png
  │   └── integration-xyz.js
  └── config.xyz.json
```

---

## 📜 Quy tắc bất biến của Core Repository

### 1️⃣ Core CHỈ chứa

✅ **Được phép:**

```
✓ Tên hệ thống nội bộ:
  - MangoAds
  - MGS (Mango Grant System)
  - MongoREST
  - Internal module names

✓ Generic features:
  - User authentication (chung cho tất cả)
  - Permission system (configurable)
  - Workflow engine (extensible)

✓ Extensible architecture:
  - Hooks
  - Plugins
  - Configuration schema
  - Policy engine
```

**Ví dụ code ĐÚNG trong Core:**

```javascript
// ✅ ĐÚNG: Generic, configurable
class AuthService {
  constructor(config) {
    this.providers = config.authProviders; // Configurable
    this.policies = config.policies;       // Extensible
  }

  authenticate(credentials) {
    // Generic authentication logic
    return this.providers.find(p => p.supports(credentials))
      .authenticate(credentials);
  }
}

// ✅ ĐÚNG: Hook-based extension
class WorkflowEngine {
  executeWorkflow(workflow, context) {
    // Core workflow logic
    this.hooks.beforeExecute?.call(context);
    const result = this._execute(workflow, context);
    this.hooks.afterExecute?.call(context, result);
    return result;
  }
}
```

---

### 2️⃣ Core KHÔNG BAO GIỜ chứa

❌ **CẤM tuyệt đối:**

```
✗ Tên khách hàng
✗ Logo khách hàng
✗ Business rule riêng cho 1 khách
✗ Hard-code config theo khách
✗ Điều kiện if/else theo customer ID
✗ API keys/credentials của khách
✗ Custom UI themes cho 1 khách
```

**Ví dụ code SAI trong Core:**

```javascript
// ❌ SAI: Hard-code tên khách hàng
if (clientId === 'ABC-Corporation') {
  return this.getABCSpecialDiscount();
}

// ❌ SAI: Business rule riêng cho 1 khách
function calculateFee(amount) {
  if (customer === 'XYZ-Company') {
    return amount * 0.05; // XYZ gets 5% fee
  }
  return amount * 0.10;
}

// ❌ SAI: Hard-code logo path
const logoPath = '/assets/logos/client-abc-logo.png';

// ❌ SAI: Điều kiện theo khách trong core
switch (clientName) {
  case 'ClientA':
    return policyA;
  case 'ClientB':
    return policyB;
  // ...
}
```

---

### ❗ Nguyên tắc phát hiện vi phạm

```
Test đơn giản:

Đọc code trong Core và tự hỏi:
  "Nhìn đoạn code này, tôi có đoán được đây là cho khách nào không?"

→ Nếu câu trả lời là CÓ → VI PHẠM CORE
→ Phải refactor ngay
```

---

## 🔀 Khi nào PHẢI tách Client Repository riêng?

### Tín hiệu cần tách repo

```
🚩 Tách repo khi:

1. Khách yêu cầu custom business logic riêng
   Ví dụ: "Công ty ABC cần approval workflow 5 cấp,
           khác hoàn toàn workflow chuẩn 2 cấp"

2. Khách có branding/UI hoàn toàn khác
   Ví dụ: Theme, logo, color scheme riêng biệt

3. Khách cần tích hợp hệ thống đặc thù
   Ví dụ: SSO với AD riêng, API gateway riêng

4. Khách có SLA/deployment khác nhau
   Ví dụ: On-premise vs Cloud, khác data center

5. Khách cần version độc lập
   Ví dụ: Client A dùng core v2.5, Client B dùng core v3.0
```

---

### Quy trình tách repo

```
Bước 1: Tạo Client Repository

  git clone template-client-repo mgs-client-abc
  cd mgs-client-abc

Bước 2: Add Core as Submodule

  git submodule add https://github.com/org/mgs-core.git core
  git submodule update --init --recursive

Bước 3: Tạo cấu trúc Client

  mgs-client-abc/
    ├── core/                 (submodule → mgs-core)
    ├── custom/
    │   ├── config/
    │   │   └── client-abc.json
    │   ├── assets/
    │   │   ├── logo.png
    │   │   └── theme.css
    │   ├── plugins/
    │   │   └── abc-workflow.js
    │   └── integrations/
    │       └── abc-sso.js
    ├── .env.abc
    └── README.abc.md

Bước 4: Import Core + Override

  // main.js
  import CoreApp from './core';
  import customConfig from './custom/config/client-abc.json';
  import customPlugins from './custom/plugins';

  const app = new CoreApp({
    ...customConfig,
    plugins: customPlugins
  });
```

---

## 🔄 Quy tắc cập nhật ngược (Core ← Client)

### Nguyên tắc vàng

```
Khi code cho Client mà phát hiện:
  "Tính năng này nếu đưa vào Core thì nhiều khách khác cũng dùng được"

→ DỪNG code trong Client repo
→ Trừu tượng hóa và đưa vào Core TRƯỚC
→ Sau đó Client dùng Core version mới
```

---

### Quy trình BẮT BUỘC

```
Step 1: DỪNG code trong Client repo
  - Không tiếp tục code custom nữa
  - Document use case

Step 2: Đưa logic về Core (trừu tượng hóa)
  - Tạo branch trong mgs-core: feature/generic-approval-workflow
  - Code generic version (config-driven)
  - Write tests
  - Create PR

Step 3: Review & Merge vào Core
  - Tech Lead review
  - Merge vào core/dev
  - Release new core version (v2.6.0)

Step 4: Update Core version trong Client repo
  cd mgs-client-abc/core
  git checkout v2.6.0
  cd ..
  git add core
  git commit -m "Update core to v2.6.0 (generic approval workflow)"

Step 5: Config trong Client
  // custom/config/client-abc.json
  {
    "approvalWorkflow": {
      "levels": 5,
      "approvers": [...]
    }
  }
```

---

### ❌ CẤM tuyệt đối

```
✗ Copy code từ Client repo ngược vào Core
✗ Merge ngược bừa bãi
✗ Cherry-pick commits từ Client vào Core

→ Core phải được phát triển có chủ đích
→ Không phải "nhặt lại code"
```

---

## 🎨 Thiết kế Core để KHÔNG CẦN tách

### Nguyên tắc: Tách là phương án cuối cùng

```
"Nếu phải tách repo → có thể Core chưa đủ tốt"
```

### Checklist trước khi quyết định tách

```markdown
Trước khi tách Client repo, tự hỏi:

- [ ] Có thể giải quyết bằng **Configuration** không?
      (config file, environment variables)

- [ ] Có thể dùng **Plugin system** không?
      (load custom plugins at runtime)

- [ ] Có thể dùng **Hook mechanism** không?
      (beforeSave, afterCreate hooks)

- [ ] Có thể dùng **Policy-based** không?
      (define rules in database/config)

- [ ] Có thể dùng **Feature flags** không?
      (enable/disable features per client)

- [ ] Có thể dùng **Theme system** không?
      (CSS variables, theme config)

- [ ] Có thể dùng **Schema extension** không?
      (dynamic fields, custom attributes)

→ Nếu TẤT CẢ đều KHÔNG → Lúc đó mới tách repo
```

---

### Ví dụ thiết kế Core tốt (ít cần tách)

```javascript
// ✅ Config-driven
class PermissionService {
  constructor(config) {
    this.rules = config.permissionRules; // Load từ config
  }

  can(user, action, resource) {
    return this.rules.evaluate(user, action, resource);
  }
}

// ✅ Plugin-based
class App {
  use(plugin) {
    plugin.install(this);
  }
}

// Client custom plugin
class ABCWorkflowPlugin {
  install(app) {
    app.workflows.register('abc-approval', this.customWorkflow);
  }
}

// ✅ Hook-based
class DataService {
  async save(data) {
    await this.hooks.beforeSave?.(data);
    const result = await this.db.save(data);
    await this.hooks.afterSave?.(result);
    return result;
  }
}

// ✅ Schema extension
const baseSchema = {
  name: String,
  email: String
};

const clientABCSchema = {
  ...baseSchema,
  customField1: String,  // Client-specific field
  customField2: Number
};
```

---

## 🌿 Git Workflow: Core & Client

### Branch Strategy

#### Core Repository

```
main              → Stable release (v2.5.0, v2.6.0)
  └── dev         → Integration branch
      ├── feature/auth-policy
      ├── feature/workflow-engine
      ├── fix/schema-validation
      └── refactor/core-scope
```

**Rules:**

```
✓ Mọi dev BẮT BUỘC làm việc trên branch riêng
✓ KHÔNG ai code trực tiếp trên dev hay main
✓ Mỗi task → 1 branch → 1 PR

Branch naming:
  feature/auth-policy
  fix/schema-validation
  refactor/core-scope-engine
```

---

#### Client Repository

```
main              → Production cho Client ABC
  └── dev         → Staging cho Client ABC
      ├── custom/abc-branding
      ├── custom/abc-workflow
      └── core/   (submodule → mgs-core v2.5.0)
```

---

### Merge Strategy

```
┌──────────────────────────────────────────────┐
│  Feature Branch → dev (resolve conflict)     │
│                                              │
│  Dev: Review, Test, Resolve Conflicts        │
│                                              │
│  dev → main (only after QA approval)         │
└──────────────────────────────────────────────┘

❌ CẤM: Merge thẳng feature → main
✅ PHẢI: feature → dev → main
```

---

## ⚔️ Xử lý Conflict: QUY TẮC BẮT BUỘC

### Khi xảy ra conflict

```
Git báo conflict:

  CONFLICT (content): Merge conflict in src/policy/index.js
  Automatic merge failed; fix conflicts and then commit the result.
```

---

### ❌ CẤM tuyệt đối

```
✗ Tự ý giữ code của mình (Accept Current Change)
✗ Tự ý giữ code của người khác (Accept Incoming Change)
✗ Ghi đè code người khác mà không hỏi
✗ Merge cho xong rồi push luôn
✗ "Để sau tôi fix" (rồi quên)
```

---

### ✅ QUY TRÌNH BẮT BUỘC

```
Step 1: DỪNG - Không tự ý resolve

Step 2: Liên hệ người viết code conflict
  Slack: "@dev-bob Anh ơi, em merge branch em vào dev
          bị conflict với code của anh ở file policy/index.js
          Anh rảnh 10 phút để hai đứa ngồi cùng resolve không?"

Step 3: Hai bên ngồi cùng nhau (physical/zoom)
  - Hiểu: Vì sao code được viết
  - Hiểu: Ý đồ kiến trúc
  - Thống nhất: Cách merge đúng

Step 4: Cùng resolve
  - Review both changes
  - Discuss best approach
  - Write code cùng nhau
  - Test cùng nhau

Step 5: Document decision
  // Commit message
  Resolve conflict in policy engine

  Discussed with @dev-bob:
  - Kept new validation logic from feature/auth-policy
  - Integrated with existing scope logic from feature/scope-engine
  - Tested: both features work together
```

---

### 💡 Hiểu đúng về Conflict

```
Conflict KHÔNG phải lỗi

Conflict là DẤU HIỆU:
  → Kiến trúc đang chồng chéo
  → Hai người đang giải quyết cùng 1 vấn đề
  → Cần refactor hoặc phân chia rõ hơn

Conflict là CƠ HỘI:
  → Review lại kiến trúc
  → Học cách người khác code
  → Cải thiện communication
```

---

## 📊 Version Management

### Core Versioning (Semantic Versioning)

```
MAJOR.MINOR.PATCH

v2.5.3
 │ │ │
 │ │ └─ Patch: Bug fixes (backward compatible)
 │ └─── Minor: New features (backward compatible)
 └───── Major: Breaking changes

Ví dụ:
  v2.5.0 → v2.5.1  (bug fix)
  v2.5.1 → v2.6.0  (new feature: workflow engine)
  v2.6.0 → v3.0.0  (breaking: new auth system)
```

---

### Client Version Tracking

```
Client repo track Core version:

# .core-version
CORE_VERSION=v2.5.0
LAST_UPDATED=2024-06-15
CHANGELOG=Updated core for new workflow features

# Or in package.json
{
  "dependencies": {
    "mgs-core": "2.5.0"
  }
}

# Or submodule commit
cd core
git checkout v2.5.0
cd ..
git add core
git commit -m "Update core to v2.5.0"
```

---

## 📋 Checklist Review PR

### Core PR Checklist

```markdown
Code Review Checklist (Core):

## Kiến trúc
- [ ] KHÔNG chứa tên khách hàng
- [ ] KHÔNG hard-code business rule riêng
- [ ] Generic, reusable cho nhiều clients
- [ ] Config-driven / Plugin-based / Hook-based

## Code Quality
- [ ] Tests pass
- [ ] Code coverage > 80%
- [ ] No security vulnerabilities
- [ ] Documentation updated

## Breaking Changes
- [ ] Có breaking changes không?
- [ ] Nếu có: Version bump (MAJOR)
- [ ] Migration guide cho clients

## Examples
- [ ] Có ví dụ sử dụng
- [ ] Có config example
```

---

### Client PR Checklist

```markdown
Code Review Checklist (Client):

## Customization
- [ ] Custom code nằm trong /custom folder
- [ ] KHÔNG modify core code trực tiếp
- [ ] Config overrides rõ ràng

## Core Version
- [ ] Core version documented
- [ ] Tested with core version này
- [ ] No breaking changes

## Client-specific
- [ ] Logo/branding correct
- [ ] Business rules match requirements
- [ ] Integration tested
```

---

## 🚨 Anti-patterns (Lỗi thường gặp)

### ❌ Anti-pattern 1: "Quick hack trong Core"

```javascript
// ❌ SAI: Hard-code cho nhanh
if (req.clientId === 'ABC') {
  return this.handleABCSpecialCase();
}

// Lý do dev làm vậy:
"Deadline gấp, code nhanh cho client ABC ship trước,
 sau refactor" → Nhưng không bao giờ refactor

// ✅ ĐÚNG: Thiết kế extension point
return this.hooks.specialCase?.(req)
    || this.defaultHandler(req);

// Client ABC plugin:
hooks.specialCase = (req) => {
  if (needSpecialHandling(req)) {
    return handleSpecial(req);
  }
};
```

---

### ❌ Anti-pattern 2: "Copy-paste từ Client vào Core"

```
Scenario:
  Client ABC có feature hay
  → Dev copy nguyên si code vào Core
  → Giữ nguyên logic riêng của ABC
  → Các client khác xài bị lỗi

✅ ĐÚNG:
  → Trừu tượng hóa logic
  → Make it generic
  → Parameterize client-specific parts
  → Test với nhiều configs
```

---

### ❌ Anti-pattern 3: "Nhánh Client tồn tại mãi mãi"

```
Scenario:
  Tạo branch: client-abc trong Core repo
  → Code custom cho ABC trong đó
  → Không bao giờ merge
  → Branch tồn tại 6 tháng, 1 năm...
  → Conflict khủng khiếp khi merge

✅ ĐÚNG:
  → Tách Client repo riêng
  → Core repo CHỈ có: main, dev, feature branches
  → Feature branches sống < 1 tuần
```

---

### ❌ Anti-pattern 4: "Submodule hell"

```
Scenario:
  Update core submodule bừa bãi
  → Mỗi client ở core version khác nhau
  → Không document
  → Không test
  → Bug xuất hiện không biết tại sao

✅ ĐÚNG:
  → Document core version clearly
  → Test sau mỗi lần update
  → Changelog rõ ràng
  → Rollback plan
```

---

## 🎯 Best Practices Summary

### Nguyên tắc vàng

```
1. Core là sản phẩm - giữ SẠCH, GENERIC
2. Client là customization - tách RIÊNG
3. Conflict là cơ hội - ngồi CÙNG resolve
4. Version là kỷ luật - track CHẶT CHẼ
5. Thiết kế là chìa khóa - EXTENSIBLE > CUSTOM
```

---

### Câu hỏi tự kiểm tra

```
Trước khi commit code vào Core, tự hỏi:

1. Code này có dính tên khách hàng không?
2. Code này có generic cho nhiều clients không?
3. Code này có extensible không?
4. Code này có break existing clients không?
5. Version có cần bump không?

Nếu có BẤT KỲ câu trả lời "KHÔNG chắc"
→ Hỏi Tech Lead trước khi merge
```

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu triết lý: Core = Product, Client = Customization
- [ ] Biết khi nào PHẢI tách Client repo
- [ ] Biết quy trình update ngược: Client → Core
- [ ] Biết thiết kế Core để ít cần tách (config/plugin/hook)
- [ ] Biết Git workflow: branch strategy, merge strategy
- [ ] Biết xử lý conflict ĐÚNG (ngồi cùng nhau)
- [ ] Biết version management
- [ ] Biết anti-patterns phải tránh
- [ ] Commit áp dụng vào công việc hàng ngày
```

---

**🚀 Tiếp theo: [02-branch-strategy.md](./02-branch-strategy.md) - Chi tiết Branch Strategy & Workflow**
