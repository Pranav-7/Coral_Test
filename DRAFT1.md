# Building a Multi-Agent System with Coral Protocol

## Table of Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [System Architecture](#system-architecture)
- [Setup Guide](#setup-guide)
  - [1. Repository Setup](#1-repository-setup)
  - [2. Backend Server Configuration](#2-backend-server-configuration)
  - [3. Session Management](#3-session-management)
  - [4. Agent Connection](#4-agent-connection)
  - [5. Thread Creation](#5-thread-creation)
  - [6. Message Handling](#6-message-handling)
  - [7. Coral Debugger Setup](#7-coral-debugger-setup)
- [Troubleshooting](#troubleshooting)

## Overview

Coral Protocol enables the composition and execution of intelligent agents in a connected system. This guide walks you through setting up a local multi-agent system with three key components:
- Coral-Interface-Agent
- Human-In-Loop-Agent
- Coral-OpenDeepResearch-Agent

The system includes a backend server (`coral-server`) and tools for orchestrating, debugging, and extending agent-based interactions.

> 📚 **Learn More**: [What is Coral Protocol?](https://docs.coralprotocol.org/CoralDoc/Introduction/WhatisCoralProtocol)

## Prerequisites

### Required Software
| Tool           | Version | Installation Link |
|----------------|---------|------------------|
| Node.js        | ≥ 18    | [Download](https://nodejs.org/) |
| Python         | ≥ 3.10  | [Download](https://www.python.org/downloads/) |
| Java (JDK)     | 24      | [Download](https://jdk.java.net/24/) |
| Postman        | Any     | [Download](https://www.postman.com/downloads/) |

### Required API Keys
- [OpenAI API Key](https://platform.openai.com/account/api-keys)
- [LINKUP API Key](https://linkup.ai)

## System Architecture

The system consists of three main agents that work together:
1. **Coral-Interface-Agent**: Handles user interface interactions
2. **Human-In-Loop-Agent**: Manages human interaction points
3. **Coral-OpenDeepResearch-Agent**: Performs research and analysis

## Setup Guide

### 1. Repository Setup

<details>
<summary>📁 Clone Required Repositories</summary>

```bash
# Create project directory
mkdir coral-project
cd coral-project

# Clone agent repositories
git clone https://github.com/Coral-Protocol/Coral-Interface-Agent.git
git clone https://github.com/Coral-Protocol/Human-In-Loop-Agent.git
git clone https://github.com/Coral-Protocol/Coral-OpenDeepResearch-Agent.git

# Clone backend and debugger
git clone https://github.com/Coral-Protocol/coral-server.git
git clone https://github.com/Coral-Protocol/coral-dbg.git
```
</details>

### 2. Backend Server Configuration

<details>
<summary>⚙️ Configure and Start Server</summary>

1. Navigate to server directory:
```bash
cd coral-server
```

2. Update `src/main/resources/application.yaml` with the following configuration:
```yaml
registry:
  coral-interface:
    options:
      - name: "OPENAI_API_KEY"
        type: "string"
        description: "OpenAI API Key for LangChain Interface Agent"
    runtime:
      type: "executable"
      command: [ "bash", "-c", "cd ../Coral-Interface-Agent && uv sync && uv run python 0-langchain-interface.py" ]
      environment:
        - name: "OPENAI_API_KEY"
          from: "OPENAI_API_KEY"

  coral-human:
    options:
      - name: "OPENAI_API_KEY"
        type: "string"
        description: "OpenAI API Key for Human Agent"
    runtime:
      type: "executable"
      command: [ "bash", "-c", "cd ../Human-In-Loop-Agent && uv sync && uv run python main.py" ]
      environment:
        - name: "OPENAI_API_KEY"
          from: "OPENAI_API_KEY"

  coral-research:
    options:
      - name: "OPENAI_API_KEY"
        type: "string"
        description: "OpenAI API Key for Research Agent"
      - name: "LINKUP_API_KEY"
        type: "string"
        description: "LINKUP API KEY for Research Agent"
    runtime:
      type: "executable"
      command: [ "bash", "-c", "cd ../Coral-OpenDeepResearch-Agent && uv sync && uv run python langchain_open_deep_research.py" ]
      environment:
        - name: "OPENAI_API_KEY"
          from: "OPENAI_API_KEY"
        - name: "LINKUP_API_KEY"
          from: "LINKUP_API_KEY"
```

3. Start the server:
```bash
./gradlew run
```

> ⚠️ **Important**: Keep the server terminal open and use a new terminal for subsequent steps.
</details>

### 3. Session Management

<details>
<summary>📡 Create a New Session</summary>

Use Postman to create a new session:

**Request Details:**
- Method: `POST`
- URL: `http://localhost:5555/sessions`
- Body:
```json
{
  "sessionId": "test-session",
  "applicationId": "app",
  "privacyKey": "priv",
  "agentGraph": {
    "agents": {
      "my-human": {
        "type": "local",
        "agentType": "coral-human",
        "options": {
          "OPENAI_API_KEY": "YOUR_OPENAI_API_KEY"
        }
      },
      "my-deepresearch": {
        "type": "local",
        "agentType": "coral-research",
        "options": {
          "OPENAI_API_KEY": "YOUR_OPENAI_API_KEY",
          "LINKUP_API_KEY": "YOUR_LINKUP_API_KEY"
        }
      },
      "my-interface": {
        "type": "local",
        "agentType": "coral-interface",
        "options": {
          "OPENAI_API_KEY": "YOUR_OPENAI_API_KEY"
        }
      }
    },
    "links": [
      ["my-human", "my-interface", "my-deepresearch"]
    ]
  }
}
```

> 📝 **Note**: Save the `sessionId` (`test-session`) for future requests.
</details>

### 4. Agent Connection

<details>
<summary>🔌 Connect Agents to Session</summary>

Open three separate Postman tabs and run these GET requests:

```http
GET http://127.0.0.1:5555/devmode/app/priv/test-session/sse?agentId=my-interface
GET http://127.0.0.1:5555/devmode/app/priv/test-session/sse?agentId=my-human
GET http://127.0.0.1:5555/devmode/app/priv/test-session/sse?agentId=my-deepresearch
```
</details>

### 5. Thread Creation

<details>
<summary>🧵 Create a New Thread</summary>

**Request Details:**
- Method: `POST`
- URL: `http://127.0.0.1:5555/debug/app/priv/test-session/my-interface/thread/`
- Body:
```json
{
  "threadName": "Test Thread 1",
  "participantIds": ["my-interface", "my-human", "my-deepresearch"]
}
```

> 📝 **Note**: Save the returned `threadId` for future use.
</details>

### 6. Message Handling

<details>
<summary>✉️ Send Messages</summary>

**Request Details:**
- Method: `POST`
- URL: `http://127.0.0.1:5555/debug/app/priv/test-session/my-interface/thread/sendMessage/`
- Body: (Add your message content here)
</details>

### 7. Coral Debugger Setup

<details>
<summary>🖥️ Launch Debugger UI</summary>

1. Open a new terminal and navigate to the debugger directory:
```bash
cd ../coral-dbg
```

2. Install dependencies and start the development server:
```bash
npm install -g yarn
yarn install
yarn dev
```

3. Access the debugger UI:
- Open [http://localhost:5173](http://localhost:5173) in your browser
- Connect using these credentials:
  - Server URL: `http://127.0.0.1:5555`
  - App ID: `app`
  - Privacy Key: `priv`
  - Session ID: `test-session`

> 💡 **Tip**: The debugger UI provides real-time visualization of agent interactions, threads, and messages.
</details>

## Troubleshooting

Common issues and solutions will be added here based on user feedback and experience.

---

> 💡 **Tip**: For the best experience, ensure all API keys are properly configured and the server is running before attempting to create sessions or send messages.
