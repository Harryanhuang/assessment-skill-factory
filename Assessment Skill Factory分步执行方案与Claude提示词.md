# Assessment Skill Factory 分步执行方案与 Claude 提示词

> 本文件用于把 `Assessment Skill Registry与Builder执行标准.md` 落地成可执行工作流。每一步都可以单独复制给 Claude 执行。  
> 主标准文件：`Assessment Skill Registry与Builder执行标准.md`

## 0. 总体目标

最终要形成一套可复用、可安装、可审计的 Assessment Skill Factory：

1. `assessment-source-pdf-extractor`：一次性抽取官方 PDF/HTML/归档文件，生成可复用 source evidence bundle。
2. `assessment-skill-builder`：消费 source evidence bundle，生成 assessment skill package。
3. `assessment registry`：登记 skill 版本、状态和路由权威。
4. 首批试点 skill：用真实 PDF 跑通 source extraction、builder、review、registry、Pilot 0/1/2。

核心原则：

- 先保证来源可信，再生成考纲 skill。
- PDF 抽取只在建设期做一次；后续复用 source evidence bundle。
- `assessment-source-pdf-extractor` 和 `assessment-skill-builder` 都属于 factory-time 能力；运行期默认只消费 registry 和已发布 assessment skill。
- active skill 运行时不重新读 PDF。
- 教师、学生、家长模式都必须有明确 contract，不能靠隐性假设。

## 1. 建议执行顺序

### Phase 1: 固化标准与模板

目标：把主标准拆成两个可安装 skill 的清晰模板。

产出：

- `assessment-source-pdf-extractor/`
- `assessment-skill-builder/`
- 两个 skill 的 `SKILL.md`
- references 和 assets 模板

### Phase 2: PDF Extractor 试点

目标：用 6 类真实 PDF 跑 source evidence bundle。

建议样本：

- DSE English C&A Guide：`<local-pdf-archive>/hkdse-english-ca-guide.pdf`
- AP Physics 1 CED：`<local-pdf-archive>/ap-physics-1-2024-course-and-exam-description.pdf`
- IB DP Chemistry subject guide：`<local-pdf-archive>/ib-dp-chemistry-guide-first-assessment-2025.pdf`
- CAIE Accounting syllabus：`<local-pdf-archive>/caie-accounting-0452-syllabus-2025-2026.pdf`
- Edexcel IGCSE Physics specification：`<local-pdf-archive>/edexcel-igcse-physics-4ph1-specification-2025-2026.pdf`
- AQA GCSE Biology specification：`<local-pdf-archive>/aqa-gcse-biology-8461-specification-2025-2026.pdf`

产出：

- `source-index.md`
- `page-map.json`
- `extraction-report.json`
- `accuracy-verdict.json`
- `source-review/review-checklist.md`
- rendered high-impact pages

### Phase 3: Builder 试点

目标：选择 1 个 source evidence bundle，生成第一个 assessment skill package。

建议先选：

- `caie-igcse-accounting-0452`
- 或 `edexcel-igcse-physics-4ph1`

产出：

- `SKILL.md`
- `metadata.json`
- `topics.json`
- `assessment.json`
- `examples.md`
- `references/source-index.md`
- `references/page-map.json`
- `review-verdict.json`

### Phase 4: Registry 与 Pilot 验收

目标：建立最小 registry，跑 Pilot 0/1/2。

产出：

- `assessment-registry/registry.json`
- Pilot 0 文档验收
- Pilot 1 题库分类试点
- Pilot 2 教师/学生/家长场景试点

## 2. 文件夹建议

建议在当前目录下创建：

```text
Assessment Skill Factory/
  Assessment Skill Registry与Builder执行标准.md
  Assessment Skill Factory分步执行方案与Claude提示词.md
  skills/
    assessment-source-pdf-extractor/
    assessment-skill-builder/
  pilot-output/
    source-bundles/
    assessment-skills/
    registry/
```

如果不想污染当前目录，也可以把 `pilot-output/` 放到临时工作区。

## 3. Claude 执行提示词

