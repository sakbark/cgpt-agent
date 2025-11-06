# Calendar GPT Interface Agent (cgpt)

**A Claude Code agent that serves as your primary interface to Calendar GPT - a universal proxy for 14+ integrated services.**

## Overview

The `cgpt` agent is designed to be your intelligent interface to Calendar GPT, providing seamless access to:
- **14 integrated services** (Google Calendar, Gmail, Sheets, Drive, Todoist, Airtable, and more)
- **300+ REST API operations**
- **280+ MCP tool operations**
- **Multi-account support** (personal + work)
- **Persistent memory** across all sessions
- **Rules-based automation** with dynamic service discovery

## Key Features

### 🔌 MCP-First Architecture
- **Primary**: Model Context Protocol (MCP) integration with Claude Code
- **Backup**: HTTP REST endpoints for direct access
- **Seamless**: Natural language → MCP tools → Calendar GPT → Services

### 🧠 Persistent Memory System
- Airtable-based memory storage
- User preferences, learned patterns, conversation context
- Cross-session memory retention

### 📋 Rules-Driven Operations
- 48+ operational rules in Airtable
- Dynamic service discovery
- Automatic rule checking before every action

### 🔐 Server-Side Authentication
- All credentials in Google Secret Manager
- No API keys needed from client
- Automatic OAuth token refresh

## Installation

### 1. Install the Agent

Copy `cgpt.md` to your Claude Code agents directory:

```bash
mkdir -p ~/.claude/agents
cp cgpt.md ~/.claude/agents/
```

### 2. Configure MCP Server (Optional but Recommended)

The agent works best with the MCP server configured in Claude Code:

**File**: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "calendar-gpt": {
      "command": "/path/to/google-services-proxy/venv-mcp/bin/python",
      "args": ["/path/to/google-services-proxy/mcp_server.py"],
      "env": {
        "GOOGLE_CLOUD_PROJECT": "your-project-id"
      }
    }
  }
}
```

**Note**: You'll need the [Google Services Proxy](https://github.com/saadyk/google-services-proxy) deployed to use this agent.

### 3. Restart Claude Code

After installing the agent and configuring MCP, restart Claude Code to load the configuration.

## Usage

### Invoking the Agent

Use the `@cgpt` mention in Claude Code to invoke the agent:

```
@cgpt what's on my calendar today?
@cgpt search my email for messages from john@example.com
@cgpt add a task to my Todoist: "Review MCP documentation"
@cgpt check my Airtable Services table
```

### Example Queries

**Calendar Operations**:
```
@cgpt show me my schedule for next week
@cgpt create a calendar event tomorrow at 2pm
@cgpt find all meetings with "standup" in the title
```

**Todoist Tasks**:
```
@cgpt what tasks are due today?
@cgpt create a task: "Finish quarterly report" due Friday
@cgpt show me all tasks in my Work Projects
```

**Gmail**:
```
@cgpt search for emails from sarah@company.com
@cgpt find unread messages in my inbox
@cgpt show me emails with attachments from this week
```

**Airtable**:
```
@cgpt show me the Rules table
@cgpt query the Services table for all active services
@cgpt update the Agent Memory with this preference
```

## Architecture

### Three-Layer MCP Integration

```
Claude Code (Mac)
    ↓ stdio (JSON-RPC)
Local MCP Server (mcp_server.py)
    ↓ HTTPS
Cloud Run (calendar-gpt)
    ├─ /mcp/execute (14 services, 280+ operations)
    └─ 300+ REST API endpoints
