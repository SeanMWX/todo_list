# Storage Model

## Default Location

- Default database path: `~/.work_report_summary/todo_list.db`
- Override for tests or custom setups with `TODO_LIST_DB_PATH`

## Schema

The runtime stores tasks in a single `tasks` table with these fields:

- `id`: integer primary key
- `title`: short task title
- `details`: optional longer description
- `task_date`: the day the todo belongs to
- `planned_amount`: numeric target amount
- `done_amount`: numeric completed amount
- `unit`: unit label such as `items`, `steps`, or `hours`
- `status`: one of `pending`, `in_progress`, `completed`, `archived`
- `is_done`: explicit completion flag stored as `0` or `1`
- `progress_note`: latest progress note
- `created_at`, `updated_at`: UTC timestamps
- `completed_at`: UTC timestamp for the first completion event
- `archived_at`: UTC timestamp for archiving

## State Rules

- Infer `pending` when `done_amount <= 0`.
- Infer `in_progress` when `0 < done_amount < planned_amount`.
- Infer done when `done_amount >= planned_amount`.
- Archive completed tasks immediately instead of leaving them in active views.
- Reject progress updates after archive so archived tasks stay historical.
- Reject attempts to archive incomplete tasks.
- Permanent deletion removes the row entirely from SQLite and from all future list and summary results.

## Test Isolation

- Point `TODO_LIST_DB_PATH` at a temporary file during local tests.
- Do not write tests against the user's real `~/.work_report_summary` directory.