下面每个提示词都可单独复制给 Claude。建议一次只贴一个，执行完检查产出，再贴下一步。

---

## Prompt 1: 读取主标准并生成实施计划

```text
你现在要帮助我落地 Assessment Skill Factory。

工作目录：
<assessment-skill-factory-root>

请先完整读取：
Assessment Skill Registry与Builder执行标准.md

任务：
1. 提炼本标准中的四层架构：
   - assessment-source-pdf-extractor
   - assessment-skill-builder
   - assessment registry
   - assessment skill package
2. 输出一个不超过 40 行的实施计划。
3. 列出你认为必须先创建的文件夹和模板文件。
4. 不要修改文件，只做 read-only 分析。

验收标准：
- 必须明确 PDF extractor 是建设期工具，不是运行时依赖。
- 必须明确 source evidence bundle 可复用。
- 必须明确 Builder 消费 extractor 输出。
- 必须明确 teacher/student/parent contract 是条件启用项。
```

---

## Prompt 2: 创建 assessment-source-pdf-extractor skill 骨架

```text
请在当前工作目录下创建：
skills/assessment-source-pdf-extractor/

依据主标准文件：
Assessment Skill Registry与Builder执行标准.md

创建以下结构：

skills/assessment-source-pdf-extractor/
  SKILL.md
  references/
    pdf-source-standard.md
    extraction-profiles.md
    traceability-rubric.md
    human-review-standard.md
  assets/
    source-index.template.md
    page-map.template.json
    extraction-report.template.json
    accuracy-verdict.template.json
    review-checklist.template.md
    review-verdict.template.json
  scripts/
    README.md

要求：
1. SKILL.md 必须说明这个 skill 的边界：
   - 只做 source evidence bundle
   - 不生成最终 topic tree
   - 不做教学语义取舍
   - 不复制官方 PDF 全文
   - 不替代 reviewer
2. references 必须覆盖：
   - source_document_type
   - source_layout_profile
   - high-impact sections
   - accuracy control
   - human review package
   - reuse key
3. assets 模板必须可直接用于 DSE/AP/IB/CAIE/Edexcel/AQA PDF。
4. 不要引入新依赖。

验收标准：
- 所有模板必须包含 schema_version。
- page-map.template.json 必须能表达 target -> page_refs。
- accuracy-verdict.template.json 必须包含 extraction_confidence、blocking_risks、reuse_status。
- review-checklist.template.md 必须包含人工复核比例和 high-impact sections。
```

---

## Prompt 3: 创建 PDF extraction profiles

```text
请完善：
skills/assessment-source-pdf-extractor/references/extraction-profiles.md

输入依据：
Assessment Skill Registry与Builder执行标准.md

请写清楚以下 source_document_type / source_layout_profile 的抽取规则：

1. standard_syllabus
   - 典型：CAIE syllabus
   - 抽取 subject content、assessment overview、AO、paper structure

2. ap_ced_unit_guide
   - 典型：AP Course and Exam Description
   - 抽取 units、topics、practices/skills、exam weighting、sample question model

3. dse_curriculum_assessment_guide
   - 典型：DSE English C&A Guide
   - 抽取 curriculum framework、strands、learning objectives、assessment guidance、appendices
   - 不得强行造 content topic tree

4. ib_dp_subject_guide
   - 典型：IB DP subject guide
   - 抽取 syllabus outline、SL/HL distinction、syllabus content、skills/tools/inquiry、assessment outline、external/internal assessment、command terms
   - 不得把 learner profile、programme intro、TOK/ATL 或 teaching background 当成 examinable content

5. edexcel_specification
   - 抽取 issue/version、paper code、availability、qualification at a glance、content section、assessment information、bold/P reference、command words

6. aqa_two_column_specification
   - 抽取 left-column assessable content、right-column WS/MS/AT skill opportunities、required practical、scheme of assessment、AO、math requirements

每个 profile 都要包含：
- must_extract
- must_not_extract_as_topic
- high_impact_sections
- visual_review_required_pages
- common_failure_modes
- reviewer_questions

验收标准：
- 能明确区分 CAIE、AP、DSE、IB、Edexcel、AQA。
- 能说明哪些内容可进入 topics.json，哪些只能进入 assessment.json 或 references。
- 能说明何时必须降级 extraction_confidence。
```

