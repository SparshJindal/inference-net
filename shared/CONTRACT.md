# Integration Contract (FROZEN)

Everyone codes to these shapes. Change only by agreement.

## A. Client <-> Backend (REST/JSON over HTTPS)

POST /jobs
  body: { "model": "llama3.2:3b", "prompt": "Explain gravity." }   # model optional
  200:  { "job_id": "a1b2c3" }

GET /jobs/{job_id}
  200:  { "status": "pending|running|done|error",
          "result": "...", "served_by": "colab-gpu-1",
          "device": "gpu", "tokens": 128, "latency_ms": 940 }

GET /nodes
  200:  [ { "node_id": "colab-gpu-1", "status": "idle|busy",
            "device": "gpu|cpu", "models": ["llama3.2:3b"] } ]

GET /metrics
  200:  { "jobs_served": 42, "nodes_online": 3,
          "avg_latency_ms": 880, "p95_latency_ms": 1600,
          "tokens_per_sec": { "gpu": 55.0, "cpu": 8.0 } }

Frontend flow: POST /jobs -> get job_id -> poll GET /jobs/{job_id} every ~500ms.

## B. Node <-> Backend (WebSocket /ws/node)

Node -> Server:
  { "type": "register", "node_id": "...", "capabilities": { "device": "gpu", "vram_gb": 16, "models": ["llama3.2:3b"] } }
  { "type": "heartbeat", "node_id": "..." }
  { "type": "job_result", "node_id": "...", "job_id": "...", "result": "...", "tokens": 128, "latency_ms": 940 }
  { "type": "job_error",  "node_id": "...", "job_id": "...", "error": "..." }

Server -> Node:
  { "type": "registered", "node_id": "..." }
  { "type": "job_assign", "job_id": "...", "model": "llama3.2:3b", "prompt": "..." }

## C. Environment variables
NEXT_PUBLIC_API_URL  (frontend) -> https://<backend>.up.railway.app
REDIS_URL            (backend)  -> rediss://...upstash.io:6379
SERVER_WS            (node)     -> wss://<backend>.up.railway.app/ws/node
DEVICE / MODEL       (node)     -> gpu / llama3.2:3b
