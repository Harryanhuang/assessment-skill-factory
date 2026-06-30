# Extraction Profiles

> 本文件定义 6 种 source_document_type / source_layout_profile 的抽取规则。每个 profile 说明 must_extract、must_not_extract_as_topic、high_impact_sections、visual_review_required_pages、common_failure_modes 和 reviewer_questions。

## Asset 分配原则

- `topics.json`：只放本 assessment 的正式 assessable content / topic tree。
- `assessment.json`：paper / tier / AO / question style / difficulty / command words / required practical / exam weighting / confidence thresholds。
- `references/`：source-index、page-map、 longer context、skill opportunities、teaching guidance、programme background、command term lists、data booklet reference、IA criteria。

## 何时降级 extraction_confidence

- 扫描件、OCR 低质量、双栏错位、表格跨页、页码错位。
- 缺 checksum、版本不明、权限限制、官方来源待确认。
- 抽取冲突未解决、高影响页未视觉复核。
- 以上任一情况出现时，`extraction_confidence` 不得写 `high`；严重时应写 `low` 并阻断 validated。

---

## 1. standard_syllabus

- 典型：CAIE IGCSE / A-Level syllabus（如 Accounting 0452、Chemistry 0620）。
- 版式特征：目录 + syllabus aim + assessment overview + subject content 表格 + assessment objectives + details of assessment + grade descriptions。

### must_extract

- Syllabus identity（code、title、version、valid years）。
- Subject content 表格中的 topic / subtopic 层级与描述。
- Assessment objectives（AO）及其权重。
- Paper / component 结构、时长、分值、题型、选项规则。
- Grade descriptions / series rules（如 March/November 可用性）。
- Syllabus 变更说明（如有）。

### must_not_extract_as_topic

- 前言、简介、认可说明、行政说明。
- 版权页、contact details、how to use this syllabus。
- 教师支持材料说明、endorsed resources 列表。
- Marketing language about the qualification。

### high_impact_sections

1. Syllabus overview / content overview。
2. Subject content table（全部层级）。
3. Assessment overview / details of assessment。
4. Assessment objectives and weightings。
5. Paper/component rules and options。
6. Grade descriptions / series availability。
7. Issue/change summary（如有）。

### visual_review_required_pages

- 目录页（确认章节映射）。
- Subject content 起始页与结束页（确认覆盖完整）。
- Assessment overview / AO 权重页。
- Paper details 表格页。
- 变更日志页（如有）。

### common_failure_modes

- 把 syllabus aim / introduction 误当成 topic。
- 漏掉 AO 权重或 paper option 规则。
- 把同一 topic 在不同 paper 的覆盖范围忽略，导致 topics.json 缺 paper scope 提示。
- 忽略 syllabus 版本或 issue 说明，用错年份。

### reviewer_questions

- subject content 表格是否完整映射到 page-map？
- AO 权重和 paper 结构是否来自官方页而非记忆？
- 是否有 admin/marketing 内容被误标为 assessable？
- 版本、code、valid years 是否与 PDF 标题页一致？

---

## 2. ap_ced_unit_guide

- 典型：AP Course and Exam Description（如 AP Physics 1 2024 CED）。
- 版式特征：Course and Exam Description → Course Framework → Science Practices → Course at a Glance → Unit Guides → Exam Information → Sample Exam Questions。

### must_extract

- Course framework components（big ideas、enduring understandings、learning objectives、science practices）。
- Unit guides（unit / topic / suggested skill / essential knowledge）。
- Science practices / skills 及其与 unit 的关联。
- Exam weighting / exam overview（section、time、question type、calculator policy）。
- Sample question model（题型示例，不复制题目全文）。

### must_not_extract_as_topic

- Programme background / AP programme description。
- Marketing about AP。
- General test-taking tips 脱离具体 unit 的内容。
- 教师教学建议（instructional approaches）除非进入 assessment.json 的 generation_constraints。

### high_impact_sections

1. Course framework components。
2. Science practices / skills。
3. Course at a glance。
4. Unit guides（unit / topic / essential knowledge）。
5. Exam weighting / exam overview。
6. Sample question model。
7. Calculator / equation sheet policy。

### visual_review_required_pages

- Course at a glance 表格页。
- 每个 unit guide 的首页（unit overview + essential knowledge 列表）。
- Science practices 定义页。
- Exam weighting / exam overview 页。
- Sample exam questions 首页（确认题型分布）。

### common_failure_modes