```

### Available Services

1. **Google Calendar** - 51 calendars (personal + work)
2. **Gmail** - 64 operations
3. **Google Sheets** - 18 operations
4. **Google Docs** - 3 operations
5. **Google Slides** - 5 operations
6. **Google Drive** - 44 operations
7. **Google Chat** - 30 operations
8. **Todoist** - Task management (personal only)
9. **Airtable** - Database operations
10. **Xero** - Accounting integration
11. **Pushover** - Notifications
12. **Google Maps** - Location services
13. **Google Custom Search** - Web search
14. **Make.com** - Webhook automations

### Multi-Account Support

The agent supports both personal and work Google accounts:
- **Personal**: `saad@sakbark.com` (default)
- **Work**: `saad@passthekeys.co.uk`

Specify `"account": "work"` in requests to use the work account.

## Agent Capabilities

### 🎯 Primary Purpose

The agent is an **INTERFACE TO CALENDAR GPT**. It:
- Accesses ALL services EXCLUSIVELY through Calendar GPT proxy
- Never uses direct API calls - Calendar GPT is the ONLY gateway
- Manages and adjusts Calendar GPT as needed (full permissions)
- Dynamically checks Services table to discover available services
- Follows Rules table for operational procedures
- Updates tables when learning new patterns

### 📊 Startup Sequence

The agent automatically runs this sequence at the start of every session:

1. **Health Check**: Verify Calendar GPT connectivity
2. **Load Rules Table**: 48+ operational rules from Airtable
3. **Load Services Table**: Dynamic service catalog (CHECKED REGULARLY)
4. **Load Automations Table**: Active automation definitions
5. **Load Memory Table**: Persistent memory and context

### 🔄 Dynamic Service Discovery

The Services table is **DYNAMIC** - it changes as services are added or updated. The agent:
- Checks the table regularly during operation
- Discovers new services automatically
- Adapts to configuration changes
- Updates documentation as needed

## Memory System

### Airtable-Based Persistent Memory

**Table**: Agent Memory & Context (`tbl6SnaymH59sJGeF`)

**Pre-Loaded Memories**:
- MEM-001: Calendar accounts (always query both)
- MEM-002: Todoist cache policy (never cached)
- MEM-003: Todoist account (personal only)
- MEM-004: Email-to-Todoist automation
- MEM-005: User profile data
- MEM-006: Calendar event transparency
- MEM-007: Flight tracking enabled
- MEM-008: Proxy preference (Calendar GPT only)

### Memory Types

- **User Preferences**: How you want things done
- **Learned Patterns**: Observed behaviors over time
- **Conversation Context**: Session info and history
- **User Data**: Profile, accounts, frequently used IDs
- **Integration State**: Service configurations and states

### Memory Hierarchy

```
Memory Table > Rules Table > Services Table > Default Behavior
```

The agent checks memory FIRST before making assumptions.

## Authentication

**ALL AUTHENTICATION IS MANAGED BY GOOGLE SECRET MANAGER**

- You do NOT need credentials
- You do NOT provide API keys
- You do NOT handle OAuth tokens
- Calendar GPT handles EVERYTHING automatically
- Just call the proxy - auth is automatic

## Testing

All services have been tested and verified working:

- ✅ Calendar (personal + work)
- ✅ Gmail
- ✅ Google Sheets
- ✅ Google Drive
- ✅ Todoist
- ✅ Airtable
- ✅ Pushover
- ✅ Health endpoint

### Test Commands

```bash
# Health check
curl -s https://calendar-gpt-958443682078.us-central1.run.app/health

# Calendar events
curl -X POST https://calendar-gpt-958443682078.us-central1.run.app/calendar/events/list \
  -H "Content-Type: application/json" \
  -d '{"body":{"calendarId":"primary","maxResults":10},"account":"personal"}'

# Todoist tasks
curl -X POST https://calendar-gpt-958443682078.us-central1.run.app/todoist/tasks/list \
  -H "Content-Type: application/json" \
  -d '{"body":{},"account":"personal"}'
```

## Requirements

- **Claude Code**: Latest version
- **Calendar GPT Proxy**: Deployed to Cloud Run
- **MCP Server** (optional): For MCP integration
- **Airtable Access**: For rules and memory tables

## Related Projects

- [Google Services Proxy](https://github.com/saadyk/google-services-proxy) - The Calendar GPT backend
- [MCP Integration Docs](https://calendar-gpt-958443682078.us-central1.run.app/docs) - API documentation

## Status

- **Version**: 1.0.0
- **Last Updated**: November 6, 2025
- **Agent File Size**: 38KB (971 lines)
- **MCP Integration**: ✅ Fully Operational
- **Services Tested**: ✅ All 14 services verified

## License

MIT License

## Support

For issues or questions:
- Check the agent file documentation in `cgpt.md`
- Review the Calendar GPT API docs
- Consult the Airtable Rules table for operational guidelines

---

**Built with Claude Code** | **Powered by Calendar GPT** | **MCP-Enabled**
