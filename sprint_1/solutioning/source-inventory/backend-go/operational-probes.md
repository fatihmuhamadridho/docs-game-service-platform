# Go Operational Probe Specifications

> **Document Type:** Operational interface note  
> **Status:** Source-mapped, no business acceptance criteria  
> **Service / Domain:** Go HTTP server  
> **Consumer:** Load balancer, deployment platform, operator, or diagnostic caller

## Scope and disposition

The Go server registers `GET /api/health` and `GET /api/hello`. These are technical endpoints, not business capabilities. They remain outside business AC coverage unless product/platform explicitly assigns a user-facing outcome. Do not count them as missing business API contracts or as proof that dependencies are healthy unless the handler checks those dependencies.

## Endpoint specifications

| Method + path     | Purpose                             | Request         | Current response                                                                                                                                                                  | Business AC                              |
| ----------------- | ----------------------------------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| `GET /api/health` | Liveness/process-level health probe | No body or auth | HTTP 200 `{"status":"success","message":"Hello from Golang!"}`. Does not query dependencies. Other methods return HTTP 405 with `Allow: GET` and plain-text `Method not allowed`. | None; operational health ownership only. |
| `GET /api/hello`  | Diagnostic hello response           | No body or auth | HTTP 200 `{"message":"Hello"}`. Non-GET requests receive ServeMux method rejection.                                                                                               | None; diagnostic endpoint.               |

## Operational constraints

- Probe payloads are standalone JSON and do not use the feature API `{code,message,data}` envelope.
- `/api/health` is a process-handler liveness response only; no MongoDB, SMTP, blob-storage, or identity-provider readiness is checked.
- Platform owner must decide whether `/health` means liveness or readiness, required dependency checks, and whether `/hello` should remain exposed in production.
- Until that decision, treat `/health` as process response only and `/hello` as non-business diagnostic. Do not use either as payment, MongoDB, SMTP, or blob readiness evidence.
- Restrict `/hello` at ingress if production exposure is not required; exact policy is a deployment decision.
- Open platform decisions are tracked in [Backend Go QUESTION.md](QUESTION.md#q6--what-probe-readiness-and-hello-exposure-policy-is-required).

## Validation evidence

Source of route existence: `go-game-service-platform/internal/controller/http/system_http.go` and route registration. No product AC has been edited or created by this note.