- 只抽 unit/topic 名称，漏掉 science practices / skills 维度。
- 把 instructional approaches 误当成 assessable content。
- 混淆 essential knowledge 与 learning objective 的层级。
- 忽略 exam weighting 导致 assessment.json 缺 paper 分布。

### reviewer_questions

- 每个 unit/topic 是否都关联了 science practice？
- essential knowledge 是否完整映射到 unit？
- sample question 的题型是否被正确记录为 question_styles？
- 是否把教师教学建议当成了学生必考内容？

---

## 3. dse_curriculum_assessment_guide

- 典型：DSE English Language Curriculum and Assessment Guide。
- 版式特征：Curriculum Framework → Strands / Learning Objectives → Assessment Framework → Public Exam → SBA → Appendices（skill lists / exemplars）。

### must_extract

- Curriculum framework（aims、strands、learning objectives）。
- Assessment framework（paper/component、weighting、format、criteria）。
- Command terms / assessment language。
- Appendix skill lists / text type lists / performance descriptors。
- 变更说明（如有）。

### must_not_extract_as_topic

- Programme background / rationale。
- 教师专业发展建议。
- 学校实施指引中的行政内容。
- 样卷全文或学生作品全文（只记录题型/标准摘要）。

### high_impact_sections

1. Curriculum framework / aims / strands。
2. Learning objectives（按 strand / module）。
3. Assessment framework / public exam structure。
4. School-based assessment（SBA）要求（如适用）。
5. Command terms / performance descriptors。
6. Appendices（skill lists / text types / standards）。

### visual_review_required_pages

- Curriculum framework 图示页。
- Strands / learning objectives 表格页。
- Assessment framework / public exam 表格页。
- Command terms / performance descriptors 页。
- Appendix skill/text type 列表页。

### common_failure_modes

- 强行把 strand/skill/objective 重组成传统 content topic tree。
- 把 text type / performance descriptor 误当成普通知识点。
- 忽略 SBA 与 public exam 的边界。
- 把教师指引中的教学活动当成 assessable task。

### reviewer_questions

- 本 guide 的分类粒度是 strand / skill / objective / text type 中的哪一种？
- 是否有证据支持把某个 learning objective 当成独立 topic？
- SBA 要求是否被正确放入 assessment.json 而非 topics.json？
- 是否保留了 command terms / performance descriptors 的页码映射？

---

## 4. ib_dp_subject_guide

- 典型：IB DP Subject Guide（如 Chemistry First Assessment 2025）。
- 版式特征：Purpose / Syllabus outline / Syllabus format / Syllabus content（Structure / Reactivity / etc.）/ Skills in the study of chemistry / Assessment outline SL/HL / External assessment / Internal assessment / Command terms / Data booklet。

### must_extract

- Syllabus outline / roadmap / format。
- Syllabus content（Structure / Reactivity 等 strands 及其 subtopics、understandings、guidance、SL/HL 或 AHL 边界）。
- Skills in the study of chemistry（tools / techniques / inquiry skills）。
- Assessment objectives。
- Assessment outline SL / HL（paper structure、weighting、duration）。
- External assessment details / Internal assessment criteria。
- Glossary of command terms。
- Data booklet reference。

### must_not_extract_as_topic

- Learner profile / programme intro / TOK / ATL。
- Teaching background / teacher support material 说明。
- Programme model / aims 脱离学科的内容。
- 样卷全文或 markscheme 全文。

### high_impact_sections

1. Syllabus outline / roadmap / format。
2. Syllabus content（all strands / subtopics / understandings / guidance）。
3. Skills in the study of chemistry。
4. Assessment objectives。
5. Assessment outline SL / HL。
6. External assessment details。
7. Internal assessment details and criteria。
8. Glossary of command terms。
9. Data booklet reference。

### visual_review_required_pages

- Syllabus outline / roadmap 页。
- 每个 strand 的首页与末页（确认 SL/HL / AHL 边界）。
- Skills in the study of chemistry 页。
- Assessment outline SL/HL 表格页。
- External assessment / Internal assessment 详情页。
- Command terms / data booklet reference 页。

### common_failure_modes

- 只抽 strand 名称，漏掉 understandings / guidance / SL-HL 边界。
- 把 learner profile / TOK / ATL 当成 assessable content。
- 把 skills/tools/inquiry 直接混入普通 content topic（应进 assessment.json 或 references）。
- 忽略 IA criteria 与 external assessment 的分工。

### reviewer_questions

- 每个 subtopic 的 SL/HL / AHL 边界是否在 page-map 中清晰？
- skills/tools/inquiry 是否被正确分配到 assessment.json / references？
- 是否有 TOK/ATL/learner profile 被误标为 topic？
- 官方来源是否为 Programme Resource Centre / IB Store / 官方导出？checksum 和 retrieved_at 是否记录？

