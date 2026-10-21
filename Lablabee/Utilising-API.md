# API Study Guide: Beginner to Advanced

> **Topic:** Utilizing APIs | **Study reference:** Lablabee Telco Cloud training  
> **Focus:** REST, HTTP, JSON, API testing, security, microservices, cloud, and 5G networks.

## Table of Contents
1. [Introduction to APIs](#1-introduction-to-apis)
2. [How APIs Work](#2-how-apis-work)
3. [Types of APIs](#3-types-of-apis)
4. [REST API Fundamentals](#4-rest-api-fundamentals)
5. [HTTP Fundamentals](#5-http-fundamentals)
6. [HTTP Methods and Status Codes](#6-http-methods-and-status-codes)
7. [JSON and Data Exchange](#7-json-and-data-exchange)
8. [HTTP Headers](#8-http-headers)
9. [API Design Best Practices](#9-api-design-best-practices)
10. [API Testing with cURL, Python, and Postman](#10-api-testing-with-curl-python-and-postman)
11. [Authentication and Authorization](#11-authentication-and-authorization)
12. [API Security](#12-api-security)
13. [Rate Limiting and Throttling](#13-rate-limiting-and-throttling)
14. [API Documentation and OpenAPI](#14-api-documentation-and-openapi)
15. [APIs and Microservices](#15-apis-and-microservices)
16. [APIs in Cloud and DevOps](#16-apis-in-cloud-and-devops)
17. [APIs in 5G Networks](#17-apis-in-5g-networks)
18. [CAMARA, GSMA, and TM Forum](#18-camara-gsma-and-tm-forum)
19. [Build a Simple API with FastAPI](#19-build-a-simple-api-with-fastapi)
20. [Troubleshooting and Interview Review](#20-troubleshooting-and-interview-review)
21. [Further Reading](#21-further-reading)

---

## 1. Introduction to APIs

**API** stands for **Application Programming Interface**. It is a defined interface that lets one software component request functionality or data from another without knowing its internal implementation.

**Example:** A weather app requests a forecast from a weather provider's API, receives structured data, and displays it to the user.

**Common use cases:** mobile/web backends, payment processing, login integrations, cloud resource management, IoT devices, and communication between microservices.

**Benefits:** reusability, integration, modularity, controlled access, automation, and faster development.

## 2. How APIs Work

```text
User -> Frontend -> HTTP request -> API server -> Database/service
User <- Frontend <- HTTP response <- API server <- Result
```

1. The client sends a request to an **endpoint** (a URL for an API operation).
2. The server authenticates and validates the request as needed.
3. The server processes the request, possibly contacting other services or a database.
4. The server returns an HTTP status code, headers, and usually a response body.

**Illustrative request:**

```http
GET /api/v1/tasks HTTP/1.1
Host: api.example.com
Accept: application/json
```

**Illustrative response:**

```http
HTTP/1.1 200 OK
Content-Type: application/json

[{"id":1,"title":"Study APIs","completed":false}]
```

> `api.example.com` is a placeholder; these examples do not rely on a live API.

## 3. Types of APIs

### REST
**Representational State Transfer** is an architectural style commonly implemented using HTTP methods and resource URLs. Responses are frequently JSON. REST interactions are stateless: each request contains the information required to process it, although the server can still store application data.

### SOAP
**Simple Object Access Protocol** is a messaging protocol that uses XML envelopes and has standardized fault handling. SOAP services often describe contracts with WSDL and can use WS-* standards for enterprise security and reliability.

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body><GetCustomer xmlns="https://example.com/customer"><Id>1</Id></GetCustomer></soap:Body>
</soap:Envelope>
```

### GraphQL
**GraphQL** is a typed API query language and execution system. Clients request specific fields, often through a single endpoint. It can reduce over-fetching, but requires careful query complexity controls.

```graphql
query {
  project(fullPath: "gitlab-org/graphql-sandbox") {
    name
    issues {
      nodes {
        title
        description
      }
    }
  }
}
```

Try the [GitLab GraphQL Explorer](https://gitlab.com/-/graphql-explorer) (project access and schema may change).

| Feature | REST | SOAP | GraphQL |
|---|---|---|---|
| Style | Architectural style | Messaging protocol | Query language/runtime |
| Common format | JSON | XML | JSON responses |
| Endpoint structure | Resource-oriented paths | Service operations | Often one endpoint |
| Strength | Simple, HTTP-native | Formal contracts and enterprise standards | Flexible field selection |
| Trade-off | Multiple requests or over-fetching possible | More verbose | Complexity, caching, and authorization need care |

## 4. REST API Fundamentals

REST principles include **client-server separation**, **stateless communication**, **cacheability**, **uniform interface**, **layered systems**, and optional **code on demand**.

### Resources and URLs

```text
https://api.example.com/api/v1/tasks/42
|-- host ----------| |version| |resource| |ID|
```

- `/api`: API prefix (convention, not a requirement).
- `/v1`: API version.
- `/tasks`: collection of tasks.
- `/42`: one task.

Prefer resource nouns: `GET /tasks` rather than `GET /getTasks`.

### CRUD mapping

| Operation | HTTP method | Example |
|---|---|---|
| Create | POST | `POST /tasks` |
| Read | GET | `GET /tasks/42` |
| Replace | PUT | `PUT /tasks/42` |
| Partially update | PATCH | `PATCH /tasks/42` |
| Delete | DELETE | `DELETE /tasks/42` |

**PUT** normally replaces the target representation; **PATCH** changes specified fields. Actual behavior is defined by the API contract.

## 5. HTTP Fundamentals

**HTTP** (Hypertext Transfer Protocol) is an application-layer request-response protocol. **HTTPS** is HTTP protected by TLS encryption.

An HTTP request contains a method, target, protocol version (in HTTP/1.1), headers, and an optional body. A response contains status information, headers, and an optional body.

```http
POST /api/v1/tasks HTTP/1.1
Host: api.example.com
Content-Type: application/json
Accept: application/json

{"title":"Learn HTTP"}
```

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/v1/tasks/43

{"id":43,"title":"Learn HTTP"}
```

**HTTP/2** supports binary framing and multiplexing multiple streams over a connection. It uses the same general HTTP semantics as HTTP/1.1. **HTTP/3** runs over QUIC.

### HTTP proxies
A proxy forwards requests or responses between clients and servers. A **forward proxy** acts on behalf of clients; a **reverse proxy** fronts servers and can provide routing, TLS termination, caching, and load balancing.

## 6. HTTP Methods and Status Codes

| Method | Typical purpose | Safe? | Idempotent? |
|---|---|---|---|
| GET | Retrieve | Yes | Yes |
| HEAD | Retrieve headers | Yes | Yes |
| POST | Submit/create | No | Not inherently |
| PUT | Replace resource | No | Yes |
| PATCH | Partial modification | No | Not inherently |
| DELETE | Delete resource | No | Yes |
| OPTIONS | Discover communication options | Yes | Yes |

**Safe** means the method is intended not to modify server state; **idempotent** means repeating the same request has the same intended effect as once.

| Code | Meaning | Typical scenario |
|---|---|---|
| 200 | OK | Successful GET/update |
| 201 | Created | New resource created |
| 202 | Accepted | Async work queued |
| 204 | No Content | Success without body |
| 301/308 | Redirect | Permanent move |
| 400 | Bad Request | Invalid request syntax/data |
| 401 | Unauthorized | Missing/invalid authentication |
| 403 | Forbidden | Authenticated but not allowed |
| 404 | Not Found | Resource not found |
| 405 | Method Not Allowed | Unsupported method |
| 409 | Conflict | State conflict |
| 422 | Unprocessable Content | Semantically invalid content |
| 429 | Too Many Requests | Rate limit reached |
| 500 | Internal Server Error | Server failure |
| 502 | Bad Gateway | Upstream invalid response |
| 503 | Service Unavailable | Overload/maintenance |
| 504 | Gateway Timeout | Upstream timeout |

## 7. JSON and Data Exchange

**JSON** (JavaScript Object Notation) is a text-based data format with objects, arrays, strings, numbers, booleans, and `null`.

```json
{
  "user": {"id": 7, "name": "Jane", "active": true},
  "skills": ["Python", "SQL", "AWS"],
  "manager": null
}
```

Rules: keys and string values use double quotes; no trailing commas; `true`, `false`, and `null` are lowercase. JSON is language-independent.

Python example:

```python
import json
payload = '{"title":"Learn APIs","completed":false}'
data = json.loads(payload)
print(data["title"])
print(json.dumps(data, indent=2))
```

## 8. HTTP Headers

Headers carry metadata about the request or response.

| Header | Purpose | Example |
|---|---|---|
| Accept | Desired response media type | `application/json` |
| Content-Type | Body's media type | `application/json` |
| Authorization | Credentials | `Bearer <token>` |
| User-Agent | Client identity | `my-api-client/1.0` |
| Cache-Control | Caching policy | `no-store` |
| ETag | Resource version validator | `"v1"` |
| If-None-Match | Conditional retrieval | `"v1"` |
| Location | Created resource or redirect | `/tasks/43` |
| Retry-After | Suggested retry delay | `60` |
| Access-Control-Allow-Origin | Browser cross-origin permission | `https://app.example.com` |

**CORS** is a browser-enforced cross-origin policy, not an authentication mechanism. Modern HTTP standards classify headers by semantics rather than requiring the older general/request/response/entity categories.

## 9. API Design Best Practices

1. **Use predictable resources:** `/products`, `/products/123`, `/products/123/reviews`.
2. **Version breaking changes:** `/api/v1/products` or another documented versioning scheme.
3. **Validate requests:** enforce data types, allowed ranges, and field constraints.
4. **Use consistent error bodies:** include a machine-readable error code and a useful message.
5. **Support pagination, filtering, sorting:**

```http
GET /api/v1/products?category=books&limit=20&offset=40&sort=-price
```

6. **Use correct status codes** and clear response headers.
7. **Document authentication and errors**, not only happy paths.
8. **Avoid exposing secrets or sensitive data** in URLs, logs, or errors.
9. **Use timeouts, retries with backoff, and idempotency keys** where appropriate.
10. **Consider caching and concurrency control**, such as `ETag` and `If-Match`.

Example error response:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "title must not be empty"
  }
}
```

## 10. API Testing with cURL, Python, and Postman

### cURL

```bash
# GET
curl -i https://jsonplaceholder.typicode.com/todos/1

# POST (public demo API; changes are simulated, not persisted)
curl -i -X POST https://jsonplaceholder.typicode.com/posts \
  -H 'Content-Type: application/json' \
  -d '{"title":"API practice","body":"Hello","userId":1}'

# Example authenticated request (replace placeholders)
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.example.com/api/v1/tasks
```

### Python requests

Install:

```bash
python -m pip install requests
```

```python
import requests

url = "https://jsonplaceholder.typicode.com/todos/1"
try:
    response = requests.get(url, timeout=10)
    response.raise_for_status()
    task = response.json()
    print(task["title"])
except requests.exceptions.RequestException as error:
    print(f"Request failed: {error}")
```

POST example:

```python
import requests

payload = {"title": "Practice API", "body": "Study", "userId": 1}
response = requests.post(
    "https://jsonplaceholder.typicode.com/posts",
    json=payload,
    timeout=10,
)
response.raise_for_status()
print(response.status_code, response.json())
```

### Postman workflow

1. Create a collection and add a request.
2. Select the HTTP method and URL.
3. Set **Headers**, **Authorization**, and **Body** as needed.
4. Click **Send**; inspect status, response headers, response body, and timing.
5. Save examples and write tests.

Example Postman post-response test:

```javascript
pm.test("Request succeeded", function () {
  pm.response.to.have.status(200);
});
pm.test("Response contains an id", function () {
  pm.expect(pm.response.json()).to.have.property("id");
});
```

**Testing checklist:** happy path, missing required fields, invalid data, missing/expired token, insufficient permissions, non-existent resources, rate limits, and server errors.

## 11. Authentication and Authorization

**Authentication (AuthN):** Who are you?  
**Authorization (AuthZ):** What are you allowed to do?

| Mechanism | How it works | Important note |
|---|---|---|
| Basic Auth | Base64-encoded username/password in header | Use only over HTTPS |
| API key | Application presents a secret key | Identifies client; not automatically user authentication |
| OAuth 2.0 | Delegated authorization using access tokens | Use appropriate grant/flow, e.g. Authorization Code + PKCE |
| JWT | Signed token carrying claims | A token format, not itself an authentication protocol |
| RBAC | Permissions determined by roles | Authorization model |
| ABAC | Permissions determined by attributes | Fine-grained authorization model |

```http
Authorization: Bearer eyJ...example-token
```

**JWT structure:** `header.payload.signature` for a signed compact JWT. Verify signature, issuer, audience, expiration, and algorithm according to your security design. Do not place secrets in readable JWT payloads.

**OAuth vs JWT:** OAuth 2.0 defines authorization flows; OAuth access tokens may be opaque strings or JWTs.

## 12. API Security

- **Enforce HTTPS:** use current TLS configurations and redirect HTTP where appropriate.
- **Authenticate and authorize every protected operation:** apply least privilege and object-level checks.
- **Validate inputs:** enforce schemas, types, and bounds; use parameterized SQL queries to prevent SQL injection.
- **Protect against injection and XSS:** encode output appropriately and avoid unsafe evaluation.
- **Store secrets securely:** environment variables or a secrets manager; never commit API keys.
- **Use rate limits and quotas:** reduce brute-force and abusive traffic.
- **Apply browser security controls where relevant:** HSTS, CSP, `X-Content-Type-Options`, and suitable framing policy.
- **Monitor logs and alerts:** log request IDs, latency, status, and error patterns, but redact credentials and personal data.
- **Avoid excessive data exposure:** return only necessary fields.
- **Use secure defaults:** restrict CORS, keep dependencies patched, and audit permissions.

**Common attacks:** broken object-level authorization (BOLA/IDOR), broken authentication, injection, excessive resource consumption, and security misconfiguration.

## 13. Rate Limiting and Throttling

**Rate limiting** sets a maximum number of requests per interval. **Throttling** controls how quickly requests are processed; systems may delay, queue, or reject excess requests. In practice, the terms sometimes overlap.

**Example:** 100 requests per minute per API key; request 101 receives:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
Content-Type: application/json

{"error":"Rate limit exceeded"}
```

### Algorithms

| Algorithm | Principle | Trade-off |
|---|---|---|
| Fixed window | Count requests in fixed intervals | Boundary bursts possible |
| Sliding window | Count within rolling interval | More accurate; more state |
| Token bucket | Tokens replenish over time | Supports controlled bursts |
| Leaky bucket | Drain/process at a controlled rate | Smooths traffic |

### Client-side retry with exponential backoff

```python
import time
import requests

url = "https://api.example.com/api/v1/tasks"
for attempt in range(4):
    try:
        response = requests.get(url, timeout=5)
        if response.status_code not in (429, 503):
            response.raise_for_status()
            print(response.json())
            break
    except requests.exceptions.RequestException:
        if attempt == 3:
            raise
    if attempt == 3:
        raise RuntimeError("Retries exhausted")
    time.sleep(min(2 ** attempt, 8))
```

Production clients should honor `Retry-After`, add jitter, set retry budgets, and avoid automatically retrying non-idempotent writes without safeguards.

**Controls:** API gateway policies, AWS WAF, authentication, quotas, alerts, and DDoS protection.

## 14. API Documentation and OpenAPI

**OpenAPI Specification (OAS)** is a machine-readable standard for describing HTTP APIs. **Swagger** refers to a family of related tools, such as Swagger UI and Swagger Editor. OpenAPI definitions can be written in YAML or JSON.

```yaml
openapi: 3.0.3
info:
  title: Task API
  version: 1.0.0
servers:
  - url: https://api.example.com
paths:
  /tasks:
    get:
      summary: List tasks
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Task'
components:
  schemas:
    Task:
      type: object
      required: [id, title]
      properties:
        id:
          type: integer
        title:
          type: string
        completed:
          type: boolean
```

**Key sections:** `openapi`, `info`, `servers`, `paths`, `components`, `security`, and `tags`.

**Tools:** [Swagger Editor](https://editor.swagger.io/), [Swagger UI](https://swagger.io/tools/swagger-ui/), [Redoc](https://redocly.com/), and [Postman](https://www.postman.com/).

Good documentation explains endpoints, authentication, query/path parameters, request bodies, sample responses, error codes, pagination, limits, and version changes.

## 15. APIs and Microservices

**Monolith:** most functionality is packaged and deployed as one application.  
**Microservices:** separate services own specific business capabilities and communicate through APIs or messages.

```text
                  API Gateway
                       |
             +---------+---------+
             |         |         |
          User API  Order API  Payment API
             |         |         |
           User DB   Order DB  Payment DB
```

**Advantages:** independent deployments and scaling, focused ownership, and technology flexibility.  
**Challenges:** network latency, distributed failures, consistency, observability, deployment complexity, and security.

### Communication

- **Synchronous:** REST/HTTP or gRPC; caller waits for response.
- **Asynchronous:** RabbitMQ, Kafka, or NATS; services exchange messages/events.
- **Service discovery:** identifies service instances and endpoints.
- **Service mesh:** Istio or Linkerd can provide traffic management, mutual TLS, and telemetry.

### Deployment patterns

| Pattern | Description |
|---|---|
| Rolling | Replace instances incrementally |
| Blue-green | Switch traffic between old and new environments |
| Canary | Gradually expose a new release to some traffic |
| Serverless | Run functions on managed execution infrastructure |

**Containers:** Docker/Podman package services. **Orchestration:** Kubernetes schedules, scales, and manages workloads. **CI/CD:** GitLab CI/CD or Jenkins automates tests and deployments.

## 16. APIs in Cloud and DevOps

Cloud providers expose APIs for infrastructure operations. Infrastructure-as-Code tools use provider APIs to create and update resources.

**AWS examples:**

- **API Gateway:** expose and manage APIs; authentication, throttling, routing, and monitoring.
- **Lambda:** run backend functions triggered by API requests.
- **IAM:** control permissions for AWS API operations.
- **CloudWatch:** collect metrics and logs.
- **CloudFormation:** provision resources through declarative templates.

**Typical serverless request flow:**

```text
Browser -> HTTPS -> API Gateway -> Lambda -> DynamoDB
                                      |
                                   CloudWatch
```

**DevOps workflow:** write API contract -> implement -> unit/integration tests -> security scan -> CI/CD -> deploy -> observe latency, errors, traffic, and saturation.

## 17. APIs in 5G Networks

### 5G Service-Based Architecture (SBA)

The 5G Core uses a **Service-Based Architecture** for many control-plane interactions. Network Functions (NFs) expose and consume standardized services using **Service-Based Interfaces (SBIs)**, commonly with HTTP/2 and JSON.

| Network Function | Role |
|---|---|
| AMF | Access and Mobility Management Function |
| SMF | Session Management Function |
| UPF | User Plane Function: packet forwarding (not a typical HTTP SBI service) |
| NRF | Network Repository Function: NF registration and discovery |
| AUSF | Authentication Server Function |
| UDM | Unified Data Management |
| PCF | Policy Control Function |
| NSSF | Network Slice Selection Function |
| NEF | Network Exposure Function: controlled exposure of network capabilities |
| SCP | Service Communication Proxy: indirect NF communication/routing |

> **Important distinction:** The correct name is **Network Repository Function (NRF)**, not “Network Function Registry (NFR).” SBA HTTP APIs mainly serve the control plane; user-plane forwarding and some other interfaces use different protocols.

### NF registration and discovery

```text
1. SMF ---- register NF profile ----> NRF
2. AMF ---- discover SMF ---------> NRF
3. AMF <--- matching SMF profile --- NRF
4. AMF ---- session service ------> SMF
```

An NF profile may include NF type, instance ID, supported services, addresses, PLMN identifiers, and supported network slices.

**Conceptual registration operation:**

```http
PUT /nnrf-nfm/v1/nf-instances/{nfInstanceID}
Content-Type: application/json
```

This illustrates the operation shape; a real request must follow the applicable 3GPP schema and authorization requirements. A successful registration may return `201 Created` or `200 OK` depending on whether the NF instance was created or updated.

**5G API documentation:** 3GPP publishes OpenAPI descriptions for many 5G core services. Explore the [3GPP 5G API repository](https://forge.3gpp.org/rep/all/5G_APIs) and the NRF NF Management specification `TS29510_Nnrf_NFManagement.yaml`.

### HTTP/2 in 5G

HTTP/2 multiplexes concurrent streams over one connection and uses binary framing. 5G SBA relies on standardized HTTP-based APIs for numerous NF service operations, but **not every 5G control-plane or user-plane interface is REST/HTTP/2**.

## 18. CAMARA, GSMA, and TM Forum

### CAMARA

**CAMARA** is an open-source Linux Foundation project, working with industry partners including GSMA, to define consistent, developer-friendly **telecom network APIs**. Its focus is exposure of operator capabilities to applications across networks.

Example API capability areas include **number verification**, **SIM swap**, **device location**, and **quality on demand**, depending on operator support and applicable consent/authorization requirements.

### GSMA

The **GSMA Open Gateway** initiative helps operators commercialize and expose standardized network capabilities and promotes alignment across the industry.

### TM Forum

**TM Forum Open APIs** focus on business and operational processes such as product catalogs, orders, service management, and application onboarding.

| Organization/project | Main focus |
|---|---|
| CAMARA | Developer-facing telecom API definitions and testing |
| GSMA Open Gateway | Operator ecosystem, alignment, adoption, and commercialization |
| TM Forum | Operational/business APIs and Open Digital Architecture |

**Example integration:** an app developer onboards with a network API provider, registers an application, obtains credentials and permissions, invokes a supported network capability, and monitors usage and errors.

Useful resources: [CAMARA](https://camaraproject.org/), [GSMA Open Gateway](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/), [GSMA API map](https://open-gateway.gsma.com/map), and [TM Forum Open APIs](https://www.tmforum.org/oda/open-apis/).

## 19. Build a Simple API with FastAPI

### Install

```bash
python -m pip install fastapi uvicorn
```

### Create `main.py`

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI(title="Study Tasks API")

class TaskCreate(BaseModel):
    title: str

class Task(TaskCreate):
    id: int
    completed: bool = False

tasks: dict[int, Task] = {}
next_id = 1

@app.get("/tasks", response_model=list[Task])
def list_tasks():
    return list(tasks.values())

@app.get("/tasks/{task_id}", response_model=Task)
def get_task(task_id: int):
    if task_id not in tasks:
        raise HTTPException(status_code=404, detail="Task not found")
    return tasks[task_id]

@app.post("/tasks", response_model=Task, status_code=201)
def create_task(payload: TaskCreate):
    global next_id
    task = Task(id=next_id, title=payload.title)
    tasks[next_id] = task
    next_id += 1
    return task

@app.delete("/tasks/{task_id}", status_code=204)
def delete_task(task_id: int):
    if task_id not in tasks:
        raise HTTPException(status_code=404, detail="Task not found")
    del tasks[task_id]
```

### Run

```bash
uvicorn main:app --reload
```

Open:

- API: `http://127.0.0.1:8000/tasks`
- Interactive Swagger UI: `http://127.0.0.1:8000/docs`
- OpenAPI JSON: `http://127.0.0.1:8000/openapi.json`

Test:

```bash
curl -i -X POST http://127.0.0.1:8000/tasks \
  -H 'Content-Type: application/json' \
  -d '{"title":"Complete API notes"}'

curl -i http://127.0.0.1:8000/tasks
curl -i http://127.0.0.1:8000/tasks/1
curl -i -X DELETE http://127.0.0.1:8000/tasks/1
```

> This is a **learning-only, in-memory API**: data disappears when the process restarts, and it has no authentication or persistent database. Do not expose it publicly without production hardening.

### Practice challenges

1. Add `PATCH /tasks/{task_id}` to change `title` or `completed`.
2. Add validation that prevents empty task titles.
3. Add pagination to `GET /tasks`.
4. Write automated tests using FastAPI's `TestClient`.
5. Add API-key authentication for protected endpoints.
6. Containerize the API with Docker.
7. Add a GitLab CI/CD pipeline for tests and linting.
8. Document all endpoints and errors using OpenAPI.

## 20. Troubleshooting and Interview Review

### Common errors

| Symptom | Likely cause | First check |
|---|---|---|
| 400/422 | Invalid payload | JSON, schema, required fields |
| 401 | Authentication failed | Token, expiry, credentials |
| 403 | Permission denied | Roles and resource ownership |
| 404 | Missing resource/route | URL, ID, method |
| 405 | Wrong HTTP method | Endpoint contract |
| 429 | Rate limited | Retry-After, client request volume |
| 500 | Server-side exception | Application logs, trace ID |
| 502/504 | Upstream failure/timeout | Proxy and upstream health |
| Browser CORS error | Cross-origin policy | Origin and preflight response |
| Timeout | Network/server latency | DNS, connectivity, service health |

### Interview questions

**Q: What is an API?**  
A: A defined interface through which software components exchange data or invoke functionality.

**Q: REST vs HTTP?**  
A: REST is an architectural style; HTTP is a communication protocol often used to implement REST APIs.

**Q: PUT vs PATCH?**  
A: PUT typically replaces a resource representation; PATCH applies partial changes.

**Q: 401 vs 403?**  
A: 401 means valid authentication is missing; 403 means the request is not permitted.

**Q: Authentication vs authorization?**  
A: Authentication verifies identity; authorization determines access rights.

**Q: Why use an API gateway?**  
A: Centralized routing, authentication integration, request policies, throttling, and observability.

**Q: What is idempotency?**  
A: Repeating the same operation produces the same intended server-side effect as doing it once.

**Q: What is the NRF in 5G?**  
A: The Network Repository Function supports registration and discovery of 5G network functions and their services.

**Q: What does CAMARA standardize?**  
A: Developer-facing APIs that expose telecommunications network capabilities consistently across operators.

### Quick revision checklist

- [ ] Explain client-server request/response flow
- [ ] Distinguish REST, SOAP, and GraphQL
- [ ] Recognize CRUD methods and common status codes
- [ ] Read JSON and HTTP headers
- [ ] Test APIs using cURL, Python, and Postman
- [ ] Explain OAuth 2.0, JWT, API keys, RBAC, and ABAC
- [ ] Explain rate limiting, throttling, and retries
- [ ] Read OpenAPI YAML and Swagger UI
- [ ] Explain microservices and API gateways
- [ ] Describe 5G SBA, SBI, NRF, AMF, and SMF
- [ ] Explain CAMARA, GSMA Open Gateway, and TM Forum

## 21. Further Reading

- [MDN: HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [MDN: HTTP Response Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Learning Center](https://learning.postman.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [OWASP API Security Top 10](https://owasp.org/API-Security/)
- [3GPP 5G API Repository](https://forge.3gpp.org/rep/all/5G_APIs)
- [CAMARA Project](https://camaraproject.org/)

---

**Acknowledgment:** These are independently organized study notes informed by the topics in Lablabee's *Utilizing APIs* training reference, expanded with examples and technical clarifications. They are not an official Lablabee publication.
