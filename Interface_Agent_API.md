# Coral Protocol Interface Agent API Guide

This guide documents the API calls for working with a single interface agent in Coral Protocol.

## Session Creation

Create a new session with an interface agent.

```http
POST http://localhost:5555/sessions
```

### Request Body
```json
{
    "sessionId": "test-session",
    "applicationId": "app",
    "privacyKey": "priv",
    "agentGraph": {
        "agents": {
            "interface-agent": {
                "type": "local",
                "agentType": "interface",
                "options": {
                    "OPENAI_API_KEY": "your-api-key"
                }
            }
        },
        "links": []
    }
}
```

### Response
```json
{
    "sessionId": "878b4fae-6721-472d-a412-496791039abc",
    "applicationId": "app",
    "privacyKey": "priv"
}
```

## Agent Connection

Connect the interface agent to the session using Server-Sent Events (SSE).

```http
GET http://localhost:5555/devmode/app/priv/878b4fae-6721-472d-a412-496791039abc/sse?agentId=interface-agent
```

This establishes a persistent SSE connection for real-time communication with the agent.

## Thread Creation

Create a new thread for the interface agent.

```http
POST http://localhost:5555/debug/app/priv/878b4fae-6721-472d-a412-496791039abc/interface-agent/thread/
```

### Request Body
```json
{
    "threadName": "Interface Thread",
    "participantIds": ["interface-agent"]
}
```

### Response
```json
{
    "id": "dd21406f-fae4-4d64-bc1a-8e7304f2d5d7",
    "name": "Interface Thread",
    "creatorId": "interface-agent",
    "participants": [
        "interface-agent"
    ],
    "messages": [],
    "isClosed": false,
    "summary": null
}
```

## Sending Messages

Send a message in the thread.

```http
POST http://localhost:5555/debug/app/priv/878b4fae-6721-472d-a412-496791039abc/interface-agent/thread/sendMessage/
```

### Request Body
```json
{
    "threadId": "dd21406f-fae4-4d64-bc1a-8e7304f2d5d7",
    "content": "Hello, this is a test message from the interface agent",
    "mentions": []
}
```

### Response
```json
{
    "id": "0a4ab068-0ae7-4e63-9f82-5efd53e169ed",
    "threadName": "Interface Thread",
    "threadId": "dd21406f-fae4-4d64-bc1a-8e7304f2d5d7",
    "senderId": "interface-agent",
    "content": "Hello, this is a test message from the interface agent",
    "timestamp": 1749747141672,
    "mentions": []
}
```

## Session Monitoring

Monitor the session using WebSocket connection.

```http
ws://localhost:5555/debug/app/priv/878b4fae-6721-472d-a412-496791039abc/
```

This WebSocket connection provides real-time updates about:
- Agent registration
- Thread creation and updates
- Message events
- Session state changes

### Example WebSocket Response
```json
{
    "type": "AgentList",
    "agents": [
        {
            "id": "interface-agent",
            "description": "interface-agent"
        }
    ]
}
```

```json
{
    "type": "ThreadList",
    "threads": [
        {
            "id": "dd21406f-fae4-4d64-bc1a-8e7304f2d5d7",
            "name": "Interface Thread",
            "creatorId": "interface-agent",
            "participants": [
                "interface-agent"
            ],
            "messages": [
                {
                    "id": "0a4ab068-0ae7-4e63-9f82-5efd53e169ed",
                    "content": "Hello, this is a test message from the interface agent",
                    "timestamp": 1749747141672
                }
            ]
        }
    ]
}
```

## Notes
- All endpoints use the base URL: `http://localhost:5555`
- The session ID, application ID, and privacy key are required in most endpoints
- The interface agent ID is used in the endpoints where agent-specific actions are performed
- WebSocket connection is useful for real-time monitoring of the session state
