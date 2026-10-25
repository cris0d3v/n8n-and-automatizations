# Workflow Guidelines

This document defines conventions for adding, testing, documenting, and sharing n8n workflows in this repository.

The goal is to keep each example understandable, reproducible, and safe to import into another n8n instance.

## 1. Directory structure

Store each workflow in its own directory, grouped by purpose:

```text
workflows/
├── integrations/
│   └── rest-api-example/
│       ├── workflow.json
│       ├── README.md
│       └── examples/
│           ├── input.json
│           └── output.json
├── notifications/
├── data-processing/
└── ai/
```

Create category directories when they contain a real workflow; do not add empty directories just to reserve space. Add `examples/input.json` and `examples/output.json` only when sample data improves understanding.

## 2. Naming conventions

- Use `kebab-case` for directory and file names, for example `bcv-exchange-rates` or `rest-api-to-telegram`.
- Use `workflow.json` for the n8n workflow export.
- Use `README.md` for workflow-specific documentation.
- Give each workflow a descriptive name that explains its purpose, not just the node or service it uses.
- Keep one main workflow per directory. If an example requires a separate reusable sub-workflow, document that dependency clearly.

## 3. Workflow README template

Each workflow's `README.md` should contain the following information, where applicable:

```markdown
# Workflow name

## Purpose

What problem does this workflow solve?

## How it works

Describe the trigger and the main processing steps.

## Requirements

List the n8n version tested, external services, and required credentials.

## Configuration

Describe environment variables, node settings, and credential types that must be configured.
Do not include secret values.

## Test

Explain how to run the workflow with sample or sanitized data.

## Expected result

Describe the expected output or side effect.

## Limitations

Document known constraints, API limits, costs, or assumptions.
```

Keep the documentation focused on the workflow's purpose and the steps needed to test it. Do not claim a workflow is tested unless it has actually been run successfully.

## 4. Export and import

- Export workflows from n8n as JSON and save the export as `workflow.json`.
- Import the file into a local or test instance and check that it opens correctly.
- Reconfigure credentials in the receiving instance rather than relying on credentials from the original instance.
- Test with safe sample input before enabling production triggers.
- Record the n8n version used for testing if version compatibility may matter.

See the official [n8n export and import guide](https://docs.n8n.io/workflows/export-import/).

## 5. Security and privacy review

Review every export before committing it. n8n workflow exports can include credential names and IDs, and HTTP Request nodes may include authentication headers or other values, especially when created from cURL imports. Names and IDs may be instance-specific, and names can expose internal details.

Before publishing, verify that the workflow does not expose:

- API keys, access tokens, passwords, OAuth secrets, or authentication headers.
- Private credential names, client names, account identifiers, or internal URLs that should not be public.
- Personal data, customer records, production payloads, or private execution output.
- Internal hostnames, IP addresses, file paths, or infrastructure details that reveal private systems.
- Sensitive information in sticky notes, node names, expressions, sample files, screenshots, or documentation.

Use n8n's credential management for secrets. Replace private values in examples with clearly fake placeholders such as `YOUR_API_KEY` or synthetic sample records. Do not commit real `.env` files, credential exports, database files, execution logs, or backups.

If a secret is accidentally committed, deleting it from the latest file is not enough. Revoke or rotate the exposed secret first, then clean up the repository history as appropriate.

## 6. Sample data

- Make examples small and representative.
- Use synthetic values instead of customer or production data.
- Keep `input.json` and `output.json` valid JSON.
- Do not include real email addresses, phone numbers, API responses containing personal information, or other private records.
- Explain any fields the user must replace before running the workflow.

## 7. AI workflows

For workflows that call AI providers or models, document:

- Which service or model is required, if applicable.
- What input data is sent to the provider.
- Any expected usage costs or rate limits.
- How credentials are configured.
- Any important limitations, validation steps, or human review requirements.

Do not send confidential or personal data to an AI provider in an example unless the intended environment and data handling have been explicitly reviewed.

## 8. Pull request checklist

Before adding or updating a workflow, check that:

- [ ] The export is valid JSON and can be imported into a test instance.
- [ ] The workflow has a descriptive name and is stored in the correct category.
- [ ] Its `README.md` explains the purpose, configuration, and test procedure.
- [ ] Any sample input and output are synthetic and valid.
- [ ] Credentials, tokens, private URLs, and personal data have been removed or anonymized.
- [ ] Required external services, costs, and known limitations are documented.
- [ ] The workflow has been tested, and the documentation accurately states the test status.

## References

- [n8n export and import](https://docs.n8n.io/workflows/export-import/)
- [n8n security audit](https://docs.n8n.io/hosting/securing/security-audit/)
