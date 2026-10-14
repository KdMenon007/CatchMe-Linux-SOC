# Process-Tree Analysis — Project 04

## Task 8 expected sequence
The reported Elastic view showed cron-related execution through a shell (`dash`) and a root-context `id` process. This is consistent with a scheduled command executing in root context, but the exact parent-child chain must be confirmed from the underlying event documents.

## Elastic review query
Use the actual field mappings in the selected data view. A starting KQL filter is:

```kql
host.name:"soc-linux" and event.category:process and
(process.name:"cron" or process.name:"dash" or process.name:"sh" or process.name:"id")
```

For focused review of the test script path:

```kql
host.name:"soc-linux" and event.category:process and
process.command_line:"*/opt/catchme-p04-lab/p04-cron-validation.sh*"
```

If wildcard matching behaves differently in the current field mapping, search the exact path using Discover's field-aware query builder.

## Record for each relevant event
- `@timestamp` and timezone
- `host.name`
- `user.name` and `user.id`
- `process.name` and `process.command_line`
- `process.parent.name` and `process.parent.command_line`, if populated
- `process.entity_id` / `process.parent.entity_id`, if populated
- document ID and index/data stream

## Interpretation limits
Do not infer a complete process tree from separate events unless process identifiers and timestamps support the relationship. Root identity in a host-side validation log corroborates the test, but does not prove an Elastic alert fired.
