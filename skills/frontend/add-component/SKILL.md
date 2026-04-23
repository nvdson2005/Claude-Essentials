---
name: add-component
description:
  Add a new shadcn component using the project's add-component script and
  PascalCase conventions
user-invocable: true
---

When adding a new shadcn component to this project, always use the npm script
instead of running shadcn directly:

```sh
npm run add-component <component-name> [layer]
```

- `component-name`: kebab-case shadcn name (e.g. `button`, `date-picker`)
- `layer`: `atoms` (default), `molecules`, or `organisms`

## Layer conventions

| Layer       | Purpose                                  | Examples                                     |
| ----------- | ---------------------------------------- | -------------------------------------------- |
| `atoms`     | Primitive UI elements, no business logic | `Button`, `Input`, `Text`, `Switch`          |
| `molecules` | Compositions of atoms                    | `Field`, `DatePicker`, `Toast`, `FileUpload` |
| `organisms` | Complex sections with logic              | `UserForm`, `Dialog`, `Carousel`             |

## What the script does

1. Runs `npx shadcn@latest add <component-name>` — shadcn always outputs to
   `src/components/atoms/`
2. Renames the file from kebab-case to PascalCase (e.g. `date-picker.tsx` →
   `DatePicker.tsx`)
3. Moves it to the target layer directory if a layer other than `atoms` was
   specified

## File naming conventions

- All component files use **PascalCase** (`DropdownMenu.tsx`, `Field.tsx`)
- Imports must match the exact casing — Linux CI is case-sensitive
- If you ever rename a file manually, use `git mv` so git tracks the rename
  correctly

## After running the script

1. Verify the file landed in the correct layer directory
2. Trim any shadcn boilerplate not needed
3. Update imports in consuming files to use
   `@/components/<layer>/<ComponentName>`

## Examples

```sh
npm run add-component badge
npm run add-component combobox molecules
npm run add-component carousel organisms
```