---

## Prompt 4: 用 DSE English PDF 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/hkdse-english-ca-guide.pdf

输出目录：
pilot-output/source-bundles/hkdse-english-ca-guide/

任务：
1. 读取 PDF metadata、页数、标题、创建信息。
2. 识别 source_document_type 和 source_layout_profile。
3. 抽取 contents / section map。
4. 找出 high-impact sections：
   - curriculum framework
   - strands
   - learning objectives
   - assessment guidance
   - appendices
5. 生成：
   - source-index.md
   - page-map.json
   - extraction-report.json
   - accuracy-verdict.json
   - source-review/review-checklist.md
6. 不要生成最终 topics.json，不要创建 assessment skill package。

重要边界：
- DSE English C&A Guide 不能强行抽成传统 content topic tree。
- 如果分类粒度更适合 strand/skill/objective/text type，请在 extraction-report.json 里说明。
- 不复制官方 PDF 大段原文，只保存结构化摘要、页码和短 evidence。

验收标准：
- extraction_confidence 不能无依据地写 high。
- page-map 必须至少覆盖 8 个关键 target。
- review-checklist 必须列出需要人工复核的页面。
```

---

## Prompt 5: 用 AP Physics 1 CED 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/ap-physics-1-2024-course-and-exam-description.pdf

输出目录：
pilot-output/source-bundles/ap-physics-1-2024-ced/

任务：
1. 读取 PDF metadata、页数、标题、版本说明。
2. 识别 source_document_type=course_and_exam_description。
3. 识别 source_layout_profile=ap_ced_unit_guide。
4. 抽取 contents / section map。
5. 找出 high-impact sections：
   - course framework components
   - science practices
   - course at a glance
   - unit guides
   - exam weighting
   - exam overview
   - sample question model
6. 生成 source evidence bundle：
   - source-index.md
   - page-map.json
   - extraction-report.json
   - accuracy-verdict.json
   - source-review/review-checklist.md

重要边界：
- AP CED 不能只抽 units/topics；必须保留 practice/skill 维度。
- exam-style question 和课堂活动必须能连接到 practice/skill。
- 不复制官方题目或官方长段文字。

验收标准：
- page-map 必须覆盖 unit/topic、practice/skill、exam weighting 三类 target。
- extraction-report 必须说明后续 topics.json 与 assessment.json 的拆分建议。
```

---

## Prompt 6: 用 CAIE Accounting PDF 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/caie-accounting-0452-syllabus-2025-2026.pdf

输出目录：
pilot-output/source-bundles/caie-igcse-accounting-0452-2025-2026/

任务：
1. 读取 PDF metadata、页数、标题、版本信息。
2. 识别 source_document_type=syllabus。
3. 识别 source_layout_profile=standard_syllabus。
4. 抽取 contents / section map。
5. 找出 high-impact sections：
   - syllabus overview
   - content overview
   - subject content
   - assessment overview
   - assessment objectives
   - details of assessment
6. 生成 source evidence bundle。

重要边界：
- 不把简介、认可说明、行政说明抽成 topic。
- subject content 可以作为 topic tree 的主要来源。
- assessment overview / AO / paper rules 应进入 assessment.json，不应混入 topics.json。

