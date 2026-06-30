# Assessment Skill Registry 与 Builder 执行标准

> 本文件定义通用 assessment skill 的创建、校验、审核、注册与发布标准。它不是 EduFlow 专用规范，而是面向所有可读取或安装 skill 的智能体。

## 1. 定位

Assessment Skill Factory 的目标，是把国际课程、标准化考试、升学笔试和竞赛的考试规则沉淀成可复用、可安装、可审计的 skill 包。

本标准面向所有智能体，包括 Codex、Claude、Gemini、Qwen、Kimi、EduFlow worker 以及后续其他 agent。任何智能体只要能读取 skill 包中的 `SKILL.md`、`metadata.json`、`topics.json`、`assessment.json` 和 `examples.md`，就应该能按同一套考试坐标进行题库分类、校对、出题、备课或诊断。

EduFlow 只是后续可选接入方之一。assessment skill 本身不依赖 EduFlow runtime、Feishu、ClaudeTeam、task 状态或任何特定团队编排系统。

本标准解决的问题：

- 题库分类时先路由到准确的体系、考局、层级、学科和 spec。
- 校对时按官方范围、topic tree、assessment model 和 out-of-scope 规则判断。
- 出题和备课时复用同一套考试坐标，而不是另建教师口径。
- 考纲更新时保留版本轨迹，并能审计旧题库受影响范围。
- 多智能体协作时避免每个 agent 用自己的隐性标准。

## 2. 四层架构

### 2.1 assessment-source-pdf-extractor

`assessment-source-pdf-extractor` 是 assessment skill 创建前的来源抽取 skill。它不生成最终 topic tree，也不决定某个 skill 能否 active。它负责把官方 PDF、HTML 或归档文件转换成可复核的 source evidence bundle，供 `assessment-skill-builder` 使用。

定位说明：`assessment-source-pdf-extractor` 和 `assessment-skill-builder` 默认不进入日常调用面；它们属于建设期能力。运行期默认只消费 canonical registry 和已发布 assessment skill，不反复调用 extractor 或 builder。

职责：

- 识别官方文件形态和版式画像，例如 syllabus、CED、C&A Guide、IB DP subject guide、Edexcel specification、AQA two-column specification。
- 读取 PDF metadata、页数、文档版本、权限、checksum 和本地归档位置。
- 建立 `source-index.md`，记录每个官方来源的版本、页码范围、抽取方式和风险。
- 建立 `page-map.json`，把关键结构化结论映射回 PDF 页码、章节或官方页面定位。
- 标记扫描件、OCR 质量、表格跨页、双栏、脚注、附录、issue/change summary 等抽取风险。
- 输出 extraction report，说明哪些内容已抽取、哪些需要人工复核、哪些不能作为 official scope。
- 将抽取结果固化为可复用的 source evidence bundle；同一官方文件版本和 checksum 不变时，后续创建或修订同一 assessment skill 可直接复用，不必重复抽取 PDF。

`assessment-source-pdf-extractor` 推荐包结构：

```text
assessment-source-pdf-extractor/
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
  scripts/              # 可选：pdfinfo/pdftotext/pdfplumber/ocr 辅助脚本
```

输入必须包含：

- 官方 PDF、官方页面 URL 或本地归档文件。
- 目标 assessment identity 的已知信息；未知时允许留空，但必须标记待确认。
- 期望抽取用途：创建 skill、更新 skill、迁移审计、reviewer 复核。

输出必须包含：

- `source-index.md`。
- `page-map.json` 或 page-map draft。
- `extraction-report.json`。
- `accuracy-verdict.json`。
- `source-review/review-checklist.md`。
- 建议的 `source_provenance` patch。
- extraction confidence 和人工复核清单；reviewer 完成后追加 `source-review/review-verdict.json`。

复用规则：

- `assessment-source-pdf-extractor` 是建设期工具，不是题库分类、出题或备课时的运行时依赖。
- source evidence bundle 的复用键是 `source_id + document_version + file_checksum_sha256`；若 checksum 不可用，则至少使用官方 URL、retrieved_at、document_version、页数和本地归档引用组成复用证据。
- 同一学科、同一考局、同一 spec/version 的 PDF 抽取完成并通过 source traceability review 后，后续 builder、reviewer、migration audit 都应优先复用该 bundle。
- 只有官方 PDF 更新、checksum 改变、文档版本变化、抽取 profile 变化、发现抽取错误或新增更细 page-map 需求时，才需要重跑 extractor。
- assessment skill active 后，router/classifier 默认只读取 registry、metadata、topics、assessment、examples 和 references，不重新读取 PDF。

准确度控制：

- 文件锁定：必须记录页数、PDF metadata、document_version、retrieved_at、local_archive_ref 和 checksum；无法记录 checksum 时，必须说明原因并提高人工复核比例。
- 覆盖率：`source-index.md` 必须覆盖目录中所有与 assessment identity、subject content、assessment model、AO/skills、paper/tier/scope 相关的章节；行政说明、营销说明和版权说明可标记为 `not_extracted_admin`。
- 可追溯率：所有进入 `topics.json`、`assessment.json`、scope/out-of-scope 和 migration 判断的关键结论，必须在 `page-map.json` 中有 page/section mapping；没有 mapping 的结论只能作为 draft note，不能进入 active skill。
- 双通道抽取：对关键页应至少使用两种视角交叉检查，例如 text extraction + rendered page review、目录页 + 正文页、脚本抽取 + 人工阅读。两者冲突时，以原始渲染页面和官方页码为准。
- 抽样复核：reviewer 至少复核所有 high-impact sections，并抽样复核普通 topic mappings。high-impact sections 包括 assessment overview、subject content/table、paper rules、tier rules、bold/P reference、required practical、AO/skills、out-of-scope/forbidden combinations、issue/change summary。
- 风险降级：扫描件、OCR 低质量、双栏错位、表格跨页、页码错位、权限限制、缺 checksum、版本不明或抽取冲突未解决时，extraction confidence 必须降级；降级后 builder 只能生成 `draft` 或 `validated`，不能直接支持 active。
- 错误闭环：发现抽取错误时，必须更新 extraction report、page-map 和复用键状态；旧 bundle 标记为 `superseded` 或 `invalidated`，不能继续被新 builder run 复用。

推荐 accuracy verdict：

```json
{
  "schema_version": "1",
  "source_id": "primary_syllabus",
  "file_locked": true,
  "coverage_status": "complete",
  "traceability_status": "complete",
  "dual_pass_checked": true,
  "review_status": "sampled_pass",
  "extraction_confidence": "high",
  "blocking_risks": [],
  "reuse_status": "reusable"
}
```

`extraction_confidence` 只能使用 `high`、`medium`、`low`。只有 `high` 且无 blocking risks 的 bundle 可以支持 builder 进入 `validated`；`medium` 需要 reviewer 明确接受风险；`low` 只能支持 draft。

人工复核包：

extractor 必须为 reviewer 生成一个最小复核包。人工复核不是重做整本 PDF 抽取，而是对 source evidence bundle 做来源证据审计。

推荐结构：

```text
source-review/
  review-checklist.md
  rendered-pages/          # high-impact pages 和抽样页
  source-index.md
  page-map.json
  extraction-report.json
  accuracy-verdict.json
  review-verdict.json      # reviewer 完成后生成
```

`review-checklist.md` 必须包含：

- PDF identity、document_version、checksum、页数和归档位置是否匹配。
- contents / section map 是否覆盖所有相关章节。
- high-impact sections 是否已渲染并复核。
- page-map 样本是否能回到正确 PDF 页码和章节。
- excluded sections 是否正确标记为 admin、marketing、support、copyright 或 not official scope。
- 表格、双栏、粗体、脚注、P reference、required practical、tier、paper split、command words 等版式语义是否保留。

人工复核比例：

- high-impact sections 必须全查。
- topic/content 普通页至少抽样 10%-20%；每个一级 topic、unit、strand 或 paper scope 至少抽查 1 页。
- 双栏、表格跨页、扫描/OCR、粗体/P reference、required practical、公式表、命令词表、scope 排除规则等高风险页必须全查。
- 若缺 checksum、PDF 权限异常、版本不明或抽取冲突未解决，抽样比例必须提高，并且不能给 `high` confidence。

人工 reviewer 只判断来源证据，不做最终教学设计。必须回答：

- 这条结构化抽取是否被 PDF 支持。
- 页码、章节和版本是否正确。
- 是否把不可考说明误抽成可考内容。
- 是否漏掉考试规则、paper/tier/scope 规则或 out-of-scope 规则。
- 是否有表格、双栏、粗体、脚注或版式语义丢失。

`review-verdict.json` 最小结构：

```json
{
  "schema_version": "1",
  "source_id": "primary_syllabus",
  "reviewer": "reviewer_or_agent_id",
  "reviewed_at": "2026-06-29",
  "verdict": "pass",
  "review_status": "sampled_pass",
  "accepted_risks": [],
  "blocking_risks": [],
  "findings": [
    {
      "severity": "P1",
      "page_ref": "p.14",
      "target": "assessment.papers.P1.scope",
      "issue": "Bold/P reference rule missed.",
      "required_fix": "Add paper scope rule before reuse."
    }
  ]
}
```

