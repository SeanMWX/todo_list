# Natural-Language Reference

Use this file when the user expresses todo actions in conversational Chinese or mixed Chinese-English phrasing.

## Intent Mapping

### Add Task

User phrasing:

- `帮我记一个 todo：写周报`
- `帮我记一下今天要做什么`
- `帮我记一下明天要做什么`
- `新增任务，明天做发版检查`
- `记录一下我要做登录页联调`

Map to:

- `add --title ...`
- Add `--task-date today` or `--task-date tomorrow` when the user anchors the task to a day
- Add `--planned-amount` and `--unit` when the user gives an amount such as `3 步`, `2 个模块`, `4 小时`
- Add `--details` when the user gives extra context

### Update Progress

User phrasing:

- `这个任务做了 2 步`
- `周报现在完成 1/3`
- `把 demo 任务进度改成 80%`
- `我今天又推进了一点`

Map to:

- Prefer `progress --increment ...` when the user describes newly completed work
- Prefer `progress --done-amount ...` when the user gives the new total completed amount
- Add `--note` when the user includes a short status note

If the user only says `推进了一点` without a measurable amount, ask for the missing amount unless there is already a clear unit convention in context.

### Complete Task

User phrasing:

- `做完了`
- `这个已经完成`
- `把任务 2 标记完成`

Map to:

- `complete --id ...`

### List Current Work

User phrasing:

- `看看还有什么没做`
- `今天还剩什么没做`
- `今天的 TODO 是什么`
- `明天的 TODO 是什么`
- `明天安排了哪些任务`
- `列出当前任务`
- `我现在有哪些 active todo`

Map to:

- `list --status active`
- Add `--task-date ...` when the user asks about a specific day

### Show Completed Or Archived Work

User phrasing:

- `看下已经完成的任务`
- `今天哪些已经完成了`
- `今天做完了什么`
- `把归档过的列出来`
- `我历史任务有哪些`

Map to:

- `list --status archived`
- `list --status all`

Because completion auto-archives, day-specific “done” questions usually map to `list --status archived --task-date ...`.

### Summary

User phrasing:

- `汇总一下现在做了多少`
- `今天完成了几个任务`
- `今天还有几个没完成`
- `看整体进度`
- `给我一个完成情况统计`

Map to:

- `summary`
- Add `--task-date ...` when the user asks about a specific day
- `summary --include-archived` only when the user explicitly wants historical totals

For day-specific routing, use these defaults:

- `今天还剩多少没做` -> `summary --task-date today`
- `今天完成了多少` -> `summary --task-date today --include-archived`
- `明天安排了多少任务` -> `summary --task-date tomorrow`

### Archive

User phrasing:

- `把这个归档`
- `归档第 4 个任务`
- `把做完的移到历史`
- `把“周报”这个任务归档`
- `把已完成任务都归档`

Map to:

- `archive --id ...`
- `archive --title ...`
- `archive --all-completed`

Archive is mainly for explicit history management or legacy completed rows. Normal newly completed tasks auto-archive already.

### Delete

User phrasing:

- `把这个任务删掉`
- `删除第 3 个任务`
- `这个 todo 不要了，永久删除`

Map to:

- `delete --id ... --confirm`
- `delete --title ... --confirm`

Delete is permanent. Prefer `archive` when the user says `归档`, `移到历史`, or otherwise implies retention.
Before deleting, ask for one more explicit confirmation.

## Resolution Rules

- Prefer exact task ids when the user provides them.
- Otherwise resolve by exact title match first.
- If multiple active tasks match the same phrase, ask a narrow follow-up instead of guessing.
- Add `--task-date` whenever the user anchors the request to today, tomorrow, yesterday, or an explicit date.
- Because completion auto-archives, do not expect normal active lists to contain finished tasks.
