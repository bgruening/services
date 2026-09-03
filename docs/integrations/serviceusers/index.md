# Serviceuser API

The **Serviceuser API** is an API endpoint that lets an *external
service* programmatically create and manage dedicated **users** and
their Jupyter servers on Jupyter4NFDI. 


## How it works

* A **service** is a registered JupyterHub service that holds an API token. 
  The service is the *caller* of this API.
* Every call is scoped to that one service. The users it manages are
  stored under the name `service:<service-name>:<user-name>`, so users created
  by different services never collide.
* A serviceuser can run one **JupyterLab**. When a server is
  (re)started, the hub issues a **short-lived API token** for it and embeds it
  in the `next_url` returned by the API.
* Opening `next_url` takes you to the live JupyterLab instance (it renders a
  small landing page and redirects into the server). `logs_url` points at a
  Server-Sent-Events stream that reports spawn progress and logs.

A typical service workflow is:

```
POST   /hub/api/serviceuser/<user>   ->  start server, get next_url
GET    /hub/api/users/<user>/servers/<server>/progress   ->  get EventStream with starting logs
...                                        ->  use next_url 
DELETE /hub/api/serviceuser/<user>   ->  stop server
```


## API Reference

* **Base URL:** `https://hub.nfdi-jupyter.de/hub/api/serviceuser`
* **Authentication:** `Authorization: token <SERVICE_API_TOKEN>`
  (the token must carry the `custom:serviceusers` scope)
* **User naming:** pass the user name *without* the `service:<service>:`
  prefix - the hub adds it automatically.

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/serviceuser/{user_name}` | Create the user (if needed) and start a named server |
| `GET` | `/api/serviceuser/{user_name}` | Get the status of a server |
| `DELETE` | `/api/serviceuser/{user_name}` | Stop the server, optionally delete the user |

### Interactive documentation (Swagger UI)

The OpenAPI description of this API is rendered below. Use the **Authorise**
button in the top-right corner and enter your token as
`token <SERVICE_API_TOKEN>` to try the endpoints against the hub.

???+ note "Machine-readable specification"
    The full [OpenAPI 3.0 specification](openapi.yaml) is available next to
    this page and is the recommended input for code generators
    (e.g. `openapi-generator`, `openapi-python-client`).

<div id="swagger-ui" style="background:#fff; border:1px solid var(--md-default-fg-color--lightest, #eee); border-radius:6px; min-height: 850px;"></div>
<link rel="stylesheet" href="https://unpkg.com/swagger-ui-dist@5.11.0/swagger-ui.css">
<script src="https://unpkg.com/swagger-ui-dist@5.11.0/swagger-ui-bundle.js"></script>
<script src="https://unpkg.com/swagger-ui-dist@5.11.0/swagger-ui-standalone-preset.js"></script>
<script>
window.addEventListener('load', function () {
  window.swaggerUI = SwaggerUIBundle({
    url: 'openapi.yaml',
    dom_id: '#swagger-ui',
    deepLinking: true,
    presets: [SwaggerUIBundle.presets.apis, SwaggerUIStandalonePreset],
    layout: 'StandaloneLayout',
    validatorUrl: 'none'
  });
});
</script>

### Example request / response

**Start a server**

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "user_options": { "flavor": "m1" } }' \
  "https://hub.nfdi-jupyter.de/hub/api/serviceuser/userX"
```

`201 Created` while spawning:

```json
{
  "status": "spawn_pending",
  "next_url": "https://hub.nfdi-jupyter.de/hub/serviceuser/userX?token=....",
  "logs_url": "https://hub.nfdi-jupyter.de/hub/api/users/service:my-external-service:userX/servers/my-external-service/progress"
}
```

`200 OK` once running:

```json
{
  "status": "running",
  "next_url": "https://hub.nfdi-jupyter.de/hub/serviceuser/userX?token=....",
  "logs_url": "https://hub.nfdi-jupyter.de/hub/api/users/service:my-external-service:userX/servers/my-external-service/progress"
}
```

**Poll the status**

```bash
curl -H "Authorization: token YOUR_TOKEN" \
  "https://hub.nfdi-jupyter.de/hub/api/serviceuser/userX"
```

**Stop the server** (and delete the user)

```bash
curl -X DELETE \
  -H "Authorization: token YOUR_TOKEN" \
  "https://hub.nfdi-jupyter.de/hub/api/serviceuser/userX?delete_user=true"
```

## Response fields

| Field | Type | Present when | Description |
|-------|------|--------------|-------------|
| `status` | string | always | `running`, `spawn_pending`, `stop_pending` or `stopped`. |
| `next_url` | string (URI) | server active | URL of the live server; a short-lived API token is embedded as the `token` query parameter. |
| `logs_url` | string (URI) | server active | SSE endpoint streaming spawn progress and log lines. |
| `exit_code` | integer | when known | Exit code of the server process (e.g. after a crash or clean stop). |
| `logs` | list of string | when available | Log lines / spawn events collected by the spawner. |
| `reason` | string | on errors | Human-readable explanation of the error. |

### HTTP status codes

| Code | Meaning |
|------|---------|
| `200` | Server is running (GET) / request succeeded and server running (POST). |
| `201` | Server created and being spawned (POST). |
| `202` | Accepted; server pending (spawn/stop) or still stopping (DELETE). |
| `204` | Server stopped / user deleted (DELETE). |
| `400` | Bad request (server limit reached, user must not be created, invalid state). |
| `403` | Not authenticated, or token lacks the `custom:serviceusers` scope. |
| `404` | Service user or named server does not exist. |

## Notes and limitations

* **Isolation.** Because user names are prefixed with the service name, two
  services can both have a user called `userX` without interfering.