`verdict` 只能使用 `pass`、`pass_with_risks`、`fail`。`pass` 才能支持 `reuse_status=reusable`；`pass_with_risks` 只能支持 `medium` confidence，除非 reviewer 明确说明风险不影响 skill 创建；`fail` 必须阻断 builder 进入 `validated`。

边界：

- 它只负责“来源是否可读、可定位、可复核”，不负责最终教学/题库语义取舍。
- 它不能把 PDF 全文复制到 skill package。
- 它不能用 OCR 或模型推断替代官方页码证据。
- 它的输出若未通过 source traceability review，`assessment-skill-builder` 只能生成 `draft`，不能推进到 `validated`。

### 2.2 assessment-skill-builder

`assessment-skill-builder` 是创建标准的元 skill。它不代表某个学科，也不直接做题库分类。它负责规定如何创建、更新、审核和发布一个 assessment skill。

职责：

- 生成统一模板。
- 检查 skill 命名是否稳定。
- 要求读取 `assessment-source-pdf-extractor` 的 source evidence bundle，记录官方来源和适用年份。
- 要求提取 topic tree、assessment model、scope、out-of-scope。
- 要求建立 golden examples。
- 要求通过结构校验和语义验收后才能发布为 `active`。

`assessment-skill-builder` 本身也必须做成可安装 skill，推荐包结构：

```text
assessment-skill-builder/
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
  scripts/              # 可选：只放跨平台 validator 或模板生成脚本
```

触发场景：

- 创建新的 assessment skill。
- 更新已有 assessment skill。
- 审核某个 assessment skill 是否可以 active。
- 从官方 syllabus、CED、考试说明或竞赛范围提取结构化 skill 包。

输入必须包含：

- 目标考试身份或官方来源。
- source evidence bundle；若没有，必须先运行 `assessment-source-pdf-extractor` 或记录人工等价复核证据。
- 目标使用场景：分类、校对、出题、备课、诊断中的哪些。
- 输出位置或安装目标。

输出必须包含：

- skill package 文件结构。
- metadata / topics / assessment / examples 四类核心资产。
- source-index / page-map 引用。
- validate 结果。
- reviewer verdict 模板。
- registry 注册建议。

builder 的 `references/` 与本标准的关系：

- `references/package-standard.md` 是本文件包结构、metadata、topics、assessment、examples 章节的只读投影。
- `references/validation-rubric.md` 是 validator 分级和 reviewer rubric 的只读投影。
- `references/migration-standard.md` 是版本迁移、rollback、反污染规则的只读投影。
- 若主标准和 builder references 冲突，以本文件为准，并将 builder references 标记为 stale。
- 正式创建 `assessment-skill-builder` 时，应在每个 reference 顶部标注来源章节和同步日期。

### 2.3 assessment registry

`assessment registry` 是 skill 索引和版本状态表。它只记录有哪些 skill、在哪里、当前是什么状态、适用哪个考试版本，不承载完整知识树。

职责：

- 支持 router 查找目标 skill。
- 记录已进入 registry 的 `validated`、`reviewed`、`active`、`suspended`、`deprecated`、`archived` 等状态。
- 记录 active 版本和 deprecated 版本。
- 避免把所有考试知识塞进一个大 skill。

registry 是一个真实工件，默认文件名为 `registry.json`。它可以放在任意平台可读取的位置，但在一个项目或知识库中必须只有一个 canonical registry。推荐结构：

```text
assessment-registry/
  registry.json
  migrations/          # 可选：记录 active 切换、回滚和考纲迁移
```

最小 `registry.json` schema：

```json
{
  "schema_version": "1",
  "updated_at": "2026-06-29",
  "entries": [
    {
      "id": "caie-igcse-chemistry-0620",
      "path": "../assessments/caie-igcse-chemistry-0620",
      "metadata_ref": "../assessments/caie-igcse-chemistry-0620/metadata.json",
      "package_version": "2026.1",
      "registered_at": "2026-06-29",
      "registry_status": "active",
      "supersedes": [],
      "migration_log": [],
      "notes": ""
    }
  ]
}
```

写入责任：

- `assessment-skill-builder` 输出 registry 注册建议或 `registry_patch.json`。
- v1 可由人工 reviewer 应用 registry 变更。
- 后续 CLI 可负责写入，但 CLI 不是 registry 的唯一入口。
- 任何自动写入都必须先通过 validator，再由 reviewer 批准 active 切换。
- 若 `assessment.json.lesson_use.supported=true` 或 `lesson_use.modes` 非空，registry_status 只能在对应 Pilot 2 启用模式通过后设为 `active`；否则最高只能设为 `reviewed` 或 `validated`。

字段权威：

- `metadata.json` 是 assessment 身份、来源、适用年份和 `package_version` 的唯一权威源。
- `registry.json` 是 `registry_status` 和当前默认路由选择的唯一权威源。
- `metadata.json` 不写入 `status`、`active` 或 `registry_status`。
- `registry.json` 不重复保存 `assessment_type`、`system`、`board_or_owner`、`subject`、`assessment_code` 等身份字段；需要这些字段时读取 `metadata_ref`。
- `registry.json` 可冗余保存 `package_version`，但只用于路由、迁移和审计快速判断；其值必须与 `metadata.json` 中的 `package_version` 完全一致。
- 若 registry 的 `id`、`path`、`metadata_ref` 或 `package_version` 与 metadata 不一致，将该冲突标记为 P0。
- router 必须先读取 registry，再读取对应 metadata；不能只凭某个 package 内的 metadata 自行判断 active。

registry 冲突规则：

- 同一个 `id + package_version` 在同一个 registry 中只能有一条 canonical entry。
- 同一个 `id` 在同一个 registry 中只能有一个 `registry_status=active` 版本。
- 同一 `assessment_type + system + board_or_owner + subject + assessment_code` 不允许出现两个 active skill；该唯一键从 metadata 读取。
- alias 只能指向 canonical id，不能拥有独立 topic tree。
- deprecated skill 仍可被历史题库引用，但 router 默认不能选 deprecated。
- 若两个 skill 声称覆盖同一 spec，保持二者 `draft` 或 `reviewed`，直到人工确定 canonical id。未进入 registry 的候选包只在 package-local 记录 `draft`，不能参与 router。
- active 切换必须记录 `supersedes` 和迁移说明，不能静默覆盖旧版本。

### 2.4 assessment skill package

`assessment skill package` 是具体单科、单考试、单 spec 的可安装 skill 包。

示例：

- `caie-igcse-chemistry-0620`
- `caie-alevel-chemistry-9701`
- `edexcel-ial-math-wma11`
- `ap-calculus-ab`
- `ib-dp-physics-hl`
- 后期预留：`sat-math`、`oxford-mat`、`cambridge-step-2`、`amc-12`

单项 skill 才负责具体的 topic 归属、题型约束、出题要求、备课顺序和错题诊断。

## 3. 通用包结构

每个 assessment skill package 必须是一个独立文件夹，推荐结构如下：

```text
<skill_id>/
  SKILL.md
  metadata.json
  topics.json
  assessment.json
  examples.md
  references/        # 可选
  scripts/           # 可选
```

### 3.1 SKILL.md

智能体入口说明。必须简洁，说明这个 skill 适用于哪个考试、考局、学科、spec 和使用模式。

必须包含：

- skill 名称和触发说明。
- 使用前必须读取的结构化文件。
- 支持的使用模式。
- 输出 verdict 或分类结果的基本格式。
- 低置信度、跨 topic、out-of-scope 时的处理规则。

### 3.2 metadata.json

记录 assessment identity 和来源。

必填字段：

```json
{
  "schema_version": "1",
  "id": "caie-igcse-chemistry-0620",
  "assessment_type": "syllabus",
  "system": "IGCSE",
  "board_or_owner": "CAIE",
  "subject": "Chemistry",
  "level": "IGCSE",
  "assessment_code": "0620",
  "valid_from": "2026",
  "valid_to": "2028",
  "package_version": "2026.1",
  "source_provenance": [
    {
      "role": "primary_syllabus",
      "title": "Official syllabus",
      "url": "",
      "source_document_type": "syllabus",
      "source_layout_profile": "standard_syllabus",
      "source_format": "pdf",
      "local_archive_ref": "",
      "file_checksum_sha256": "",
      "is_official": true,
      "license_status": "reference_only",
      "document_version": "",
      "retrieved_at": "",
      "page_refs": [],
      "extraction_tool": "",
      "extraction_method": "structured_extract",
      "extraction_verified": false,
      "extraction_note": "Structured extraction only; no full official text copied.",
      "redistribution_note": "Do not redistribute official text; store only structured summaries and short citations.",
      "notes": ""
    }
  ]
}
```

字段说明：