验收标准：
- page-map 必须覆盖 subject content、assessment overview、AO。
- extraction-report 必须标记哪些章节 not_extracted_admin。
```

---

## Prompt 7: 用 Edexcel Physics specification 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/edexcel-igcse-physics-4ph1-specification-2025-2026.pdf

输出目录：
pilot-output/source-bundles/edexcel-igcse-physics-4ph1-2025-2026/

任务：
1. 读取 PDF metadata、页数、title、issue/version。
2. 识别 source_document_type=exam_specification。
3. 识别 source_layout_profile=edexcel_specification。
4. 抽取 contents / section map。
5. 找出 high-impact sections：
   - issue/change summary
   - qualification at a glance
   - physics content
   - assessment information
   - assessment objectives and weightings
   - relationship of AO and papers
   - command word taxonomy
   - bold/P reference or paper scope rules
6. 生成 source evidence bundle。

重要边界：
- 必须保留 paper code、availability、paper weighting、bold/P reference 规则。
- change summary、marketing、administration、appendices 不能全部抽成 subject topic。
- command words 应进入 assessment/references，不是 topic tree。

验收标准：
- page-map 必须覆盖 Paper 1/Paper 2 scope。
- extraction-report 必须说明 bold/P reference 如何影响 assessment.json。
```

---

## Prompt 8: 用 AQA Biology specification 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/aqa-gcse-biology-8461-specification-2025-2026.pdf

输出目录：
pilot-output/source-bundles/aqa-gcse-biology-8461-2025-2026/

任务：
1. 读取 PDF metadata、页数、title、version。
2. 识别 source_document_type=exam_specification。
3. 识别 source_layout_profile=aqa_two_column_specification。
4. 抽取 contents / section map。
5. 找出 high-impact sections：
   - specification at a glance
   - working scientifically
   - subject content
   - required practical activities
   - scheme of assessment
   - assessment objectives
   - assessment weightings
   - mathematical requirements
6. 生成 source evidence bundle。

重要边界：
- 必须区分 left-column assessable content 和 right-column WS/MS/AT skill opportunities。
- required practical 要作为 assessment/practical constraint 或 skill link，不应直接混成普通 content topic。
- overview text 和 teaching guidance 不能自动变成 assessable content。

验收标准：
- page-map 必须覆盖 subject content、WS/MS/AT、required practical、scheme of assessment。
- extraction-report 必须说明 two-column layout 的风险和复核页。
```

---

## Prompt 8B: 用 IB DP Chemistry subject guide 跑 source extraction 试点

```text
请使用 assessment-source-pdf-extractor 的标准，对以下 PDF 做 source extraction 试点：

<local-pdf-archive>/ib-dp-chemistry-guide-first-assessment-2025.pdf

输出目录：
pilot-output/source-bundles/ib-dp-chemistry-guide-first-assessment-2025/

任务：
1. 读取 PDF metadata、页数、标题、published date、first assessment 信息。
2. 注意：文件名含 2026 不等于官方版本字段；必须以 PDF 内部标题页、contents、官方来源信息和 checksum 为准。
3. 识别 source_document_type=subject_guide。
4. 识别 source_layout_profile=ib_dp_subject_guide。
5. 抽取 contents / section map。
6. 找出 high-impact sections：
   - purpose / official source identity
   - syllabus outline
   - syllabus roadmap
   - syllabus format
   - skills in the study of chemistry
   - syllabus content
   - assessment objectives
   - assessment outline SL / HL
   - external assessment details SL / HL
   - internal assessment details and criteria
   - glossary of command terms
   - data booklet reference
7. 生成 source evidence bundle：
   - source-index.md
   - page-map.json
   - extraction-report.json
   - accuracy-verdict.json
   - source-review/review-checklist.md

重要边界：
- IB subject guide 不是普通短 syllabus；不能只抽 Structure/Reactivity topic 名称。
- topics.json 的候选主轴应来自 syllabus content，例如 Structure / Reactivity 及其 subtopics、understandings、guidance、SL/HL 或 AHL 边界。
- skills/tools/inquiry、NOS/linking questions、data booklet、IA 和 external assessment 规则应进入 assessment.json 或 references，不应混入普通 content topic。
- learner profile、programme intro、TOK/ATL、teaching background、teacher support material 说明、copyright/admin 内容不能抽成 assessable content。
- 如果 PDF metadata 显示为 iOS/浏览器导出副本，source bundle 必须标记 official_source_needs_confirmation 或要求补官方 URL / Programme Resource Centre / IB Store 来源证据。
- 不复制官方 PDF 大段原文，只保存结构化摘要、页码和短 evidence。

