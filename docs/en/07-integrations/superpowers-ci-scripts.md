# Superpowers CI & Validation Examples

This document contains example scripts and a small Python validator used as a CI step to validate OpenSpec headers and battle-case schema before publishing or running battles.

Quick GitHub Actions snippet (extended):

```yaml
name: Validate & Run Battle-Subset
on: [pull_request]
jobs:
  validate-and-battle:
    runs-on: ubuntu-latest
    env:
      PROJECT: project-a
    steps:
      - uses: actions/checkout@v3
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install jsonschema pyyaml requests
      - name: Validate OpenSpec & battle-case schema
        run: |
          python scripts/validate_superpowers.py docs/02-projects/${{ env.PROJECT }}/agents docs/02-projects/${{ env.PROJECT }}/tests/battle
      - name: Install Superpowers CLI
        run: |
          curl -sSfL https://example.com/superpowers/install.sh | bash
      - name: Run battle-subset via Superpowers
        env:
          SUPERPOWERS_TOKEN: ${{ secrets.SUPERPOWERS_TOKEN }}
        run: |
          superpowers battle run --subset --project ${{ env.PROJECT }} --simulator-start-on-demand
```

Sample Python validator (scripts/validate_superpowers.py)

```python
#!/usr/bin/env python3
import sys, json, os, glob, yaml
from jsonschema import validate, ValidationError

BATTLE_SCHEMA_PATH = 'docs/en/06-analysis/schemas/battle-case.jsonschema'

def load_schema(path):
    with open(path,'r') as f:
        return json.load(f)

def validate_yaml_files(schema, paths):
    errors = 0
    for p in paths:
        for fn in glob.glob(os.path.join(p, '*.yaml')):
            with open(fn,'r') as f:
                doc = yaml.safe_load(f)
            try:
                validate(instance=doc, schema=schema)
                print(f"OK: {fn}")
            except ValidationError as e:
                print(f"VALIDATION ERROR in {fn}: {e.message}")
                errors += 1
    return errors

if __name__ == '__main__':
    args = sys.argv[1:]
    if len(args) < 2:
        print('Usage: validate_superpowers.py <agents_dir> <battle_dir>')
        sys.exit(2)
    battle_schema = load_schema(BATTLE_SCHEMA_PATH)
    battle_dir = args[1]
    errs = validate_yaml_files(battle_schema, [battle_dir])
    if errs:
        print(f"Found {errs} validation errors")
        sys.exit(1)
    print('Validation passed')
```

Notes

- The validator is a starter example; production CI should also validate OpenSpec header fields and other JSON Schemas (prompt, agent).
- The `--simulator-start-on-demand` flag is hypothetical: your Superpowers runner should support starting required simulators based on `simulator_spec`.
