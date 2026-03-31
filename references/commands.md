# Command Reference

Use `python {baseDir}/scripts/todo_list.py --json ...` for deterministic output.

## Commands

### Add

Create a new task with planned work and optional details.

```bash
python {baseDir}/scripts/todo_list.py --json add \
  --title "Write sprint summary" \
  --planned-amount 2 \
  --unit sections \
  --details "Risks plus next steps"
```

### List

List tasks by lifecycle state.

```bash
python {baseDir}/scripts/todo_list.py --json list --status active
python {baseDir}/scripts/todo_list.py --json list --status completed
python {baseDir}/scripts/todo_list.py --json list --status archived
python {baseDir}/scripts/todo_list.py --json list --status all
```

### Progress

Update the completed amount with either an absolute value or an increment.

```bash
python {baseDir}/scripts/todo_list.py --json progress --id 1 --done-amount 2
python {baseDir}/scripts/todo_list.py --json progress --id 1 --increment 1 --note "Shipped the draft"
python {baseDir}/scripts/todo_list.py --json progress --id 1 --planned-amount 5
```

Rules:

- Reject updates to archived tasks.
- Auto-mark tasks as `completed` when `done_amount >= planned_amount`.
- Move tasks back to `pending` or `in_progress` if progress is corrected downward before archiving.

### Complete

Force a task into the completed state and raise `done_amount` to at least `planned_amount`.

```bash
python {baseDir}/scripts/todo_list.py --json complete --id 1
```

### Archive

Move a task into historical storage.

```bash
python {baseDir}/scripts/todo_list.py --json archive --id 1
```

### Summary

Return count totals plus aggregate planned and completed amounts.

```bash
python {baseDir}/scripts/todo_list.py --json summary
python {baseDir}/scripts/todo_list.py --json summary --include-archived
```

## Example Flow

```bash
python {baseDir}/scripts/todo_list.py --json add --title "Prepare demo" --planned-amount 3 --unit steps
python {baseDir}/scripts/todo_list.py --json progress --id 1 --increment 2 --note "Slides and notes ready"
python {baseDir}/scripts/todo_list.py --json complete --id 1
python {baseDir}/scripts/todo_list.py --json archive --id 1
```
