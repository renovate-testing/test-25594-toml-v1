# Minimal Reproduction for Renovate failing to parse valid TOML v1.0.0 spec.

This reproduction demonstrates how renovate fails to parse a valid pyproject.toml.

You can see the parsing error via the action logs [here](https://github.com/BradenM/renovate-toml-v1-repro/actions/runs/3358868498/jobs/5566325881#step:2:397).

See renovatebot/renovate#18668 for more details.

## The Bug

The exact error is as follows:

```
DEBUG: Matched 1 file(s) for manager poetry: pyproject.toml (repository=bradenm/renovate-toml-v1-repro)
DEBUG: Error parsing pyproject.toml file (repository=bradenm/renovate-toml-v1-repro)
       "err": {
         "name": "TomlError",
         "fromTOML": true,
         "wrapped": null,
         "line": 9,
         "col": 38,
         "pos": 271,
         "message": "Inline lists must be a single type, not a mix of string and inline-table at row 10, col 39, pos 271:\n 9:   \"README.md\",\n10>   { path = \"tests\", format = \"sdist\" }\n                                          ^\n11: ]\n\n",
         "stack": "TomlError: Inline lists must be a single type, not a mix of string and inline-table at row 10, col 39, pos 271:\n 9:   \"README.md\",\n10>   { path = \"tests\", format = \"sdist\" }\n                                          ^\n11: ]\n\n\n    at TOMLParser.recordInlineListValue (/usr/src/app/node_modules/@iarna/toml/lib/toml-parser.js:1305:28)\n    at TOMLParser.runOne (/usr/src/app/node_modules/@iarna/toml/lib/parser.js:64:30)\n    at TOMLParser.returnNow (/usr/src/app/node_modules/@iarna/toml/lib/parser.js:107:17)\n    at TOMLParser.recordValue (/usr/src/app/node_modules/@iarna/toml/lib/toml-parser.js:523:19)\n    at TOMLParser.runOne (/usr/src/app/node_modules/@iarna/toml/lib/parser.js:64:30)\n    at TOMLParser.parse (/usr/src/app/node_modules/@iarna/toml/lib/parser.js:45:22)\n    at parseString (/usr/src/app/node_modules/@iarna/toml/parse-string.js:13:12)\n    at Object.extractPackageFile (/usr/src/app/node_modules/renovate/lib/modules/manager/poetry/extract.ts:130:26)\n    at extractPackageFile (/usr/src/app/node_modules/renovate/lib/modules/manager/index.ts:70:9)\n    at getManagerPackageFiles (/usr/src/app/node_modules/renovate/lib/workers/repository/extract/manager-files.ts:52:43)\n    at /usr/src/app/node_modules/renovate/lib/workers/repository/extract/index.ts:47:28\n    at async Promise.all (index 2)\n    at extractAllDependencies (/usr/src/app/node_modules/renovate/lib/workers/repository/extract/index.ts:45:26)\n    at extract (/usr/src/app/node_modules/renovate/lib/workers/repository/process/extract-update.ts:100:20)\n    at extractDependencies (/usr/src/app/node_modules/renovate/lib/workers/repository/process/index.ts:110:26)\n    at Object.renovateRepository (/usr/src/app/node_modules/renovate/lib/workers/repository/index.ts:48:52)\n    at attributes.repository (/usr/src/app/node_modules/renovate/lib/workers/global/index.ts:173:11)\n    at Object.start (/usr/src/app/node_modules/renovate/lib/workers/global/index.ts:158:7)\n    at /usr/src/app/node_modules/renovate/lib/renovate.ts:17:22"
       }
DEBUG: Found asdf package files (repository=bradenm/renovate-toml-v1-repro)
```

It is caused by the following excerpt from pyproject.toml:
https://github.com/BradenM/renovate-toml-v1-repro/blob/ae92c263d27b7c2069e5121aa5184b326e942c54/pyproject.toml#L8-L11

The problematic toml lines are valid according to the latest TOML spec:

> Array
>
> Arrays are square brackets with values inside. Whitespace is ignored. Elements are separated by commas. Arrays can contain values of the same data types as allowed in key/value pairs. **Values of different types may be mixed**.
>
> Source: _https://toml.io/en/v1.0.0#array_

## Info

This repo has a simple workflow that:

- Uses poetry to validate the pyproject.toml
- Executes the latest renovate/renovate docker image to reproduce the failure

See the latest runs [here](https://github.com/BradenM/renovate-toml-v1-repro/actions/workflows/reproduce.yml).

See point of failure of the renovate reproduction [here](https://github.com/BradenM/renovate-toml-v1-repro/actions/runs/3358868498/jobs/5566325881#step:2:397).
