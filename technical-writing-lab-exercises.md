# Technical Writing Lab Exercises

This submission demonstrates three software-engineering documentation roles:

- **User manual author:** guides a beginner through a repeatable task.
- **API technical writer:** documents a service contract for developers.
- **Technical report contributor:** explains technical concepts and responsibilities clearly for a project audience.

## HTTP and JSON: essential definitions

**HTTP (Hypertext Transfer Protocol)** is the request-and-response protocol used by clients and servers to exchange resources over a network. A client sends an HTTP request containing a method, URL, headers, and sometimes a body. The server returns an HTTP response containing a status code, headers, and sometimes a body. For example, `POST` asks a server to create a resource, while `GET` asks it to return a resource.

**JSON (JavaScript Object Notation)** is a text format for representing structured data. JSON objects contain name-value pairs in braces (`{}`), arrays contain ordered values in brackets (`[]`), and strings use double quotation marks. JSON supports strings, numbers, booleans (`true` or `false`), `null`, objects, and arrays. JSON is commonly used in HTTP request and response bodies because it is readable by people and easy for programs to parse.

## A. User Manual Procedure: Create a Python Virtual Environment

This procedure uses Python because it is a common first-semester development tool and keeps project dependencies separate from the rest of the computer.

### Prerequisites

Before starting, you need:

- A Windows, macOS, or Linux computer.
- Python 3.10 or later installed from [python.org](https://www.python.org/downloads/).
- Permission to create files in a project folder.
- A terminal application, such as PowerShell, Command Prompt, Terminal, or Bash.
- A stable internet connection for downloading the package.

### Procedure

1. **Open a terminal.**  
   **Expected result:** A command prompt appears and accepts keyboard input.

2. **Create a project folder by running `mkdir python-practice`.**  
   **Expected result:** A folder named `python-practice` exists in the terminal's current location.

3. **Enter the project folder by running `cd python-practice`.**  
   **Expected result:** The terminal's current path ends with `python-practice`.

4. **Create a virtual environment by running `python -m venv .venv`.**  
   **Expected result:** A hidden or dot-prefixed `.venv` folder appears in the project folder.

5. **Activate the environment by running `.venv\Scripts\Activate.ps1` in PowerShell.**  
   **Expected result:** The terminal prompt begins with `(.venv)`.

   On macOS or Linux, run `source .venv/bin/activate` instead.

6. **Install the Requests package by running `python -m pip install requests`.**  
   **Expected result:** The terminal reports that `requests` and its dependencies installed successfully.

7. **Verify the installation by running `python -c "import requests; print(requests.__version__)"`.**  
   **Expected result:** A Requests version number appears without an import error.

8. **Deactivate the environment by running `deactivate`.**  
   **Expected result:** The `(.venv)` prefix disappears from the terminal prompt.

### Screenshot description

Include a screenshot of the terminal immediately after step 7. The screenshot should show the active `(.venv)` prompt, the installation command result, and the printed Requests version number. Do not include personal usernames or unrelated terminal history.

### Troubleshooting

**Error:** `python is not recognized` or `python: command not found`.

**Fix:** Install Python from [python.org](https://www.python.org/downloads/) and select **Add Python to PATH** during installation. Close and reopen the terminal, then run `python --version` to confirm that the command is available.

## B. API Reference Entry: Create a Project Task

### Overview

Creates a new task in a project for the authenticated user. The project must exist, and the authenticated user must have permission to create tasks in it. The response returns the saved task, including its server-generated identifier and timestamps.

### HTTP method and endpoint

```http
POST /api/v1/projects/{projectId}/tasks
```

### Path parameters

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `projectId` | string | Yes | Unique identifier of the project that will contain the task. |

### Request headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Yes | Bearer access token for an authenticated user. Format: `Bearer <access-token>`. |
| `Content-Type` | Yes | Must be `application/json` because the request body is JSON. |
| `Accept` | Recommended | Should be `application/json` to request a JSON response. |

### Request body

The request body must be a JSON object with the following properties:

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Task name. Must contain 1–200 characters. |
| `description` | string | No | Additional task details. If omitted, the task has no description. |
| `assigneeId` | string | Yes | Unique identifier of the user assigned to the task. The user must belong to the project. |
| `dueDate` | string (ISO 8601 date) | Yes | Date by which the task is due, in `YYYY-MM-DD` format. |
| `priority` | string | Yes | Urgency of the task. Allowed values are `low`, `medium`, or `high`. |

### Example request

```http
POST /api/v1/projects/proj_8f31/tasks HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
Accept: application/json

{
  "title": "Prepare release notes",
  "description": "Summarize the changes included in version 2.4.",
  "assigneeId": "usr_1042",
  "dueDate": "2026-09-30",
  "priority": "medium"
}
```

### Example successful response

**HTTP 201 Created**

```json
{
  "id": "task_73b91c",
  "projectId": "proj_8f31",
  "title": "Prepare release notes",
  "description": "Summarize the changes included in version 2.4.",
  "assigneeId": "usr_1042",
  "dueDate": "2026-09-30",
  "priority": "medium",
  "status": "todo",
  "createdBy": "usr_1007",
  "createdAt": "2026-09-06T16:17:07Z",
  "updatedAt": "2026-09-06T16:17:07Z"
}
```

### Response status codes

| Code | Meaning |
| --- | --- |
| `201 Created` | The task was created successfully. |
| `400 Bad Request` | The JSON is malformed, a required property is missing, a value has the wrong type, or `priority` is not `low`, `medium`, or `high`. |
| `401 Unauthorized` | The `Authorization` header is missing, expired, or invalid. |
| `403 Forbidden` | The token is valid, but the user cannot create tasks in this project or cannot assign the selected user. |
| `404 Not Found` | The project or assignee does not exist. |
| `409 Conflict` | The request conflicts with the current project state, such as a duplicate task identifier supplied by an upstream integration. |
| `415 Unsupported Media Type` | The `Content-Type` header is missing or is not `application/json`. |
| `422 Unprocessable Entity` | The JSON is syntactically valid, but a value violates a business rule, such as a due date that cannot be accepted. |
| `429 Too Many Requests` | The client has exceeded the API rate limit. |
| `500 Internal Server Error` | The server encountered an unexpected error while processing the request. |
| `503 Service Unavailable` | The task service is temporarily unavailable. |

## C. Technical Report Section: Documentation Across Software Engineering Roles

Technical documentation supports different decisions at different stages of software delivery. A **software developer** documents implementation details, interfaces, and limitations so that other developers can safely maintain the code. A **QA engineer** uses acceptance criteria, reproducible steps, and expected results to verify that behavior matches requirements. A **DevOps or site reliability engineer** documents deployment commands, configuration, monitoring, and recovery procedures so that services remain available. A **product manager** supplies user goals and business rules that define what the system must accomplish. A **technical writer** translates these inputs into audience-appropriate procedures, reference material, and explanations.

These roles are connected by shared documentation practices: precise terminology, explicit assumptions, versioned examples, and feedback from real users. A beginner procedure prioritizes one action at a time and visible results, while an API reference prioritizes an exact contract and complete error behavior. Treating both as technical interfaces reduces ambiguity, shortens troubleshooting time, and gives the team a reliable record of how the product should be used.
