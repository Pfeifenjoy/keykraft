# Script conventions

Scripts follow a hierarchical naming convention using colons as separators.

## Structure

```
<action>:<category>:<tool>
```

- **action** — what the script does (`test`, `format`)
- **category** — the area being targeted (`lint`, `unit`, etc.)
- **tool** — the specific tool being invoked (`eslint`, `prettier`, etc.)

Parent scripts always call their children, so running `test` runs everything under `test:*`.

## Adding new scripts

- Add the leaf script first (`test:unit:<tool>`), invoking the tool via `npx`
- Wire it into the parent (`test:unit` → `... && npm run test:unit:<tool>`)
- Wire the parent into its parent if not already there (`test` → `... && npm run test:unit`)