---

## 5. edexcel_specification

- 典型：Edexcel International GCSE specification（如 Physics 4PH1）。
- 版式特征：Issue/change summary → Qualification at a glance → Content → Assessment information → Appendices（command words / formulae / equation sheet）。

### must_extract

- Issue / change summary / version。
- Qualification at a glance（paper code、availability、options）。
- Physics content（按 topic / subtopic，含 bold/P reference 规则）。
- Assessment information（paper structure、duration、marks、weighting）。
- Assessment objectives and weightings。
- Relationship of AO and papers。
- Command word taxonomy。
- Bold / P reference or paper scope rules。

### must_not_extract_as_topic

- Change summary 中的版本历史。
- Qualification marketing / administration。
- 教师支持 / endorsed resources 列表。
- Appendices 中的非 content 部分（如 equation sheet 可复制到 references 但不作为 topic）。

### high_impact_sections

1. Issue / change summary。
2. Qualification at a glance。
3. Content section（全部 topics）。
4. Assessment information。
5. Assessment objectives and weightings。
6. Relationship of AO and papers。
7. Command word taxonomy。
8. Bold / P reference / paper scope rules。

### visual_review_required_pages

- Qualification at a glance 页。
- Content 首页与末页（确认 topic 覆盖）。
- Assessment information 表格页。
- AO weightings / relationship of AO and papers 页。
- Command word taxonomy 页。
- Bold / P reference 示例页。

### common_failure_modes

- 把 change summary 当成 topic。
- 忽略 bold/P reference 导致 paper scope 错误。
- 把 command words 当成 content topic（应进 assessment.json）。
- 忽略 paper availability / options / tier 规则。

### reviewer_questions

- 每个 topic 是否有 bold/P reference 说明？是否映射到 assessment.json？
- Command words 是否被正确放入 assessment.json 而非 topics.json？
- Paper code / availability / tier 是否完整记录？
- Change summary 是否被排除在 topic tree 外？

---

## 6. aqa_two_column_specification

- 典型：AQA GCSE specification（如 Biology 8461）。
- 版式特征：Specification at a glance → Working scientifically → Subject content（two-column：左 assessable content，右 WS/MS/AT skill opportunities）→ Required practical activities → Scheme of assessment → AO / weightings → Mathematical requirements。

### must_extract

- Specification at a glance（paper structure、tiers、weighting）。
- Working scientifically（WS）overview / skill opportunities（作为 skill links，不混成 content topic）。
- Left-column assessable content（topic / subtopic / required practical links）。
- Required practical activities（作为 assessment/practical constraint 或 skill link）。
- Scheme of assessment（paper / tier / duration / marks）。
- Assessment objectives and weightings。
- Mathematical requirements。

### must_not_extract_as_topic

- 右栏 WS/MS/AT skill opportunities（应进 assessment.json 或 references，不是 biology content topic）。
- Teaching guidance / overview text。
- Required practical 的 full instructions（只记录其作为约束的存在和 topic link）。
- 行政说明、版权、认可资源。

### high_impact_sections

1. Specification at a glance。
2. Working scientifically。
3. Subject content（左栏）。
4. Right-column WS/MS/AT opportunities。
5. Required practical activities。
6. Scheme of assessment。
7. Assessment objectives / weightings。
8. Mathematical requirements。

### visual_review_required_pages

- Specification at a glance 页。
- Working scientifically 首页。
- 每个 topic 的左栏/右栏首页（确认两栏分离）。
- Required practical activities 页。
- Scheme of assessment / AO weightings 页。
- Mathematical requirements 页。

### common_failure_modes

- 把右栏 WS/MS/AT opportunities 当成 content topic。
- 把 teaching guidance / overview text 当成 assessable content。
- 忽略 required practical 与 topic 的链接。
- 忽略 foundation / higher tier 的 scope 差异。

### reviewer_questions

- 左栏 content 是否完整映射到 topics.json？
- 右栏 WS/MS/AT 是否被正确放入 assessment.json / references？
- Required practical 是否作为 assessment constraint 记录？
- Foundation / higher tier 的 scope 差异是否在 assessment.json 中体现？

---

## 通用 high-impact sections

所有 profile 都必须重点复核：

1. Assessment overview / subject content table。
2. Paper rules / tier rules。
3. Bold/P reference / paper split / availability。
4. Required practical / command words。
5. AO/skills / grade descriptions。
6. Out-of-scope / forbidden combinations / issue/change summary。