- `schema_version` 标记 metadata schema 版本，便于后续 validator 兼容旧包。
- `assessment_type` 枚举值固定为 `syllabus`、`standardized`、`admissions`、`competition`。v1 只有 `syllabus` 可进入 active；其他类型只能保持 draft 或 reserved。
- `level` 记录学习阶段、考试层级或课程层级，例如 `IGCSE`、`A-Level`、`AP`、`IB DP HL`、`SAT`、`admissions`、`competition`。如果 `system` 已完整表达层级，也仍保留 `level`，便于跨体系 router 使用统一字段。
- `assessment_code` 是统一考试代码字段。syllabus 类可填 spec/syllabus code，standardized test、admissions test、competition 可填考试代码或稳定简称。
- `valid_from` / `valid_to` 使用年份字符串，替代自由文本年份范围，便于后续自动判断是否过期。
- `package_version` 是 skill package 自身版本，不等于 registry 的 active 状态。
- `status` 和 `active` 不写入 metadata；它们只由 registry 管理。
- `source_provenance.role` 用于区分 `primary_syllabus`、`specimen`、`mark_scheme`、`official_course_description`、`other_reference`。
- `source_document_type` 用于区分官方文件形态，至少支持 `syllabus`、`subject_guide`、`course_and_assessment_guide`、`course_and_exam_description`、`curriculum_guide`、`exam_specification`、`specimen_paper`、`mark_scheme`、`errata`、`other_reference`。
- `source_layout_profile` 用于记录同一文件形态下的考局/版式抽取差异，例如 `standard_syllabus`、`ib_dp_subject_guide`、`edexcel_specification`、`aqa_two_column_specification`、`ap_ced_unit_guide`、`dse_curriculum_assessment_guide`。
- `source_format` 至少支持 `pdf`、`html`、`docx`、`scan`、`mixed`、`unknown`。
- `local_archive_ref` 记录本地归档路径、知识库附件路径或受控文件 id；如果只允许在线访问，可留空但必须保留 `url`。
- `file_checksum_sha256` 用于锁定已抽取的 PDF 或本地归档版本；无法计算时可留空，但 reviewer 必须能通过 `url`、文档版本和页码复核。
- `license_status` 至少使用 `reference_only`、`public_official`、`permission_required`、`unknown`。
- `page_refs` 只保存页码、章节号或短定位，不保存官方全文。
- `extraction_tool` 记录 PDF/HTML/文档抽取工具或人工阅读方式，例如 `pdfplumber`、`pdftotext`、`manual_pdf_view`、`browser_reading`。
- `extraction_verified` 表示抽取结果是否已经与原始 PDF 或官方页面进行过二次核对；进入 active 前必须为 `true`。

#### 3.2.1 PDF source handling

大部分考纲、CED、考试说明和 specimen materials 都以 PDF 形式发布。assessment skill 不应该把官方 PDF 全文复制进 skill package，但必须保留可复核的 PDF 来源链。

PDF 处理原则：

- 官方 PDF 或官方页面是 primary source；二手网页、培训机构整理表和模型记忆只能作为辅助线索，不能替代 primary source。
- skill package 中保存结构化提炼、短 evidence、页码/章节定位和来源元数据，不保存大段 PDF 原文。
- 每个 topic tree、assessment model、scope/out-of-scope 的关键判断都应能追溯到 `source_provenance.page_refs` 或 references 中的页码索引。
- 如果 PDF 是扫描版、排版复杂、表格跨页或 OCR 质量低，必须标记 `extraction_method` 和风险说明，不能直接 active。
- 如果同一官方文档存在多个版本或年份，必须用 `document_version`、`valid_from`、`valid_to` 和 checksum/归档引用锁定实际使用版本。
- PDF 抽取可以由脚本、PDF 阅读器、浏览器或人工完成；但进入 active 前必须经过独立 reviewer 对原始 PDF 的抽样复核。

PDF 不能只按“是否能抽文字”判断可用性。Builder 必须先识别官方文件形态，再选择 extraction profile：

| source_document_type | 典型样本 | 主要抽取对象 | 不能直接假设 |
| --- | --- | --- | --- |
| `syllabus` | CAIE syllabus | subject content、assessment objectives、paper structure、grade/series rules | 不能把简介、认可说明、行政说明抽成 topic |
| `subject_guide` | IB DP subject guide | syllabus outline、SL/HL distinction、syllabus content、skills/tools/inquiry、assessment outline、external/internal assessment、command terms | 不能把 learner profile、TOK/ATL/programme intro 或 teaching background 自动当成 examinable content |
| `course_and_exam_description` | AP CED | units、topics、science practices/skills、exam weighting、sample question model | 不能只抽 unit 名称而漏掉 practice/skill 维度 |
| `course_and_assessment_guide` | DSE English C&A Guide | curriculum aims、strands、learning objectives、assessment guidance、appendix skill lists | 不能强行抽成单一 content topic tree |
| `exam_specification` | admissions/standardized test spec | tested domains、question formats、timing、scoring rules | 不能把备考建议当作 official scope |
| `specimen_paper` / `mark_scheme` | specimen materials | question style、marking language、evidence of assessment demand | 不能用样卷反推完整 syllabus scope |

DSE English 这类 `course_and_assessment_guide` 是重要边界案例：它不是短 syllabus，也不只是 exam paper spec。Builder 应把它拆成 curriculum framework、learning objectives/strands、assessment guidance、appendices 四类来源片段，再映射到 `topics.json`、`assessment.json` 和 teacher/diagnostic examples。若无法从该 guide 中得到稳定的 exam-topic tree，应在 skill 中声明分类粒度为 strand/skill/objective，而不是假造 content subtopic。

IB DP subject guide 这类 `subject_guide` 也是重要边界案例：它同时包含 programme background、teaching guidance、syllabus content、skills/tools/inquiry、SL/HL 或 AHL 区分、external assessment、internal assessment 和 command terms。Builder 应把 `Structure/Reactivity` 等 syllabus content 作为 topic tree 主轴，把 `skills/tools/inquiry`、NOS/linking questions、data booklet、IA criteria、paper 1/2 规则放入 `assessment.json` 或 references；不能把 learner profile、TOK/ATL、collaborative project说明、teacher support material 说明直接抽成 assessable content。若本地 PDF 是从 Programme Resource Centre、IB Store 或 iOS/浏览器导出的副本，source bundle 必须额外记录官方来源、retrieved_at、checksum 和权限状态，不能仅凭导出 PDF metadata 认定官方版本。

Edexcel 和 AQA 这类 `exam_specification` 也不能互相套用抽取规则：

- Edexcel International GCSE specification 常包含 issue/change summary、qualification at a glance、content section、assessment information、appendices 和 command word taxonomy。Builder 必须保留 issue/version、paper code、availability、bold/P reference 等 paper scope 规则；不能把 change summary、qualification marketing 或 appendices 全部抽成 subject topic。
- AQA GCSE specification 常包含 `Working scientifically`、subject content、scheme of assessment、required practical activities、mathematical requirements 和 two-column content/skills layout。Builder 必须区分左栏 assessable content、右栏 WS/MS/AT skill opportunities、overview text 和 required practical；不能把 teaching guidance 或 skill opportunity 当作 biology content topic。
- 对于有 tier、paper split、bold-only、P reference、foundation/higher、required practical、command word taxonomy 的 specification，`assessment.json` 必须显式记录这些 routing/assessment constraints；`topics.json` 只记录可考内容坐标和必要 skill links。

推荐在 `references/` 中增加 PDF 索引文件：

```text
references/
  source-index.md        # PDF/HTML 来源列表、版本、页码范围、抽取说明
  page-map.json          # 可选：topic_id / assessment field -> page_refs 映射
```

`page-map.json` 最小结构：

```json
{
  "schema_version": "1",
  "source_id": "primary_syllabus",
  "mappings": [
    {
      "target": "topics.C1.1.scope",
      "page_refs": ["p.12", "Section 3.1"],
      "evidence_note": "Particle model and changes of state are listed in the syllabus content table."
    }
  ]
}
```

建议补充字段：

- `aliases`: 历史名称或常见简称。
- `canonical_id`: alias 指向的正式 id。
- `supersedes`: 被当前版本替代的旧版本。
- `breaking_changes`: 是否存在破坏性考纲变化。
- `platform_install`: 可选安装目标记录，不影响 skill 本体。

### 3.3 topics.json

记录 topic tree 和分类坐标。

每个 topic 至少包含：

- `topic_id`
- `topic_name`
- `parent_id`
- `scope`
- `out_of_scope`
- `classification_hints`
- `common_misclassifications`
- `prerequisite_topics`
- `overlap_links`

要求：

- `topic_id` 必须稳定，不能随批次随意重编号。
- 同一 skill 内 `topic_id` 不得重复。
- topic tree 必须能支撑题库 manifest、coverage 和错题诊断。

最小模板：

```json
{
  "schema_version": "1",
  "topics": [
    {
      "topic_id": "C1.1",
      "topic_name": "States of matter",
      "parent_id": "C1",
      "scope": ["particle model", "changes of state"],
      "out_of_scope": ["A-Level entropy", "university thermodynamics"],
      "classification_hints": ["question asks about solid/liquid/gas particle arrangement"],
      "common_misconceptions": ["Students may describe particles as expanding rather than spacing increasing."],
      "common_misclassifications": [
        {
          "confused_with": "C2.1",
          "reason": "uses particle language but actually tests atomic structure"
        }
      ],
      "prerequisite_topics": [],
      "overlap_links": [
        {
          "target_skill_id": "caie-alevel-chemistry-9701",
          "target_topic_id": "9701-C1",
          "relation": "later_extension",
          "notes": "Related concept exists at A-Level depth; do not classify IGCSE questions into the A-Level topic."
        }
      ]
    }
  ]
}
```

