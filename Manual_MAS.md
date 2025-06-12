# Building Multi-Agent Systems with Coral Protocol

## Overview
This guide will walk you through setting up and running a multi-agent system using Coral Protocol. The system consists of three main components:
- Coral Server (central communication hub)
- Interface Agent (coordinator)
- Human-in-Loop Agent (interactive content provider)
- OpenDeepResearch Agent (research and validation)

## Prerequisites
- Git
- Python 3.x
- Java Development Kit (JDK)
- OpenAI API key
- Linkup API key (for OpenDeepResearch Agent)

## System Architecture
```
Coral Server (Central Hub)
       ↑
       |
Interface Agent (Coordinator)
       ↑
       |
Human-in-Loop Agent ←→ OpenDeepResearch Agent
```

## Installation and Setup

### 1. Coral Server Setup
```bash
# Clone the repository
git clone https://github.com/Coral-Protocol/coral-server.git

# Navigate to the directory
cd coral-server

# Run the server
./gradlew run

# To stop the server
# Press Ctrl + C
# When prompted: Terminate batch job (Y/N)? y
```

### 2. Interface Agent Setup
```bash
# Clone the repository (from root directory)
git clone https://github.com/Coral-Protocol/Coral-Interface-Agent.git

# Navigate to the directory
cd Coral-Interface-Agent

# Install uv package manager
pip install uv

# Sync dependencies
uv sync

# Set OpenAI API key
$env:OPENAI_API_KEY="your-api-key-here"

# Run the agent
uv run python 0-langchain-interface.py
```

### 3. Human-in-Loop Agent Setup
```bash
# Clone the repository (from root directory)
git clone https://github.com/Coral-Protocol/Human-In-Loop-Agent

# Navigate to the directory
cd Human-In-Loop-Agent

# Install uv package manager
pip install uv

# Sync dependencies
uv sync

# Set OpenAI API key
$env:OPENAI_API_KEY="your-api-key-here"

# Run the agent
uv run python main.py
```

### 4. OpenDeepResearch Agent Setup
```bash
# Clone the repository (from root directory)
git clone https://github.com/Coral-Protocol/Coral-OpenDeepResearch-Agent.git

# Navigate to the directory
cd Coral-OpenDeepResearch-Agent

# Install uv package manager
pip install uv

# Sync dependencies
uv sync

# Set API keys
$env:OPENAI_API_KEY="your-api-key-here"
$env:LINKUP_API_KEY="your-linkup-api-key-here"

# Run the agent
uv run python langchain_open_deep_research.py
```

## Running the System

### Step 1: Start the Components
1. Start Coral Server first
2. Start Interface Agent
3. Start Human-in-Loop Agent
4. Start OpenDeepResearch Agent

### Step 2: Interact with the System
Once all components are running, you can interact with the system through the Interface Agent. Here's an example prompt:

```
"Please have the Human-in-Loop agent share a fact about artificial intelligence, and then have the OpenDeepResearch agent research and validate this fact."
```

## How It Works

### Communication Flow
1. User sends a request to the Interface Agent
2. Interface Agent creates a thread and coordinates between agents
3. Human-in-Loop Agent provides initial content
4. OpenDeepResearch Agent validates and expands the content
5. Interface Agent presents the final result to the user

### Agent Roles
- **Interface Agent**: Central coordinator that manages communication between agents
- **Human-in-Loop Agent**: Provides engaging content (facts, quotes, jokes)
- **OpenDeepResearch Agent**: Validates and expands content with detailed research

## Best Practices
1. Always start Coral Server first
2. Ensure all API keys are properly set
3. Keep all agents running simultaneously
4. Use clear, specific prompts for better results
5. Monitor the terminal outputs for any errors

## Troubleshooting
- If an agent fails to start, check API keys and dependencies
- If communication fails, ensure Coral Server is running
- If an agent doesn't respond, check if it's properly connected to the server

## Example Use Cases
1. Fact validation and research
2. Content generation and verification
3. Multi-step information gathering
4. Interactive learning and exploration