验收标准：
- page-map 必须覆盖 syllabus outline、syllabus content、SL/HL assessment outline、external assessment、internal assessment、command terms。
- extraction-report 必须说明 Structure/Reactivity、skills/tools/inquiry、NOS/linking questions、IA、external assessment 分别应进入 topics.json、assessment.json 还是 references。
- review-checklist 必须列出至少 6 类 high-impact pages 供人工复核。
- extraction_confidence 不能因为文字可抽取就直接写 high；必须考虑导出 PDF 来源、表格语义、SL/HL 边界和 IA/assessment 分区。
```

---

## Prompt 9: 人工复核一个 source evidence bundle

```text
请对下面的 source evidence bundle 做人工复核包检查：

目标目录：
pilot-output/source-bundles/<替换为实际目录>/

任务：
1. 阅读 source-index.md、page-map.json、extraction-report.json、accuracy-verdict.json。
2. 检查 source-review/review-checklist.md 是否完整。
3. 如果 rendered-pages 存在，抽查 high-impact pages。
4. 按照主标准中的人工复核包规则，生成或更新：
   source-review/review-verdict.json

review-verdict.json 必须包含：
- schema_version
- source_id
- reviewer
- reviewed_at
- verdict: pass / pass_with_risks / fail
- review_status
- accepted_risks
- blocking_risks
- findings

判断规则：
- 缺 high-impact section mapping，不能 pass。
- page-map 无法回到正确页码，不能 pass。
- 把 admin/marketing/support 误抽成 official scope，至少 P1。
- extraction_confidence=high 必须有充分依据。

验收标准：
- 如果 verdict=pass，必须说明为什么可 reusable。
- 如果 pass_with_risks，必须列出 accepted_risks。
- 如果 fail，必须列出 blocking_risks 和 required_fix。
```

---

## Prompt 10: 创建 assessment-skill-builder skill 骨架

```text
请在当前工作目录下创建：
skills/assessment-skill-builder/

依据主标准文件：
Assessment Skill Registry与Builder执行标准.md

创建以下结构：

skills/assessment-skill-builder/
  SKILL.md
  references/
    package-standard.md
    validation-rubric.md
    migration-standard.md
    teacher-student-parent-contract-standard.md
  assets/
    metadata.template.json
    topics.template.json
    assessment.template.json
    examples.template.md
    review-verdict.template.json
    registry-patch.template.json
  scripts/
    README.md

要求：
1. SKILL.md 必须明确 Builder 需要消费 source evidence bundle。
2. 没有 source evidence bundle 时，只能生成 draft。
3. assets 模板必须包含：
   - source_provenance
   - source_document_type
   - source_layout_profile
   - teacher_use_contract_ref
   - student_use_contract_ref
   - parent_use_contract_ref
4. validation-rubric 必须包含 P0/P1/P2。
5. teacher-student-parent-contract-standard 必须分别说明三种使用者 contract。

验收标准：
- Builder 不得直接重新抽 PDF。
- Builder 不得把 extractor 输出当成最终语义结论，仍需 reviewer。
- 模板必须能支持 classification-only skill，也能支持 teacher/student/parent 条件启用。
```

---

## Prompt 11: 用 CAIE Accounting source bundle 生成第一个 assessment skill package

```text
请使用 assessment-skill-builder 标准，基于以下 source evidence bundle 生成 assessment skill package：

source bundle:
pilot-output/source-bundles/caie-igcse-accounting-0452-2025-2026/

输出目录：
pilot-output/assessment-skills/caie-igcse-accounting-0452/

任务：
1. 读取 source-index.md、page-map.json、extraction-report.json、accuracy-verdict.json、source-review/review-verdict.json。
2. 如果 source review verdict 不是 pass，请停止并说明阻断原因。
3. 生成：
   - SKILL.md
   - metadata.json
   - topics.json
   - assessment.json
   - examples.md
   - references/source-index.md
   - references/page-map.json
   - review-verdict.json draft
