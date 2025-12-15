<!-- docs/README.md -->
# Templateer

Render templates in GitHub Actions using environment variables.

- **Type:** GitHub Composite Action
- **Renderer:** `envsubst`
- **Inputs:** `template`, `result`, `debug`
- **Outputs:** `path`, `body`

---

## What it is

**Templateer** is a GitHub Composite Action that renders template files using `envsubst`, based on environment variables available in your workflow step/job. It is useful for generating Markdown/HTML/text files and reusing the rendered content in subsequent steps (PR comments, reports, artifacts, etc.).

---

## How it works

1. Exports environment variables into `$GITHUB_ENV` (excluding variables starting with `GITHUB_`)
2. Runs `envsubst` to replace placeholders (e.g. `$VAR`) in the template
3. Exposes:
   - `path`: the rendered file path
   - `body`: the rendered content (multiline)

---

## Inputs

| Input | Required | Default | Description |
|---|---:|---:|---|
| `template` | yes | - | Template file path |
| `result` | yes | - | Rendered output file path |
| `debug` | no | `false` | If `true`, prints exported variables to logs |

---

## Outputs

| Output | Description |
|---|---|
| `path` | Rendered file path |
| `body` | Rendered content (multiline) |

---

## Quickstart

### 1) Create a template

```md
# .github/templates/report.md
Hello, $NAME.

File: $FILE_NAME
Repository: $GITHUB_REPOSITORY
```

### 2) Render it in a workflow

```yml
# .github/workflows/example.yml
name: templateer-example

on:
  workflow_dispatch:

jobs:
  render:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Render template
        id: tpl
        uses: Malnati/templateer@v1.0.0
        env:
          NAME: Ricardo
          FILE_NAME: .reports/20251215-0100_hardcode.json
        with:
          template: .github/templates/report.md
          result: /tmp/report.rendered.md

      - name: Print path
        shell: bash
        run: echo "${{ steps.tpl.outputs.path }}"

      - name: Print content
        shell: bash
        run: |
          echo "-----"
          echo "${{ steps.tpl.outputs.body }}"
          echo "-----"

```

Template rules
	•	Placeholders follow envsubst syntax: $VAR
	•	Variables can come from:
	•	env: in the same step
	•	env: at the job level
	•	previous steps via $GITHUB_ENV

⸻

Debug mode

Enable debug only when needed:

- name: Render template
  uses: Malnati/templateer@v1.0.0
  with:
    template: .github/templates/report.md
    result: /tmp/report.rendered.md
    debug: "true"


⸻

Security notes
	•	Avoid debug: "true" if your environment may contain secrets.
	•	Prefer passing only the variables you need via env: on the step, instead of exporting a large environment.

⸻

Lim#### Limitations	envsubst does not support conditional logic.
	•	sing variables may render as empty strings.
	•	GitHub-hosted runners typically have envsubst available on ubuntu-latest.

⸻

#### Links
- Repository: https://github.com/Malnati/templateer
	•	Releases: https://github.com/Malnati/templateer/releases
	•	License: https://github.com/Malnati/templateer/blob/main/LICENSE

