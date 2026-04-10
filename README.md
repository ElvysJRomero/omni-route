# Omni-Route: Autonomous LLM API Proxy

## The Mission
Omni-Route is an asynchronous proxy designed to dynamically route prompts to the optimal Large Language Model (e.g., OpenAI's GPT-4o, Anthropic's Claude 3 Haiku, Meta's Llama 3 8B). The routing engine makes execution decisions based on a tier-specific, real-time Epsilon-Greedy Arbitrage Metric (`Cost * Latency`).

## Current State: v0.1 (Infrastructure Baseline)
The current iteration establishes the localized, persistent infrastructure required to capture high-velocity routing telemetry.

### The Architecture
* **Containerization:** Docker & Docker Compose
* **State Management:** PostgreSQL (Local Host Bind Mount for data persistence across container lifecycles)
* **Telemetry Ledger:** Optimized schema for high-speed asynchronous writes and reads.

## Quickstart (Local Development)

1. **Clone the repository:**
   ```bash
   git clone https://github.com/ElvysJRomero/omni-route
   cd omni-route
   ```

2. **Configure environment:**
   Copy the environment template and populate your secure database credentials:
   ```bash
   cp .env.example .env
   ```

3. **Spin up the ledger:**
   ```bash
   docker compose up -d
   ```
   **Note: Postgres is exposed on `localhost:5432`*

4. **Initialize the schema:**
   Connect to the database via your preferred SQL client (`localhost:5432`) and execute the command in the **Telemetry Table** section below to create the `routing_telemetry` table.  

## Telemetry Schema
The engine relies on the `routing_telemetry` table to calculate the Epsilon-Greedy routing matrix:

```sql
CREATE TABLE routing_telemetry (
    id SERIAL PRIMARY KEY,
    race_id UUID NOT NULL,
    tier VARCHAR(50) NOT NULL,
    optimization_goal VARCHAR(50) NOT NULL,
    model_name VARCHAR(50) NOT NULL,
    ms_latency DECIMAL(8, 2) NOT NULL,
    input_token_cost DECIMAL(10, 6) NOT NULL,
    output_token_cost DECIMAL(10, 6) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_routing_query ON routing_telemetry (tier, optimization_goal, created_at DESC);
```
