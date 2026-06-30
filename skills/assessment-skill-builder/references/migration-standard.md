# Migration Standard

> 版本迁移、rollback、反污染规则。与主标准第 7.1 节一致。

## 何时新建版本

考纲或考试结构更新时，必须新建版本记录，而不是直接覆盖旧 active。

## 迁移记录建议包含

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

## 迁移标准

- topic 被拆分、合并、删除或重命名时，必须记录 `topic_id_changes`。
- 老题库继续保留原版本引用，不能强行改写历史分类。
- 新题默认使用 active 版本。
- deprecated 版本只能用于历史题库回放、迁移审计和旧课件追溯。
- 若迁移影响题库 manifest，必须生成 affected items 清单后再切 active。

## 迁移记录存储位置

- 推荐放在 registry 的 `migrations/` 目录。
- 文件名：`<skill_id>-<from_version>-to-<to_version>.json`。
- `affected_qbank_items` 只保存 item id / manifest row id / 外部引用，不内联题目全文。

## Rollback 规则

- 若新 active 版本上线后发现错误，将新版本切为 `suspended` 或 `deprecated`，并记录 `rollback_reason`。
- 将上一可用版本恢复为 `registry_status=active`，并记录 `restored_from_rollback`。
- rollback 必须写入 migration log，不能静默改 registry。
- rollback 后必须安排 reviewer 复查导致回退的问题。

## 反污染规则

- 单项 skill 的 `topics.json` 只能包含本 assessment 的正式 topic tree。
- 跨体系重叠内容只能写入 `overlap_links` 或 `prerequisite_topics`。
- extension 内容必须标记为 `extension`，不能进入正式 coverage。
- 同一题若可被多个 assessment 接收，router 必须输出候选列表和置信度。
- 任何跨体系复用都必须保留原始 skill id 和目标 skill id。