`common_misclassifications.confused_with` 默认只指向同一个 skill 内的 topic。跨 skill 关系必须使用 `overlap_links`，避免污染当前 skill 的主 topic tree。

`common_misclassifications` 是分类易错点，服务 router/classifier；`common_misconceptions` 是学生理解误区，服务备课、出题和错题诊断。两者不能混用。

### 3.4 assessment.json

记录考试结构、paper、题型、能力要求和难度规则。

建议字段：

- `papers`
- `assessment_objectives`
- `question_styles`
- `difficulty_ladder`
- `marking_guidance`
- `lesson_use`
- `generation_constraints`

最小模板：

```json
{
  "schema_version": "1",
  "papers": [
    {
      "paper_id": "P1",
      "name": "Multiple Choice",
      "question_styles": ["mcq"],
      "coverage_notes": "Core syllabus sampling"
    }
  ],
  "assessment_objectives": [
    {
      "ao_id": "AO1",
      "name": "Knowledge and understanding",
      "classification_hints": ["recall", "define", "identify"]
    }
  ],
  "difficulty_ladder": [
    {"level": "foundation", "description": "single concept recall or direct application"},
    {"level": "standard", "description": "multi-step application within one topic"},
    {"level": "challenge", "description": "cross-topic reasoning or exam-style synthesis"}
  ],
  "difficulty_scale": "ordinal_3",
  "confidence_thresholds": {
    "route_minimum": 0.80,
    "classification_minimum": 0.75,
    "review_required_below": 0.75
  },
  "lesson_use": {
    "supported": false,
    "modes": [],
    "teacher_use_contract_ref": "",
    "student_use_contract_ref": "",
    "parent_use_contract_ref": "",
    "notes": "Set supported=true when at least one teacher/student/parent-facing mode is intentionally declared; do not mark the skill active for that mode until the corresponding Pilot 2 checks pass."
  },
  "generation_constraints": {
    "must_match_scope": true,
    "mark_out_of_scope_as_extension": true
  }
}
```

`lesson_use.supported` 是 teacher/student/parent-facing 能力的总开关，不是单独的教师模式开关。`modes` 必须列出实际启用的模式，例如 `plan_lesson`、`explain_mistake` 或 `summarize_progress`。若 `supported=false`，则不启用 teacher/student/parent-facing 输出；分类、校对和题库 coverage 仍可独立 active。若 `supported=true` 但 `modes=[]`，或 `supported=false` 但 `modes` 非空，validator 应判为 P1。

### 3.5 examples.md

保存 golden examples，用于检查 skill 是否真的能指导分类和校对。

分类、校对和 coverage 用途至少包含：

- 正确分类样例。
- 易错分类样例。
- out-of-scope 样例。
- 跨 topic 样例。

teacher/student/parent-facing 样例是条件必需项：

- 若 `assessment.json` 中 `lesson_use.supported=false` 且 `lesson_use.modes=[]`，不强制包含 teacher/student/parent-facing 样例。
- 若 skill 在 `lesson_use.modes` 中宣称支持 teacher-facing `generate_questions`、`plan_lesson`、`diagnose_student` 或 `map_coverage`，必须补齐对应教师场景样例。
- 若 skill 宣称支持 `explain_mistake`、`recommend_practice`、`build_revision_path` 或 `map_student_coverage`，必须补齐对应学生场景样例。
- 若 skill 宣称支持 `summarize_progress`、`explain_risk`、`plan_parent_support` 或 `prepare_teacher_questions`，必须补齐对应家长场景样例。
- 用户场景样例缺失时，validator 应按 `lesson_use.modes` 和声明模式判断为 P1 或 P2，不能一律阻断分类用途 active。

每个样例必须写清楚：

- 输入。
- 期望 route。
- 期望 topic；out-of-scope 样例可写 `topic_id=null` 和 `verdict=out_of_scope`。
- 判断依据。
- 常见错误。

最小模板：

```markdown
## Correct Classification

### Example C1

Input:
> A question prompt or summarized item.

Expected route:
- skill_id: `caie-igcse-chemistry-0620`
- topic_id: `C1.1`
- expected_confidence_min: `0.75`
- threshold_source: `assessment.confidence_thresholds.classification_minimum`

Evidence:
- The question tests particle arrangement during change of state.

Common wrong classification:
- `C2.1 Atomic structure`, because both use particle language.

## Out-of-Scope

### Example O1

Input:
> A question prompt or summarized item.

Expected result:
- verdict: `out_of_scope`
- action: `archive` or `extension`
- reason: The concept belongs to A-Level Chemistry, not this syllabus.
```

### 3.6 references/

可选。放较长的结构化参考资料、topic 说明或官方文档摘录索引。

原则：

- 不放官方 syllabus 全文。
- 不放大段版权文本。
- 放结构化提炼、页码索引、来源链接和必要短引用。

### 3.7 scripts/

可选。放确定性校验脚本、转换脚本或 registry 工具。

原则：

- 只有在需要稳定重复执行时才加入。
- 脚本必须有最小测试或可运行样例。
- 不把平台专用流程写死在脚本里。

## 4. 命名标准

命名必须稳定、短、可路由，并能区分体系、考局、层级、学科和 spec。

推荐：

```text
caie-igcse-chemistry-0620
caie-alevel-chemistry-9701
edexcel-ial-math-wma11
ap-calculus-ab
ib-dp-physics-hl
hkdse-chemistry
```

后期预留：

```text
sat-math
oxford-mat
cambridge-step-2
amc-12
aime
ukmt-senior-maths-challenge
```

禁止：

```text
chemistry-skill
math-syllabus
cambridge-chemistry
physics
exam-helper
```

禁止原因：这些名字不能稳定区分考局、层级、考试版本或 spec，后续会导致 router 串线。

topic_id 命名规则：

- `topic_id` 只需要在单个 skill 内唯一，不要求全局唯一。
- manifest 或跨 skill 引用必须同时保存 `skill_id + topic_id`。
- 推荐格式为稳定短码，如 `C1.1`、`U2.3`、`M1-04`；不强制所有考试使用同一格式。
- topic 被重命名时，尽量保留原 `topic_id`。
- topic 被拆分、合并或删除时，必须写入 migration log 的 `topic_id_changes`。
- 若需要兼容旧题库，可在 migration 中提供 `topic_id_alias_map`，但 alias 不能进入主 topic tree。

## 5. 创建步骤标准（执行步骤标准）

每个 assessment skill 必须按以下步骤创建。任何步骤缺失，都不能进入 `active`。

### Step 1: 确认 assessment identity

确认：

- assessment_type。
- system。
- board or owner。
- subject。
- level。
- assessment code。
- valid_from / valid_to。
- 当前目标用途：分类、校对、出题、备课、诊断中的哪些。

输出：`metadata.json` 的 identity 部分。

### Step 2: 记录 source provenance

若 primary source 是 PDF、扫描件、长 CED、C&A Guide 或复杂 specification，应先查找是否已有同一 `source_id + document_version + checksum` 的可复用 source evidence bundle。若没有，再运行 `assessment-source-pdf-extractor`，或提供等价的人工 source evidence bundle。没有 source evidence bundle 时，只能创建 `draft`，不能进入 `validated`。

记录官方来源：

- 官方文档名称。
- 官方 URL 或本地归档位置。
- 官方文件形态 `source_document_type`，例如 syllabus、CED、C&A Guide、exam specification。
- 版式/考局抽取画像 `source_layout_profile`，例如 Edexcel specification、AQA two-column specification、AP CED unit guide。
- 来源格式，如 PDF、HTML、DOCX、扫描件或混合来源。
- PDF 文件 checksum 或稳定归档引用；无法提供 checksum 时必须记录可复核原因。
- 适用年份。
- 文档版本。
- 提取日期。
- 提取人或 agent。
- PDF/文档抽取工具或人工阅读方式。
- extraction profile：按 syllabus、CED、C&A Guide、exam specification 等文件形态选择抽取对象。
- 抽取结果是否已与原始 PDF 或官方页面二次核对。
- 是否只做结构化提炼。
- 页码、章节或来源定位。
- 版权/引用状态。

要求：

- 缺 source provenance 不能 active。
- 不得把官方 PDF 全文塞进 skill 主体。
- 若来源不确定，只能保持 `draft`。
- 若 primary source 是 PDF，缺页码/章节定位不能进入 `reviewed`。
- 若 PDF 抽取未经过原始文件抽样复核，只能进入 `validated`，不能进入 `active`。
- 若 PDF 为扫描件或 OCR 低质量，必须记录抽取风险，并由 reviewer 决定是否打回。

### Step 3: 提取 topic tree

从官方 syllabus、CED、考试说明或竞赛范围中提取 topic tree。

要求：

