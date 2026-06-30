# Teacher / Student / Parent Contract Standard

> 当 skill 声明支持 teacher/student/parent-facing 模式时，必须提供对应 contract。v1 默认不启用任何用户模式。

## 默认状态

```json
{
  "lesson_use": {
    "supported": false,
    "modes": [],
    "teacher_use_contract_ref": "",
    "student_use_contract_ref": "",
    "parent_use_contract_ref": "",
    "notes": "No user-facing modes enabled in v1."
  }
}
```

## 启用规则

- 若 `lesson_use.supported=false`，则 `modes` 必须为空。
- 若 `lesson_use.supported=true`，则 `modes` 必须非空，且每个 mode 必须有对应 contract 和 examples。
- 若某一类 mode 启用，必须将 `supported` 设为 `true`。
- 若只有某一类未启用，只能记录该类 mode 未启用说明，不得把其他已启用 mode 关闭。

## Teacher Use Contract

支持任务：备课、出题、改题、讲评、错题诊断、coverage mapping、revision planning。

必填输入：skill_id、topic_id 或 strand/objective、目标考试版本、paper/tier/level、学生水平、课时长度、目标产出类型。

输出格式：lesson plan、worksheet、quiz、mark scheme、diagnostic report、coverage table、revision checklist 等。

证据要求：所有教学输出必须引用当前 skill 的 topic_id / assessment objective / paper rule。

边界：不得复刻官方题目或 mark scheme；原创题必须标记 generated/original。

## Student Use Contract

支持任务：错题解释、弱点诊断、下一步练习、复习计划、考前 checklist、coverage 自查、command word 理解。

必填输入：skill_id、topic_id 或分类结果、学生当前答案或错误表现、目标考试版本、paper/tier/level、目标分数段或当前水平。

输出格式：student-friendly explanation、mistake diagnosis、next practice set、revision path、exam technique note、confidence warning。

反馈要求：必须区分 `what went wrong`、`why it matters for this assessment`、`what to revise next`、`what to practise next`。

边界：不能承诺分数提升，不能替代老师批改，不能把低置信度诊断说成确定结论。

## Parent Use Contract

支持任务：学习状态摘要、薄弱环节解释、复习计划沟通、风险提醒、家校沟通问题清单、阶段性 progress summary。

必填输入：skill_id、assessment identity、学生年级/level、当前 evidence 来源、目标考试版本、报告用途。

输出格式：parent-friendly summary、risk level、evidence basis、next 2-4 week support plan、questions for teacher、what not to worry about。

证据要求：必须说明结论基于哪些 evidence；没有足够 evidence 时标记 `insufficient_evidence`。

边界：不能承诺提分、录取、等级或考试结果；不能用一次错题判断长期能力；不能替代老师或学校。

## 平台无关性

contract 文件中不得出现：

- EduFlow worker 名称。
- Feishu 群、消息格式或机器人流程。
- ClaudeTeam、task id、runtime 状态。
- 特定 IDE 或 CLI 的私有命令。