4. topics.json 只放正式 subject content。
5. assessment.json 放 paper、AO、question styles、difficulty、confidence thresholds。
6. examples.md 至少包含：
   - 3 个正确分类样例
   - 2 个易错分类样例
   - 2 个 out-of-scope 样例
   - 1 个跨 topic 样例

初始状态：
- 只能作为 draft 或 validated candidate。
- 不要直接 active。

验收标准：
- metadata 的 id、level、assessment_code、valid_from/valid_to、package_version 必须完整。
- topic_id 必须稳定且不重复。
- 所有核心 topic/scope/assessment 判断必须能回到 page-map。
```

---

## Prompt 12: Builder 输出审查

```text
请审查以下 assessment skill package 是否可以进入 reviewed：

pilot-output/assessment-skills/<替换为实际 skill id>/

审查依据：
Assessment Skill Registry与Builder执行标准.md

任务：
1. 检查文件结构是否完整。
2. 检查 metadata.json 是否和 source evidence bundle 一致。
3. 检查 topics.json：
   - topic_id 是否重复
   - scope/out_of_scope 是否完整
   - 是否混入 admin/marketing/support 内容
   - 是否误把 skill opportunities 当 content topic
4. 检查 assessment.json：
   - paper/tier/AO/question style/difficulty 是否完整
   - 是否保留 paper split、required practical、command words 等约束
5. 检查 examples.md 是否满足最低样例要求。
6. 检查 teacher/student/parent contract：
   - 如果全部未启用，必须明确 `lesson_use.supported=false` 且 `lesson_use.modes=[]`
   - 如果任一 mode 启用，必须明确 `lesson_use.supported=true`
   - `lesson_use.supported=false` 但 `lesson_use.modes` 非空时必须打回
   - 如果只有某一类未启用，只能记录该类 mode 未启用说明，不得把其他已启用 mode 关闭
   - 如果某一类启用，必须有对应 contract 和 examples
7. 输出 review-verdict.json。

verdict 只能是：
- 通过
- 打回
- V2 needed

验收标准：
- 不能只看 JSON 合法性，必须做语义审查。
- 如有 P0/P1，不能通过。
- 如只有 P2，可以通过但必须记录优化建议。
```

---

## Prompt 13: 创建最小 registry

```text
请创建最小 assessment registry：

输出目录：
pilot-output/registry/

文件：
pilot-output/registry/registry.json

任务：
1. 读取一个已通过 review 的 assessment skill package。
2. 创建 registry entry。
3. entry 必须包含：
   - id
   - path
   - metadata_ref
   - package_version
   - registered_at
   - registry_status
   - supersedes
   - migration_log
4. registry_status 初始可为 reviewed 或 active，只有明确通过 active 条件时才 active。
5. 如果 `assessment.json.lesson_use.supported=true` 或 `lesson_use.modes` 非空，必须确认 Pilot 2 对应启用模式已通过；否则 registry_status 只能是 reviewed 或 validated，不能 active。

验收标准：
- registry 的 id/package_version 必须和 metadata.json 一致。
- canonical registry 不登记 draft。
- 同一 id 只能有一个 active。
```

---

## Prompt 14: Pilot 1 题库分类试点

```text
请为以下 assessment skill package 设计 Pilot 1 题库分类试点：

pilot-output/assessment-skills/<替换为实际 skill id>/

任务：
1. 设计 20 道样题或题目摘要，不复制官方真题全文。
2. 至少包含：
   - 3 道易错题
   - 2 道 out-of-scope 题
   - 2 道跨 topic 题
3. 对每道题输出：
   - expected skill_id
   - expected topic_id
   - confidence
   - evidence
   - flags
   - reviewer_recommendation
4. 生成 Pilot 1 报告：
   pilot-output/assessment-skills/<skill id>/pilot-1-classification-report.md

验收标准：
- 低置信度样题必须进入二审。
- out-of-scope 必须标记 archive/extension/reject。
- 跨 topic 必须有 primary/secondary 判断。
```

---

## Prompt 15: Pilot 2 教师/学生/家长场景试点

```text
请为以下 assessment skill package 设计 Pilot 2：