- 每个 topic 有稳定 `topic_id`。
- 每个 topic 有 `scope` 和 `out_of_scope`。
- 易混 topic 必须写 `common_misclassifications`。
- 允许跨 topic 题，但必须定义 primary topic 和 secondary topic 规则。

输出：`topics.json`。

### Step 4: 提取 assessment model

提取考试结构：

- paper 或 section。
- 题型。
- assessment objectives。
- 难度梯度。
- 评分和表达风格。
- 出题限制。

输出：`assessment.json`。

### Step 5: 写 scope 与 out-of-scope

每个 topic 必须说明：

- 哪些内容属于本 topic。
- 哪些内容看起来相关但不属于本 topic。
- 哪些内容属于其他考试或更高层级。
- 哪些内容可以作为 extension，但不能进入正式题库。

### Step 6: 建 golden examples

建立最小黄金样例集：

- 至少 3 个正确分类样例。
- 至少 2 个易错分类样例。
- 至少 2 个 out-of-scope 样例。
- 至少 1 个跨 topic 样例。

如果该 skill 要支持 teacher/student/parent-facing 场景，还应按启用模式补充：

- 教师模式：至少 1 个出题样例、1 个备课样例、1 个错题诊断样例。
- 学生模式：至少 1 个学生错题解释或复习建议样例。
- 家长模式：至少 1 个家长进度摘要或风险解释样例。

输出：`examples.md`。

### Step 7: 运行 validate

validate 分两层：

- 结构校验：文件存在、JSON 合法、必填字段、重复 topic id、schema_version。
- 质量校验：来源、PDF/page traceability、scope、out-of-scope、examples、assessment model 是否完整。

v1 validator 是 `assessment-skill-builder` 必须交付的跨平台检查能力。它可以先是脚本，也可以先是清单式 agent 检查，但必须输出同一套 P0/P1/P2 结果。后续若实现 CLI，validator 输出应包含机器可读 JSON、人工摘要和退出码。

validator 问题分级：

```text
P0  阻断注册和 active：来源缺失、topic tree 缺失、JSON 无法解析、topic_id 重复、平台专用硬依赖、多个 assessment 混在一个 skill、registry 与 metadata 版本不一致。
P1  阻断 validated/reviewed：分类用途 examples 不足、lesson_use.supported=true 但 modes 为空、lesson_use.supported=false 但 modes 非空、已声明教师模式但缺 teacher use contract 或 teacher examples、已声明学生模式但缺 student use contract 或 student examples、已声明家长模式但缺 parent use contract 或 parent examples、out-of-scope 不清楚、assessment model 不完整、版本字段缺失、primary PDF 缺页码/章节定位、PDF 抽取未复核、缺人工复核包、source review verdict 为 fail、extraction confidence 为 low、accuracy verdict 存在 blocking risks。
P2  建议优化：命名说明不够清楚、classification hints 偏少、已启用教师模式的样例覆盖不足但不缺失、已启用 teacher use contract 输出格式不够细、已启用 student use contract 反馈格式不够细、已启用 parent use contract 风险表达不够清楚、references 索引不够细、PDF page-map 覆盖不够细、extraction confidence 为 medium 且 reviewer 已接受风险。
```

处理规则：

- 存在 P0：只能保持 `draft`，不能 register。
- 存在 P1：只能保持 `draft`，不能进入 `validated`。
- 只有 P2：可进入 reviewer 语义验收，但必须记录优化建议。

`validated` 的准入条件是 P0/P1 清零；P2 可以存在，但必须记录。结构校验通过后，最多只能进入 `validated`，不能直接 active。

### Step 8: reviewer 给 verdict

reviewer 必须做语义验收，不只看 lint 或 JSON 结构。

verdict 必须包含：

- source match。
- source traceability。
- topic tree match。
- assessment model match。
- examples coverage。
- out-of-scope clarity。
- production readiness。
- 结论：`通过`、`打回` 或 `V2 needed`。

reviewer 可以是人，也可以是独立 agent；但不能是同一轮创建该 skill 的执行者。agent reviewer 必须只读取 skill package 和原始来源，不读取创建者的隐藏判断。

verdict 存储为 `review-verdict.json`，放在 skill package 根目录或 registry 的 review 记录目录中。最小结构：

```json
{
  "schema_version": "1",
  "skill_id": "caie-igcse-chemistry-0620",
  "reviewer": "reviewer_or_agent_id",
  "reviewed_at": "2026-06-29",
  "dimensions": {
    "source_match": {"rating": "pass", "notes": ""},
    "source_traceability": {"rating": "pass", "notes": ""},
    "topic_tree": {"rating": "pass", "notes": ""},
    "assessment_model": {"rating": "pass", "notes": ""},
    "out_of_scope": {"rating": "pass", "notes": ""},
    "examples": {"rating": "pass", "notes": ""},
    "portability": {"rating": "pass", "notes": ""}
  },
  "conclusion": "通过",
  "p0": [],
  "p1": [],
  "p2": []
}
```

reviewer rubric：

| 维度 | Pass | Weak | Fail |
| --- | --- | --- | --- |
| source match | 来源、年份、版本明确 | 来源明确但定位不够细 | 来源缺失或疑似非官方 |
| source traceability | 关键结论可回到 PDF/官方页面页码或章节，accuracy verdict 为 high 且抽取已复核 | 有页码但 page-map 不够细、只抽样复核或 extraction confidence 为 medium | 无法从结构化结论回到原始来源，或 extraction confidence 为 low |
| topic tree | 可支撑分类和 coverage | 主干可用但子 topic 边界弱 | 无法稳定分类 |
| assessment model | paper/AO/题型/难度可用 | 有结构但约束不够 | 不能指导出题或校对 |
| out-of-scope | 易混边界清楚 | 有边界但例子不足 | 无法防止串线 |
| examples | 覆盖正确、易错、out-of-scope、跨 topic | 有样例但覆盖不全 | 无可复测样例 |
| portability | 不绑定特定平台 | 有轻微平台表述 | 写死 EduFlow/Feishu/ClaudeTeam |

portability 的硬性平台依赖由 validator 判 P0；reviewer rubric 只复核轻微平台表述、可读性和安装说明，不重复承担 P0 检查。

总 verdict 规则：

- 所有维度 Pass，且无 P0/P1：`通过`。
- 存在 Weak 但不影响生产使用：`通过`，附 P2 建议。
- 任一核心维度 Fail：`打回`。
- 结构可用但需大批修订：`V2 needed`。
- `打回` 映射为保持或回到 `draft`。
- `V2 needed` 映射为保持 `draft`，并在 registry review 记录或 verdict 中标记 `needs_v2=true`。

### Step 9: register 到 registry

只有 `validated` 或 `reviewed` 状态的 skill 可以注册。`draft` 是 package-local 工作状态，不能写入 canonical registry；若需要保留 draft 审核记录，应放在 review 记录目录、工作区清单或 builder 输出中，不参与 router。

registry 记录：

- id。
- path。
- metadata_ref。
- package_version。
- registered_at。
- registry_status。
- supersedes。
- migration_log。

### Step 10: 切换 active

只有通过 reviewer 语义验收的 skill 才能切换为 `active`。

active 前必须确认：

- 官方来源明确。
- topic tree 完整。
- assessment model 完整。
- examples 可用于复测。
- 没有平台专用依赖。
- 题库智能体能根据该 skill 输出稳定分类 verdict。

## 6. 质量门槛

以下情况必须打回，不能 active：

- 缺官方来源。
- 缺适用年份或版本。
- 缺 topic tree。
- topic id 重复或不稳定。
- 缺 scope。
- 缺 out-of-scope。
- 缺 golden examples。
- 只有结构校验通过，没有语义验收。
- skill 中写死 EduFlow worker、Feishu、ClaudeTeam 或 task 状态。
- skill 名称不能稳定区分考试体系和 spec。
- 把多个体系或多个 spec 强行塞进一个大 skill。

## 7. 状态流转

状态定义：

```text
draft       初稿，可不完整，不能用于生产；只存在于 package-local 或 builder 工作记录，不进入 canonical registry。
validated   结构校验通过，但未完成语义验收。
reviewed    reviewer 已给出语义 verdict。
active      默认可用版本，可被 router 和题库智能体使用。
suspended   已发布但发现风险，暂时禁止默认路由。
deprecated  被新版本替代，但保留历史题库追溯。
archived    不再使用，仅保留归档。
```

状态流转：

```text
draft -> validated -> reviewed -> active
active -> suspended -> reviewed -> active
active -> deprecated -> archived
```

禁止：

- `draft -> active`
- `validated -> active` 且无 reviewer verdict
- 覆盖旧 active 版本而不保留历史版本

状态使用规则：

- canonical registry 只记录 `validated` 及以上状态；`draft` 不能参与 router、list active 或默认候选选择。
- `suspended` 用于 active 后发现系统性错误或来源风险，但尚未被新版本替代。
- `deprecated` 用于被新版本替代的旧版本。
- `archived` 用于不再参与路由和历史迁移的归档版本。
- 从 `suspended` 恢复到 `active` 必须重新经过 reviewer verdict。

## 7.1 版本迁移与历史追溯

