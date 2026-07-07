# Inference Net

A distributed AI inference network for a 24-hour hackathon.

## Architecture

The project consists of three main components:

1. **Frontend (Next.js)**: Dashboard for submitting jobs and viewing results. Deployed to Vercel.
2. **Backend (FastAPI)**: Central scheduler that receives jobs, assigns them to nodes via WebSocket, and tracks metrics. Deployed to Railway. Uses Upstash Redis for state management.
3. **Node (Python)**: Clients running on Colab or local laptops. Connect to the backend via WebSocket to receive and process inference jobs.

## Deployment Topology
- **Frontend**: Vercel
- **Backend**: Railway
- **Database (Redis)**: Upstash
- **Nodes**: Google Colab / Laptops
