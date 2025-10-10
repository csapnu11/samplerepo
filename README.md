## samplerepo - Template workflow in n8n ##

---


# n8n Daily JSON Update Workflow

This n8n workflow runs daily at 9:00 AM and performs the following automated steps:

1. Trigger – Schedules the workflow to run daily at 09:00 AM.
2. Fetch JSON File – Retrieves a JSON file from a GitHub repository.
3. Update Properties – Modifies the `date` and `runCount` fields in the JSON content.
4. Commit Changes – Pushes the updated JSON file back to the GitHub repo with a commit message.

---

## Workflow Overview

### Node 1: Schedule Trigger
- Type: `Schedule`
- Config: Runs daily at `09:00 AM` in your local timezone.
- Purpose: Automates the daily trigger for the workflow.

---

### Node 2: HTTP Request – Fetch JSON File from GitHub
- Method: `GET`
- URL:
```

[https://api.github.com/repos/](https://api.github.com/repos/)<your-username>/<your-repo>/contents/daily_update.json

```
- Authentication: GitHub Personal Access Token (PAT) with `repo` scope.
- Output: Base64-encoded content and `sha` of the file, required for updating.

---

### Node 3: Function – Update `date` and `lastRun`
- Logic:
- Decodes base64 JSON content
- Parses it and updates:
  - `date`: formatted as `YYYY-MM-DD`
  - `runCount`: total run count
- Encodes the updated content back to base64

- Output:
- `updatedContent` (base64-encoded JSON)
- `sha` (from original file, required for update)

---

### Node 4: HTTP Request – Commit Changes Back to GitHub
- Method: `PUT`
- URL:
```

[https://api.github.com/repos/](https://api.github.com/repos/)<your-username>/<your-repo>/contents/daily_update.json

````
- Authentication: GitHub PAT
- Body:
```json
{
  "message": "Daily update by n8n",
  "content": "<base64 content>",
  "sha": "<original sha>"
}
````

---

## JSON File Example (`daily_update.json`)

```json
{
  "date": "2025-10-10",
  "runCount": 3,
  "system_status": {
    "cpu_usage": "15%",
    "memory_usage": "62%"
  }
}
```

---

## GitHub Access

You must use a GitHub Personal Access Token (PAT) with the `repo` scope enabled. Store it in n8n as a GitHub credential or use it in an `HTTP Request` node with header authentication.

Example header:

```
Authorization: Bearer <your-token>
```

---


## Requirements

* n8n instance (cloud or self-hosted)
* GitHub repo with the target JSON file
* GitHub Personal Access Token