考纲或考试结构更新时，必须新建版本记录，而不是直接覆盖旧 active。

迁移记录建议包含：

```json
{
  "from_version": "2026.1",
  "to_version": "2028.1",
  "supersedes": "caie-igcse-chemistry-0620@2026.1",
  "breaking_changes": true,
  "topic_id_changes": [
    {
      "old_topic_id": "C1.1",
      "new_topic_id": "C1.1a",
      "change_type": "split",
      "notes": "Old topic split into particle model and changes of state"
    }
  ],
  "topic_id_alias_map": {
    "C1.1": ["C1.1a", "C1.1b"]
  },
  "affected_qbank_items": [],
  "rollback_reason": null,
  "restored_from_rollback": false,
  "migration_notes": ""
}
```

迁移标准：

- topic 被拆分、合并、删除或重命名时，必须记录 `topic_id_changes`。
- 老题库继续保留原版本引用，不能强行改写历史分类。
- 新题默认使用 active 版本。
- deprecated 版本只能用于历史题库回放、迁移审计和旧课件追溯。
- 若迁移影响题库 manifest，必须生成 affected items 清单后再切 active。

迁移记录存储位置：

- 推荐放在 registry 的 `migrations/` 目录。
- 文件名使用 `<skill_id>-<from_version>-to-<to_version>.json`。
- `affected_qbank_items` 只保存题库 item id、manifest row id 或外部引用，不内联题目全文。

rollback 规则：

- 若新 active 版本上线后发现错误，将新版本切为 `suspended` 或 `deprecated`，并记录 `rollback_reason`。
- 将上一可用版本恢复为 `registry_status=active`，并记录 `restored_from_rollback`。
- rollback 也必须写入 migration log，不能静默改 registry。
- rollback 后必须安排 reviewer 复查导致回退的问题。

## 8. 后期预留

### v1: syllabus 类

第一阶段只把 syllabus 类作为 production-enabled：

- A-Level
- IGCSE
- AP
- IB
- DSE

目标能力：

- 创建标准。
- registry。
- validate。
- register。
- list/show。
- 题库分类标准对齐。

### v2: router / classify / coverage

第二阶段接入智能能力：

- route：判断题目属于哪个 assessment skill。
- classify：在目标 skill 内分类到 topic/subtopic。
- coverage：按 topic tree 检查题库覆盖。
- low-confidence review：低置信度进入二审。

### v3: SAT / 牛剑笔试 / 竞赛 / 教师场景

第三阶段扩展：

- SAT、ACT 等 standardized test。
- MAT、TMUA、STEP、PAT、ESAT、TSA、LNAT 等 admissions test。
- AMC、AIME、UKMT、BMO、USABO、化学竞赛等 competition。
- 出题。
- 备课。
- 学生错题诊断。
- 考纲迁移和旧题库影响审计。

这些类型在 v1 可保留 schema，但不能误标为 production-ready。

## 9. 非 EduFlow 专用原则

本标准是通用 assessment skill 标准，不是 EduFlow 专用模块。

必须遵守：

- skill 不写死 EduFlow worker 名称。
- skill 不写死 Feishu 群、消息格式或机器人流程。
- skill 不写死 ClaudeTeam、task id、runtime 状态。
- skill 不要求某个特定 orchestrator 才能使用。
- 团队编排由外部系统负责，skill 本身只提供考试标准和执行契约。
- EduFlow 可以消费 registry 和 skill package，但不拥有它们。

允许：

- 在 EduFlow 中做 wrapper。
- 在 Codex 中安装到 `~/.codex/skills/`。
- 在 Claude 或其他 agent 中按项目说明引用。
- 在 Obsidian 知识库中作为路径资源读取。

## 9.1 安装矩阵

assessment skill package 必须保持跨平台可安装或可读取。

| 智能体/平台 | 推荐安装或引用方式 | 不允许写死的内容 |
| --- | --- | --- |
| Codex | 复制到 `~/.codex/skills/` 或项目 `.codex/skills/` | 本地绝对路径、单一 workspace 假设 |
| Claude | 作为 project skill / project instruction 引用 | ClaudeTeam task 状态、特定 pane |
| Gemini/Qwen/Kimi | 通过路径读取 `SKILL.md` 和结构化文件 | 平台私有命令 |
| EduFlow | 作为 wrapper 或 worker identity 的外部标准 | Feishu 群、worker 名、runtime 状态 |
| Obsidian | 作为知识库规范与索引入口 | 需要执行才能理解的隐藏逻辑 |
| Registry | 读取 canonical `registry.json`，再按 `metadata_ref` 加载 skill package | 平台私有路径、隐式 active 状态 |

跨平台必保留字段：

- `id`
- `assessment_type`
- `system`
- `board_or_owner`
- `subject`
- `level`
- `assessment_code`
- `valid_from`
- `valid_to`
- `package_version`
- `source_provenance`

## 9.2 反污染规则

反污染规则用于防止 AP、IB、A-Level、IGCSE、DSE、SAT、牛剑笔试、竞赛之间互相串线。

必须遵守：

- 单项 skill 的 `topics.json` 只能包含本 assessment 的正式 topic tree。
- 跨体系重叠内容只能写入 `overlap_links` 或 `prerequisite_topics`，不能混入主 topic tree。
- extension 内容必须标记为 `extension`，不能进入正式 coverage。
- 同一题若可被多个 assessment 接收，router 必须输出候选列表和置信度，不能静默选一个。
- 教师备课可引用 prerequisite，但题库分类必须落回当前 assessment 的 topic_id。
- 任何跨体系复用都必须保留原始 skill id 和目标 skill id，便于追溯。

## 10. 题库智能体接入标准

题库智能体使用 assessment skill 时必须遵守两段式流程：

```text
题目进入
  -> router 判断 assessment identity
  -> 加载目标 assessment skill
  -> 按 topics.json 分类 topic/subtopic
  -> 按 assessment.json 判断题型、难度、paper 或能力要求
  -> 写入题库 manifest
  -> coverage / duplicate / out-of-scope 检查
```

router / classifier 输出必须包含：

```json
{
  "primary_route": {
    "skill_id": "caie-igcse-chemistry-0620",
    "confidence": 0.86,
    "evidence": ["mentions IGCSE Chemistry 0620 topic language"]
  },
  "candidate_routes": [
    {
      "skill_id": "caie-alevel-chemistry-9701",
      "confidence": 0.42,
      "evidence": ["overlap with A-Level terminology"]
    }
  ],
  "topic_classification": {
    "topic_id": "C1.1",
    "topic_name": "States of matter",
    "confidence": 0.81,
    "evidence": ["tests particle arrangement during change of state"]
  },
  "flags": [],
  "reviewer_recommendation": "auto_accept"
}
```

低置信度规则：

- route confidence 低，进入二审。
- topic confidence 低，进入二审。
- 题目跨多个体系，进入二审。
- 题目 out-of-scope，标记 archive、extension 或 reject。

最小题库 manifest 引用 schema：

```json
{
  "item_id": "Q-C1.1-001",
  "skill_id": "caie-igcse-chemistry-0620",
  "package_version": "2026.1",
  "topic_id": "C1.1",
  "subtopic_id": null,
  "route_confidence": 0.86,
  "classification_confidence": 0.81,
  "evidence": ["short evidence only"],
  "flags": [],
  "reviewer_recommendation": "auto_accept",
  "review_status": "pending"
}
```

manifest 只保存 skill/topic 引用和简短 evidence，不内联完整题目或官方文本。

## 11. 教师使用预留

assessment skill 不只服务题库分类，也要服务老师。

后续教师模式应复用同一套 topic tree 和 assessment model：

- `generate_questions`：按 topic、题型、难度、paper 风格出题。
- `plan_lesson`：按 syllabus topic、prerequisite、misconception 备课。
- `diagnose_student`：按错题映射到 topic 和能力缺口。
- `map_coverage`：检查班级或学生已覆盖和未覆盖的 topic。

### 11.1 教师使用契约

如果 `lesson_use.modes` 声明任一教师模式，必须提供 teacher use contract。它可以写在 `assessment.json` 中，也可以放在 `references/teacher-use-contract.md`，并由 `assessment.json.lesson_use.teacher_use_contract_ref` 指向。

teacher use contract 必须说明：

- 支持哪些教师任务：备课、出题、改题、讲评、错题诊断、coverage mapping、revision planning。
- 必填输入：skill_id、topic_id 或 strand/objective、目标考试版本、paper/tier/level、学生水平、课时长度、目标产出类型。
- 可选输入：班级背景、已学 prerequisite、目标分数段、语言水平、学校进度、是否需要 extension。
- 输出格式：lesson plan、worksheet、quiz、mark scheme、diagnostic report、coverage table、revision checklist 等。
- 证据要求：所有教学输出必须引用当前 skill 的 topic_id / assessment objective / paper rule；不能只说“根据考纲”。
- 不确定处理：缺 exam board、spec、tier、paper、topic 或学生水平时，必须先给出澄清问题或输出低置信度草案，不能静默假设。
- 版权边界：不得复刻官方题目、官方 mark scheme 或大段官方 wording；可以生成原创题、结构化摘要、短 evidence 和页码引用。

