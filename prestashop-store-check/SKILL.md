---
name: prestashop-store-check
description: Check if a PrestaShop store is ready to be updated. Use when the user wants to assess compatibility or available versions without starting an update.
---

## Requirements

Find out what is the path to the module `autoupgrade`:
* The commands are run with `[module_path]/bin/console`,
* Commands details are provided by adding `--help` at the end.

## Steps

- Run `update:check-new-version` and display available versions. Ask the user to select the canal that will be used during the next steps.
- Run `update:check-requirements` and summarize the results.
- If there are errors, explain each one and suggest fixes — but do not fix anything automatically.
- Conclude with a clear go / no-go recommendation.
