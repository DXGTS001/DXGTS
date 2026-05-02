# CodeNexus — 智能代码审查与自动化修复 Agent

> 🚀 基于多 Agent 协作的下一代代码审查系统，让 AI 像资深工程师一样审查代码、发现问题、自动生成修复 PR。

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue)](https://python.org)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-green)](https://openai.com)

---

## 📖 目录

- [项目简介](#-项目简介)
- [核心痛点](#-核心痛点)
- [系统架构](#-系统架构)
- [快速开始](#-快速开始)
- [使用示例](#-使用示例)
- [Agent 详解](#-agent-详解)
- [配置指南](#-配置指南)
- [性能指标](#-性能指标)
- [贡献指南](#-贡献指南)
- [许可证](#-许可证)

---

## 🎯 项目简介

**CodeNexus** 是一个基于多 Agent 协作的智能代码审查系统，能够：

- 🔍 **自动检测**代码中的安全漏洞、性能瓶颈和风格问题
- 🤖 **智能修复**生成可合并的修复 PR，无需人工介入
- 🧪 **闭环验证**自动运行测试确保修复不引入新问题
- 📊 **数据驱动**提供团队代码质量趋势分析

> **核心价值**：将代码审查时间从平均 **2.4 小时**缩短至 **40 秒**，安全问题拦截率提升至 **94%**。

---

## 💡 核心痛点

| 痛点 | 传统方案 | CodeNexus 方案 |
|------|---------|---------------|
| **审查耗时长** | 人工逐行审查，大型 PR 需 2-4 小时 | 全自动审查，平均 40 秒完成 |
| **安全问题滞后** | 生产环境才发现 SQL 注入、XSS 等漏洞 | 提交时即扫描，拦截 94%+ 安全问题 |
| **规范不统一** | 各团队风格差异大，技术债累积 | 统一规则引擎，自动修复风格问题 |
| **修复成本高** | 发现问题后需人工改代码 | AI 自动生成修复 PR，一键合并 |
| **知识难沉淀** | 审查经验依赖个人，难以传承 | 规则库持续积累，团队共享 |

---

## 🏗️ 系统架构

### 多 Agent 协作架构

```
┌─────────────────────────────────────────────────────────────┐
│  用户提交 PR                                                │
└────────────────────┬────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────────────────────┐
│  [RouterAgent] 路由分析                                      │
│  • 解析变更文件树 • 识别影响模块 • 分配审查优先级              │
└────────────────────┬────────────────────────────────────────┘
                     ↓
        ┌────────────┼────────────┐
        ↓            ↓            ↓
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│[SecurityAgent]│ │ [PerfAgent]  │ │[StyleAgent]  │
│   安全审计    │ │   性能分析    │ │   规范检查    │
│• SQL注入/XSS │ │• 复杂度分析   │ │• ESLint     │
│• 依赖漏洞    │ │• N+1查询     │ │• 命名规范    │
│• 密钥泄露    │ │• 内存泄漏    │ │• 格式统一    │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       └────────────────┼────────────────┘
                        ↓
           ┌────────────────────────┐
           │   [CoordinatorAgent]   │
           │     汇总 & 去重        │
           │   生成统一问题报告      │
           └───────────┬────────────┘
                       ↓
           ┌────────────────────────┐
           │   [FixGenAgent]        │
           │   长链推理生成修复      │
           │   • AST 感知编辑       │
           │   • 上下文保持         │
           │   • 多文件联动修改      │
           └───────────┬────────────┘
                       ↓
           ┌────────────────────────┐
           │   [ValidatorAgent]     │
           │   闭环验证             │
           │   • 单元/集成测试      │
           │   • 安全回归测试       │
           │   • 构建 & 类型检查    │
           └───────────┬────────────┘
                       ↓
              ┌────────────────┐
              │  输出审查报告    │
              │  自动创建修复 PR │
              └────────────────┘
```

### 技术栈

| 层级 | 技术 |
|------|------|
| **Agent 框架** | LangChain + LangGraph |
| **LLM 引擎** | OpenAI GPT-4 / Claude 3.5 Sonnet |
| **代码分析** | Semgrep, CodeQL, ESLint, Prettier |
| **AST 操作** | Babel, TypeScript Compiler API |
| **依赖安全** | Snyk, OWASP Dependency-Check |
| **CI/CD 集成** | GitHub Actions, GitLab CI |
| **部署** | Docker, Kubernetes |

---

## 🚀 快速开始

### 环境要求

- Python 3.10+
- Node.js 18+
- GitHub/GitLab 访问令牌
- OpenAI API 密钥

### 安装

```bash
# 克隆仓库
git clone https://github.com/your-org/codenexus.git
cd codenexus

# 安装依赖
pip install -r requirements.txt
npm install

# 配置环境变量
cp .env.example .env
# 编辑 .env 填入 API 密钥

# 初始化数据库
codenexus db migrate
```

### 基础配置

```yaml
# .codenexus.yml
version: "2.1"

agents:
  router:
    enabled: true
    max_files_per_batch: 20

  security:
    enabled: true
    rulesets:
      - owasp-top-10
      - cwe-top-25
      - custom-payment-rules
    severity_threshold: medium

  performance:
    enabled: true
    max_complexity: 15
    detect_n_plus_1: true

  style:
    enabled: true
    linter: eslint
    config: airbnb-base
    auto_fix: true

fix_generation:
  enabled: true
  confidence_threshold: 0.85
  max_files_per_fix: 5

validation:
  unit_tests: true
  integration_tests: true
  security_regression: true
  type_check: true
  build_check: true

notifications:
  slack_webhook: "${SLACK_WEBHOOK_URL}"
  jira_integration: true
```

---

## 💻 使用示例

### 命令行使用

```bash
# 审查单个 PR
codenexus review --repo github.com/acme/payment-service --pr 1847

# 审查并自动创建修复 PR
codenexus review --repo github.com/acme/payment-service --pr 1847 --auto-fix

# 批量审查仓库所有开放 PR
codenexus review --repo github.com/acme/payment-service --all-open

# 启动交互式仪表板
codenexus dashboard

# 导出审查报告
codenexus report --pr 1847 --format pdf --output report.pdf
```

### GitHub Actions 集成

```yaml
# .github/workflows/codenexus.yml
name: CodeNexus Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run CodeNexus
        uses: codenexus/action@v2
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          openai_api_key: ${{ secrets.OPENAI_API_KEY }}
          auto_fix: true
          config_file: .codenexus.yml
```

### 示例输出

```
[11:37:42] Initializing CodeNexus Engine...
[11:37:42] OK Config loaded: .codenexus.yml
[11:37:43] OK GitHub API connected (rate: 4,982/5,000)

[RouterAgent]    Analyzing PR #1847 scope...
                 Files: 12 | Add: +847 | Del: -203

[SecurityAgent]  WARNING: 3 issues found:
                 [HIGH] gateway.js:142 - SQL injection risk
                 [MED]  jwt.ts:67 - Weak JWT key (HS256)
                 [LOW]  logger.js:23 - Sensitive data leak

[PerfAgent]      WARNING: 2 issues found:
                 [WARN] service.js:89 - Complexity 28 (limit: 15)
                 [WARN] query.js:34 - Potential N+1 query

[FixGenAgent]    Generating fixes...
                 - Fix SQL injection: parameterized query
                 - Fix JWT: upgrade to RS256 + key rotation

[Validator]      Unit tests: 142/142 passed (100%)
                 Integration tests: 38/38 passed (100%)
                 Security regression: 5/5 passed (100%)

[11:38:29] OK Auto-fix PR created: #1848
[11:38:29] Confidence: 94.2%
```

---

## 🤖 Agent 详解

### 1. RouterAgent（路由分析 Agent）

**职责**：分析 PR 变更范围，智能分配审查任务

**核心能力**：
- 解析 Git diff，提取变更文件树
- 识别模块边界和依赖关系
- 根据文件类型和变更量分配审查优先级
- 支持大 PR 分批处理（默认每批 20 个文件）

**输出**：
```json
{
  "pr_id": 1847,
  "files_changed": 12,
  "additions": 847,
  "deletions": 203,
  "modules": ["payment-gateway", "auth-middleware"],
  "priority": "high",
  "batch_count": 1
}
```

### 2. SecurityAgent（安全审计 Agent）

**职责**：全方位安全漏洞扫描

**检测能力**：
| 漏洞类型 | 检测工具 | 示例 |
|---------|---------|------|
| SQL 注入 | Semgrep + 自定义规则 | `gateway.js:142` |
| XSS | CodeQL | `render.ts:56` |
| 弱加密 | Crypto 规则库 | `jwt.ts:67` |
| 依赖漏洞 | Snyk + OWASP | `package.json` |
| 密钥泄露 | GitLeaks + 正则 | `config.yml:12` |
| SSRF | 自定义 AST 规则 | `fetch.ts:89` |

**修复策略**：
- 参数化查询替换字符串拼接
- JWT HS256 → RS256 升级
- 敏感信息 PII 脱敏处理
- 依赖自动升级至安全版本

### 3. PerfAgent（性能分析 Agent）

**职责**：识别性能瓶颈和代码异味

**检测维度**：
- **圈复杂度**：超过阈值（默认 15）触发重构建议
- **N+1 查询**：检测 ORM 懒加载问题
- **内存泄漏**：识别闭包陷阱和事件监听未移除
- **异步阻塞**：检测 `await` 在循环中的低效用法
- **大对象创建**：识别重复对象实例化

### 4. StyleAgent（规范检查 Agent）

**职责**：统一代码风格和命名规范

**支持工具**：
- ESLint（支持自定义规则集）
- Prettier（自动格式化）
- Commitlint（提交信息规范）
- 自定义命名规范（camelCase, PascalCase 等）

### 5. CoordinatorAgent（协调 Agent）

**职责**：汇总多 Agent 结果，去重和优先级排序

**核心逻辑**：
```python
def coordinate_findings(findings: List[Finding]) -> Report:
    # 1. 去重：合并同一位置的多维度问题
    merged = merge_by_location(findings)

    # 2. 优先级排序：安全 > 性能 > 风格
    prioritized = sort_by_severity(merged)

    # 3. 关联分析：识别跨文件问题链
    chains = find_cross_file_chains(prioritized)

    # 4. 生成统一报告
    return generate_report(prioritized, chains)
```

### 6. FixGenAgent（修复生成 Agent）

**职责**：基于长链推理生成精准修复代码

**技术特点**：
- **AST 感知编辑**：在抽象语法树层面操作，保持代码结构完整
- **上下文保持**：确保修复不破坏周边逻辑
- **多文件联动**：处理跨文件引用变更
- **置信度评分**：低于阈值（默认 85%）的修复需人工确认

**修复示例**：

```javascript
// 修复前（SQL 注入）
const query = `SELECT * FROM orders WHERE id = ${req.id}`;

// 修复后（参数化查询）
const query = 'SELECT * FROM orders WHERE id = ?';
await db.execute(query, [req.id]);
```

### 7. ValidatorAgent（验证 Agent）

**职责**：闭环验证修复的正确性

**验证流程**：
1. **单元测试**：运行相关模块的全部测试用例
2. **集成测试**：验证模块间交互是否正常
3. **安全回归**：确保修复不引入新漏洞
4. **类型检查**：TypeScript / Flow 类型验证
5. **构建验证**：确保项目可正常编译构建

---

## ⚙️ 配置指南

### 规则集配置

```yaml
# rules/security.yml
rules:
  - id: sql-injection-dynamic
    pattern: |
      const $QUERY = `...${$VAR}...`;
      await $DB.query($QUERY);
    severity: high
    fix: |
      const $QUERY = '...?...';
      await $DB.execute($QUERY, [$VAR]);

  - id: weak-jwt-algorithm
    pattern: |
      jwt.sign($PAYLOAD, $SECRET, { algorithm: 'HS256' })
    severity: medium
    fix: |
      jwt.sign($PAYLOAD, $PRIVATE_KEY, { algorithm: 'RS256' })
```

### 自定义 Agent

```python
# agents/custom_agent.py
from codenexus import BaseAgent

class CustomAgent(BaseAgent):
    def __init__(self, config):
        super().__init__(config)
        self.name = "CustomAgent"

    async def analyze(self, context: ReviewContext) -> List[Finding]:
        # 自定义分析逻辑
        findings = []
        for file in context.files:
            if self.should_check(file):
                result = await self.check(file)
                findings.extend(result)
        return findings

    async def fix(self, finding: Finding) -> Patch:
        # 自定义修复逻辑
        return await self.generate_patch(finding)
```

---

## 📊 性能指标

### 审查效率

| 指标 | 人工审查 | CodeNexus | 提升 |
|------|---------|----------|------|
| 平均审查时间 | 2.4 小时 | 40 秒 | **216x** |
| 大型 PR (>50 文件) | 4-6 小时 | 2.5 分钟 | **144x** |
| 安全问题发现率 | 60% | 94% | **+57%** |
| 误报率 | - | 8.3% | - |

### 修复质量

| 指标 | 数值 |
|------|------|
| 自动修复成功率 | 87.6% |
| 修复后测试通过率 | 99.2% |
| 修复引入新问题率 | 0.7% |
| 平均置信度评分 | 91.4% |

### 资源消耗

| 指标 | 数值 |
|------|------|
| 平均 Token 消耗 / PR | ~48K |
| 日处理 PR 上限 | ~120 个 |
| 并发 Agent 数 | 5-7 个 |
| 内存占用 | ~2GB |

---

## 🤝 贡献指南

### 开发环境搭建

```bash
# 安装开发依赖
pip install -r requirements-dev.txt
npm install

# 运行测试
pytest tests/ -v
npm test

# 代码检查
ruff check .
eslint src/

# 提交前格式化
ruff format .
prettier --write "src/**/*.{ts,js}"
```

### 提交规范

```
feat: 新增功能
fix: 修复 bug
docs: 文档更新
style: 代码格式调整
refactor: 重构
test: 测试相关
chore: 构建/工具链
```

### 添加新 Agent

1. 在 `agents/` 目录创建新 Agent 类
2. 继承 `BaseAgent` 并实现 `analyze()` 和 `fix()` 方法
3. 在 `config.yml` 中注册 Agent
4. 添加单元测试和集成测试
5. 更新文档

---

## 📄 许可证

[MIT License](LICENSE) © 2026 CodeNexus Team

---

## 🙏 致谢

- [LangChain](https://github.com/langchain-ai/langchain) — Agent 框架
- [Semgrep](https://github.com/returntocorp/semgrep) — 静态分析引擎
- [OpenAI](https://openai.com) — LLM 能力支持
- [GitHub](https://github.com) — API 和 CI/CD 平台

---

> **⭐ 如果这个项目对你有帮助，请给它一个 Star！**
> 
> **📧 有问题或建议？欢迎提交 Issue 或联系 team@codenexus.dev**