教师使用 contract 最小模板：

```json
{
  "schema_version": "1",
  "supported_modes": ["plan_lesson", "generate_questions", "diagnose_student", "map_coverage"],
  "required_inputs": ["skill_id", "topic_id", "valid_from", "valid_to", "paper_or_tier", "learner_level", "output_type"],
  "optional_inputs": ["lesson_duration_minutes", "class_profile", "prior_knowledge", "target_grade_band", "language_support", "extension_allowed"],
  "output_contracts": {
    "plan_lesson": ["learning_objectives", "prerequisites", "core_scope", "activities", "assessment_check", "misconceptions", "homework"],
    "generate_questions": ["question", "topic_id", "paper_style", "difficulty", "mark_scheme", "examiner_notes", "originality_note"],
    "diagnose_student": ["observed_error", "topic_id", "likely_cause", "misconception", "remediation", "next_practice"],
    "map_coverage": ["covered_topics", "missing_topics", "weak_topics", "evidence", "recommended_sequence"]
  },
  "must_not": [
    "copy official question text",
    "invent unsupported assessment rules",
    "mix another assessment's topic tree into current coverage",
    "treat extension content as core syllabus"
  ]
}
```

### 11.2 老师视角的常见风险

从国际课程学科老师视角，以下情况必须显式处理：

- 老师可能用自然语言说“教 Waves”或“出一套 Paper 2 题”，但没有给考局、spec、tier、paper 或年份；skill 必须先路由或澄清。
- 老师关心的是“可教、可练、可诊断”，不只是 topic 命中；因此每个 teacher output 必须把 topic scope、paper demand、command words、difficulty 和 common misconceptions 连接起来。
- 有些官方文件包含 teaching guidance、support resources 或 overview text；这些可用于教学启发，但不能自动变成 assessable content。
- 对 AQA 这类 two-column specification，右栏 WS/MS/AT skill opportunities 不能当作 biology content topic，但出题和备课时必须保留为 skill links。
- 对 Edexcel 这类 bold/P reference 或 paper split，备课和出题必须说明内容是否适用于 Paper 1、Paper 2 或两者。
- 对 DSE English 这类 C&A Guide，分类粒度可能是 strand、skill、objective 或 text type，而不是传统 content topic；教师模式必须暴露这个粒度，不得假造 subtopic。
- 对 AP CED，unit/topic 之外还必须保留 practice/skill 维度；否则课堂活动和 exam-style question 会偏离 AP 评分需求。
- 对 IB DP subject guide，必须保留 SL/HL 或 AHL 边界、Structure/Reactivity 主轴、skills/tools/inquiry、NOS/linking questions、data booklet、IA 与 external assessment 的分工；不得把 programme intro、TOK/ATL 或 learner profile 当成必考 topic。

教师场景需要额外注意：

- 不为了教学方便改变 topic_id。
- 不把 extension 混入正式 syllabus。
- 出题时必须标记题型、难度和考纲依据。
- 备课时必须区分 prerequisite、core scope 和 extension。
- 原创题必须标记为 generated/original，不得暗示来自官方真题。
- 若输出 mark scheme，必须说明是 generated marking guidance，不得冒充官方 mark scheme。
- 课堂建议可以基于教学经验，但必须和 official scope 分开标注。

教师模式启用规则：

- v1 skill 默认不启用教师 mode；若也未启用 student/parent mode，才设置 `lesson_use.supported=false`。
- 只有当 skill 在 `lesson_use.modes` 中宣称支持 teacher-facing `generate_questions`、`plan_lesson`、`diagnose_student` 或 `map_coverage` 时，才必须补齐 teacher examples 并通过 Pilot 2。
- `plan_lesson` 至少需要 topic prerequisites、common_misconceptions、difficulty_ladder。
- `diagnose_student` 至少需要 topic_id、common_misconceptions、evidence 和 remediation notes。
- 教师模式失败不阻断题库分类用途 active，但必须从 `lesson_use.modes` 中移除对应教师模式；若没有任何 teacher/student/parent-facing mode 保留，才将 `lesson_use.supported=false`。
- 若 `lesson_use.modes` 声明任一教师模式，teacher use contract、teacher examples、Pilot 2 和 reviewer 语义验收全部必须通过。

## 11.3 学生使用契约

学生模式和教师模式共享同一套 topic tree、assessment model、difficulty ladder 和 source traceability，但不能直接照搬教师输出。学生需要的是可理解反馈、下一步练习、复习路径和考试边界提醒。

如果 skill 宣称支持 student-facing 诊断、复习或练习推荐，必须提供 student use contract。它可以写在 `assessment.json` 中，也可以放在 `references/student-use-contract.md`，并由 `assessment.json.lesson_use.student_use_contract_ref` 指向。

student use contract 必须说明：

- 支持哪些学生任务：错题解释、弱点诊断、下一步练习、复习计划、考前 checklist、topic coverage 自查、exam command word 理解。
- 必填输入：skill_id、topic_id 或题目分类结果、学生当前答案或错误表现、目标考试版本、paper/tier/level、目标分数段或当前水平。
- 可选输入：学习时间、已学内容、错题历史、语言偏好、是否允许 extension、是否需要基础补救。
- 输出格式：student-friendly explanation、mistake diagnosis、next practice set、revision path、exam technique note、confidence warning。
- 反馈要求：必须区分 `what went wrong`、`why it matters for this assessment`、`what to revise next`、`what to practise next`。
- 边界要求：不能承诺分数提升，不能替代老师批改，不能把低置信度诊断说成确定结论。
- 来源要求：学生可读反馈不必暴露完整 PDF source chain，但必须保留 topic_id、assessment objective 或 paper rule 依据。

student use contract 最小模板：

```json
{
  "schema_version": "1",
  "supported_modes": ["explain_mistake", "recommend_practice", "build_revision_path", "map_student_coverage"],
  "required_inputs": ["skill_id", "topic_id", "student_response_or_error", "paper_or_tier", "learner_level"],
  "optional_inputs": ["target_grade_band", "available_study_time", "prior_attempts", "language_support", "extension_allowed"],
  "output_contracts": {
    "explain_mistake": ["student_friendly_explanation", "misconception", "correct_concept", "exam_link", "next_step"],
    "recommend_practice": ["topic_id", "difficulty", "question_style", "practice_goal", "success_criteria"],
    "build_revision_path": ["prerequisites", "core_topics", "weak_topics", "sequence", "time_estimate"],
    "map_student_coverage": ["secure", "developing", "not_yet_seen", "out_of_scope", "evidence"]
  },
  "must_not": [
    "promise a grade increase",
    "diagnose certainty from weak evidence",
    "recommend out-of-scope content as required core",
    "copy official mark scheme wording"
  ]
}
```

### 11.4 学生视角的常见风险

从学生视角，以下情况必须显式处理：

- 学生可能只上传一道题或一句“我不会这个”，没有说明考局、paper、tier 或课程版本；skill 必须先路由或标记低置信度。
- 学生需要知道“这是不会概念、不会题型、不会 command word、计算错误、表达不清，还是超纲内容”，不能只返回 topic 名称。
- 学生容易把 extension、higher-tier、A-Level/AP/IB 重叠内容误认为当前 syllabus 必考；反馈必须明确 core、extension、out-of-scope。
- 学生练习推荐必须按 prerequisite -> core -> exam-style 递进，不能直接跳到高难题。
- 对语言类 assessment，例如 DSE English，学生反馈可能围绕 strand、skill、text type、communicative function，而不是传统知识点；skill 必须保留这个粒度。
- 对科学类 assessment，required practical、WS/MS/AT、AO、command words 会影响得分；学生诊断必须能指出是 content gap 还是 skill/AO gap。
- 学生答案可能包含个人信息、学校作业或未公开试卷；skill 输出应避免要求学生继续提供不必要个人信息，且不复制受版权保护题目全文。

学生场景启用规则：

- v1 skill 默认不强制支持 student mode。
- 如果声明支持 `explain_mistake`、`recommend_practice`、`build_revision_path` 或 `map_student_coverage`，必须提供 student use contract 和 student examples。
- `diagnose_student` 至少需要错误表现、topic_id、misconception、evidence、remediation、next_practice 和 confidence。
- 学生模式失败不阻断题库分类用途 active，但必须从 `lesson_use.modes` 中移除对应学生模式；若没有任何 teacher/student/parent-facing mode 保留，才将 `lesson_use.supported=false`。
- 若 `lesson_use.modes` 声明任一学生模式，student use contract、student examples、Pilot 2 和 reviewer 语义验收全部必须通过。

## 11.5 家长使用契约

家长模式和学生模式不同。家长通常需要理解学习风险、进度状态、下一步支持方式、何时需要老师介入，而不是直接查看 topic tree 或接受大量技术细节。

如果 skill 宣称支持 parent-facing summary、progress report 或 study support planning，必须提供 parent use contract。它可以写在 `assessment.json` 中，也可以放在 `references/parent-use-contract.md`，并由 `assessment.json.lesson_use.parent_use_contract_ref` 指向。

parent use contract 必须说明：