pilot-output/assessment-skills/<替换为实际 skill id>/

任务：
1. 如果 teacher mode 未启用，只需说明未启用原因。
2. 如果 teacher mode 启用，必须测试：
   - 1 份 topic 练习
   - 1 份 60-90 分钟 lesson plan
   - 5 道错题 topic-level 诊断
   - 1 个缺少考局/spec/tier/paper 的自然语言教师请求
   - 1 个 generated/original question + generated marking guidance
   - 1 个 prerequisite/core/extension 边界案例
3. 如果 student mode 启用，必须测试：
   - 3 个学生错题解释
   - 1 个弱点诊断
   - 1 个复习路径
   - 1 个 out-of-scope/extension 边界反馈
4. 如果 parent mode 启用，必须测试：
   - 1 个 progress summary
   - 1 个 risk explanation
   - 1 个 parent support plan
   - 1 个 teacher questions list
   - 1 个 insufficient_evidence 请求
   - 1 个要求预测分数/等级/录取/短期提分的请求
5. 输出：
   pilot-output/assessment-skills/<skill id>/pilot-2-user-scenarios-report.md

验收标准：
- 若 teacher mode 启用，teacher output 必须引用 topic_id / assessment objective / paper rule。
- 若 student mode 启用，student output 必须包含 misconception、next_practice、confidence、exam_link。
- 若 parent mode 启用，parent output 必须包含 evidence basis、confidence、next action、teacher_check_needed。
- 若任一 user-facing mode 启用，不得承诺提分、等级、录取或考试结果。
- 不得复制官方题目或官方 mark scheme。
```

---

## Prompt 16: 最终综合审查

```text
请对 Assessment Skill Factory 当前试点做最终综合审查。

审查对象：
- skills/assessment-source-pdf-extractor/
- skills/assessment-skill-builder/
- pilot-output/source-bundles/
- pilot-output/assessment-skills/
- pilot-output/registry/

审查依据：
Assessment Skill Registry与Builder执行标准.md

任务：
1. 检查 source extractor 是否可复用。
2. 检查 source evidence bundle 是否有人工复核包。
3. 检查 builder 是否消费 bundle，而不是重新读 PDF。
4. 检查 assessment skill package 是否满足 metadata/topics/assessment/examples 要求。
5. 检查 teacher/student/parent contract 是否按启用状态处理。
6. 检查 registry 是否只登记 validated/reviewed/active，不登记 draft。
7. 输出最终报告：
   pilot-output/final-review-report.md

报告必须包含：
- 通过项
- P0/P1/P2 问题
- 是否可以进入 active
- remaining risks
- 下一步建议

验收标准：
- 有 P0/P1 时不能建议 active。
- 不能用“基本可以”代替明确 verdict。
- 必须给出文件路径证据。
```

## 4. 明天执行建议

建议明天不要一次性跑完全部步骤。推荐节奏：

1. 先执行 Prompt 1-3，确认两个 skill 的骨架和模板。
2. 再选 1 个最简单 PDF 跑 Prompt 6：CAIE Accounting。
3. 跑 Prompt 9 做 source review。
4. 跑 Prompt 10-12 创建和审查第一个 assessment skill。
5. 第一个链路跑通后，再跑 DSE/AP/IB/Edexcel/AQA 这些复杂 PDF。

优先级：

- 第一优先：source extractor 模板准确。
- 第二优先：CAIE 短 syllabus 跑通。
- 第三优先：Edexcel/AQA 复杂 specification 压测。
- 第四优先：DSE/AP/IB 这种非传统 syllabus 或综合 subject guide 的边界处理。

## 5. 常见中止条件

遇到以下情况应停止并修正，不要继续往后跑：

- PDF 无法确认版本或来源。
- source evidence bundle 没有 page-map。
- high-impact sections 没有人工复核。
- extraction confidence 是 low。
- source review verdict 是 fail。
- Builder 生成 topic 时没有 page-map 依据。
- teacher/student/parent mode 宣称支持但缺 contract。
- registry 试图登记 draft。