- 支持哪些家长任务：学习状态摘要、薄弱环节解释、复习计划沟通、风险提醒、家校沟通问题清单、阶段性 progress summary。
- 必填输入：skill_id、assessment identity、学生年级/level、当前 evidence 来源、目标考试版本、报告用途。
- 可选输入：目标分数段、近期作业/测验表现、可用学习时间、家长关注点、老师反馈。
- 输出格式：parent-friendly summary、risk level、evidence basis、next 2-4 week support plan、questions for teacher、what not to worry about。
- 证据要求：必须说明结论基于哪些 evidence，例如错题样本、coverage table、quiz result、teacher note 或 student self-report；没有足够 evidence 时必须标记 `insufficient_evidence`。
- 边界要求：不能承诺提分、录取、等级或考试结果；不能用一次错题判断长期能力；不能替代老师、学校或考试局官方建议。
- 隐私要求：不要求家长提供不必要个人信息、学校内部材料或未公开试卷全文；输出应避免暴露学生敏感信息。

parent use contract 最小模板：

```json
{
  "schema_version": "1",
  "supported_modes": ["summarize_progress", "explain_risk", "plan_parent_support", "prepare_teacher_questions"],
  "required_inputs": ["skill_id", "assessment_identity", "learner_level", "evidence_summary", "report_purpose"],
  "optional_inputs": ["target_grade_band", "recent_scores", "available_study_time", "teacher_feedback", "parent_concerns"],
  "output_contracts": {
    "summarize_progress": ["strengths", "weaknesses", "evidence", "confidence", "next_focus"],
    "explain_risk": ["risk_level", "risk_reason", "exam_link", "what_to_monitor", "teacher_check_needed"],
    "plan_parent_support": ["home_support_actions", "practice_routine", "review_timeline", "when_to_escalate"],
    "prepare_teacher_questions": ["questions", "evidence_to_share", "decision_needed"]
  },
  "must_not": [
    "promise grade improvement",
    "predict admission or exam outcome",
    "label the student from limited evidence",
    "replace teacher judgement"
  ]
}
```

### 11.6 家长视角的常见风险

从家长视角，以下情况必须显式处理：

- 家长可能只给一个分数或一句“孩子物理不行”，证据不足时必须输出 `insufficient_evidence`，不能强行诊断。
- 家长需要知道“现在最该关注什么”，输出应按 high/medium/low risk 或 immediate/next/later 排序。
- 家长可能把 extension、竞赛内容或高年级内容当成当前考试必需；必须明确 core、extension、out-of-scope。
- 家长可能要求预测分数、等级、录取或短期提分；skill 必须拒绝承诺，只能给 evidence-based risk 和学习建议。
- 家长需要和老师沟通，输出应提供可问老师的问题，而不是替老师下结论。
- 家长报告必须避免制造焦虑；要区分“需要立刻处理的问题”和“正常学习曲线中的问题”。

家长场景启用规则：

- v1 skill 默认不强制支持 parent mode。
- 如果声明支持 `summarize_progress`、`explain_risk`、`plan_parent_support` 或 `prepare_teacher_questions`，必须提供 parent use contract 和 parent examples。
- parent-facing 输出必须包含 evidence basis、confidence、next action 和 teacher_check_needed。
- 家长模式失败不阻断题库分类用途 active，但必须从 `lesson_use.modes` 中移除对应家长模式；若没有任何 teacher/student/parent-facing mode 保留，才将 `lesson_use.supported=false`。
- 若 `lesson_use.modes` 声明任一家长模式，parent use contract、parent examples、Pilot 2 和 reviewer 语义验收全部必须通过。

## 12. 首批试点建议

建议先选 2 到 3 个高价值、易验证的 skill：

1. `caie-igcse-chemistry-0620`
   - 已有 IGCSE 题库资产基础。
   - 适合验证 topic tree、out-of-scope、题库 coverage。

2. `ap-calculus-ab`
   - AP 是 greenfield，适合验证 College Board CED 类型。
   - 适合验证未来 AP 体系扩展。

3. `ib-dp-chemistry`
   - 适合验证 IB subject guide 类型、SL/HL 边界、IA/external assessment 和 skills/tools/inquiry 分层。
   - 适合压测 concept-based syllabus，不应把 learner profile、TOK/ATL 或 teaching background 抽成 topic。

4. `caie-alevel-chemistry-9701` 或 `edexcel-ial-math-wma11`
   - 适合验证 A-Level/IAL 的考局和 spec 边界。
   - 适合验证 topic drift 和跨体系误分类防护。

试点验收标准：

```text
Pilot 0  文档试点
  - 建立 skill package 文件夹。
  - 填写 metadata.json。
  - 至少提取 3 个一级 topic 和 6 个二级 topic。
  - 建立 examples.md 的最小黄金样例集。
  - reviewer 能按 rubric 给出 verdict。

Pilot 1  题库分类试点
  - 使用 20 道样题测试 route 和 topic classification。
  - 至少包含 3 道易错题、2 道 out-of-scope 题、2 道跨 topic 题。
  - 输出 confidence、evidence、flags。
  - 低置信度样题能进入二审规则。

Pilot 2  教师/学生/家长场景试点
  - 若声明支持 teacher mode，至少包含 1 份 topic 练习、1 份 60-90 分钟 lesson plan、5 道 topic-level 诊断。
  - 若声明支持 teacher mode，teacher output 必须引用当前 assessment skill 的 topic_id / assessment objective / paper rule。
  - 若声明支持 teacher mode，至少包含 1 个缺少考局/spec/tier/paper 的自然语言教师请求，并验证 skill 会澄清或标记低置信度。
  - 若声明支持 teacher mode，至少包含 1 个 generated/original question + generated marking guidance，并验证没有复制官方题目或 mark scheme。
  - 若声明支持 teacher mode，至少包含 1 个 prerequisite/core/extension 边界案例。
  - 对存在 paper split、tier、required practical、WS/MS/AT、command words 或 practice/skill 维度且声明支持对应 user-facing mode 的 assessment，必须抽样验证输出是否保留这些约束。
  - 若声明支持 student mode，至少包含 3 个学生错题解释、1 个弱点诊断、1 个复习路径和 1 个 out-of-scope/extension 边界反馈。
  - 若声明支持 student mode，student output 必须包含 misconception、next_practice、confidence 和 exam_link，且不得承诺分数提升。
  - 若声明支持 parent mode，至少包含 1 个 progress summary、1 个 risk explanation、1 个 parent support plan 和 1 个 teacher questions list。
  - 若声明支持 parent mode，parent output 必须包含 evidence basis、confidence、next action 和 teacher_check_needed。
  - 若声明支持 parent mode，至少包含 1 个证据不足的家长请求，并验证输出 `insufficient_evidence` 而不是强行诊断。
  - 若声明支持任一 user-facing mode，至少包含 1 个要求预测分数、等级、录取或短期提分的请求，并验证 skill 不做承诺。
```

首个 skill 通过标准：

- Pilot 0 必须通过。
- Pilot 1 至少达到可人工复核状态。
- Pilot 2 可保留为 `P2` 建议；如果 `lesson_use.supported=true` 或 `lesson_use.modes` 非空，则必须通过对应启用模式。

## 13. 完成定义

一个 assessment skill package 完成的最低标准：

- 文件结构完整。
- metadata 来源完整。
- primary source PDF/官方页面可追溯，关键结论有页码或章节定位。
- topic tree 可用于分类。
- assessment model 可用于题型和难度判断。
- examples 可用于复测。
- validate 通过。
- reviewer 给出通过 verdict。
- registry 注册成功。
- 状态为 `active`。

如果只满足部分标准，应保持 `draft` 或 `validated`，不得进入生产使用。

## 14. 下一步落地顺序

建议按以下顺序推进，避免一开始做成过重系统：

1. 把本文件固化为 Assessment Skill Factory 总标准。
2. 创建真正可安装的 `assessment-source-pdf-extractor` skill，并定义 PDF/source-index/page-map 最小模板。
3. 用 DSE C&A Guide、AP CED、IB DP subject guide、CAIE syllabus、Edexcel specification、AQA specification 各跑一次来源抽取试点。
4. 创建真正可安装的 `assessment-skill-builder` skill，让它消费 extractor 输出。
5. 用 builder 生成第一个试点 skill：优先 `caie-igcse-chemistry-0620` 或 `ap-calculus-ab`。
6. 建立最小 registry，只登记试点 skill。
7. 用 20 道样题跑 Pilot 1。
8. 根据 reviewer verdict 修订 builder 和 extractor 模板。
9. 再扩展到第二个体系，验证反污染规则。

原则：

- 先验证创建质量，再做 CLI。
- 先验证 PDF/source traceability，再相信结构化抽取。
- 先把 PDF 抽取做成可复用 skill 和可复用 source evidence bundle，再批量建设 assessment skill。
- 同一官方文件版本的 PDF 抽取只做一次；后续复用 bundle，除非版本、checksum、抽取错误或复核需求发生变化。
- 先验证单项 skill，再做 router。
- 先验证题库分类，再做出题和备课。
- 每次扩新体系，都要回头修订 builder，而不是只修订单项 skill。
