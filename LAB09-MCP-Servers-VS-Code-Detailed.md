# LAB 09 - Install and Use MCP Servers in VS Code

## Description
Learn how to install, configure, and use Model Context Protocol (MCP) servers with GitHub Copilot in Visual Studio Code to enhance AI-assisted development workflows.

**Estimated Duration**: 45 minutes

---

## Learning Objectives

By the end of this exercise, you will be able to:
- Understand what MCP (Model Context Protocol) is and why it matters
- Install and configure MCP servers in VS Code
- Use pre-built MCP servers from the official registry
- Integrate MCP servers with GitHub Copilot
- Create custom MCP server configurations
- Manage and troubleshoot MCP servers
- Build your first custom MCP server

---

## What is Model Context Protocol (MCP)?

### Overview

Model Context Protocol (MCP) is an open protocol that standardizes how AI applications interact with external data sources and tools. Think of it as a universal translator between AI models and your development environment.

### Key Components

1. **MCP Client**: The AI application (GitHub Copilot in VS Code)
2. **MCP Protocol**: The standardized communication layer
3. **MCP Server**: The tool or service that provides functionality

### Why MCP Matters

Without MCP:
- AI assistants are limited to their training data
- Cannot access real-time information
- Cannot interact with external tools
- Each integration requires custom implementation

With MCP:
- AI can access live data from APIs, databases, files
- Can trigger actions (create issues, send emails, run scripts)
- Standardized protocol works across tools
- Growing ecosystem of pre-built servers

---

## Prerequisites

### Required Software

- Visual Studio Code (latest version or Insiders)
- GitHub Copilot subscription (Free, Pro, Business, or Enterprise)
- Node.js 18+ and NPM (for Node-based MCP servers)
- Python 3.10+ and pip (for Python-based MCP servers)
- Git 2.48+

### Verify Prerequisites

```bash
# Check VS Code version
code --version

# Check Node.js and NPM
node --version
npm --version

# Check Python and pip
python --version
pip --version

# Check Git
git --version
```

---

## Part 1: Understanding MCP Architecture

### How MCP Works

```
┌─────────────────┐
│   VS Code       │
│  GitHub Copilot │ <-- MCP Client
└────────┬────────┘
         │
         │ MCP Protocol
         │ (JSON-RPC)
         │
    ┌────┴────────────────────┐
    │                         │
┌───┴────┐              ┌────┴────┐
│  MCP   │              │   MCP   │
│ Server │              │ Server  │
│  #1    │              │   #2    │
└───┬────┘              └────┬────┘
    │                        │
    │                        │
┌───┴────┐              ┌────┴────┐
│ GitHub │              │  File   │
│  API   │              │ System  │
└────────┘              └─────────┘
```

### MCP Server Types

1. **HTTP Servers**: Connect to remote APIs
2. **Local Servers**: Run on your machine (Node.js, Python, etc.)
3. **Docker Servers**: Run in containers

---

## Part 2: Enable MCP in VS Code

### Step 1: Verify GitHub Copilot is Active

**Action**:
1. Open VS Code
2. Check the bottom-right corner for the GitHub Copilot icon
3. If not visible, install GitHub Copilot extension:

```bash
code --install-extension GitHub.copilot
code --install-extension GitHub.copilot-chat
```

### Step 2: Enable Agent Mode (Required for MCP)

**Action**:
1. Open GitHub Copilot Chat (Ctrl+Shift+I or Cmd+Shift+I)
2. Click on the Copilot icon at the top
3. Look for "Agent Mode" toggle
4. Enable "Agent Mode"

**Alternative Method**:
1. Press Ctrl+Shift+P (Cmd+Shift+P on Mac)
2. Type: "GitHub Copilot: Enable Agent Mode"
3. Press Enter

### Step 3: Access MCP Settings

**Query 1**: Open MCP configuration

**Action**:
1. Press Ctrl+Shift+P (Cmd+Shift+P on Mac)
2. Type: "MCP: Show Configuration"
3. Press Enter

This opens your MCP configuration file (mcp.json).

**Expected Result**: A JSON file opens, either empty or with default configuration:

```json
{
  "servers": {}
}
```

---

## Part 3: Install MCP Servers from Registry

### Step 1: Browse Official MCP Registry

**Query 2**: Access MCP server registry

**Action**:
1. Open browser and navigate to: https://code.visualstudio.com/mcp
2. Or in VS Code, press Ctrl+Shift+P and type: "MCP: Browse Servers"

**Expected Result**: You see a list of available MCP servers categorized by functionality:
- Development Tools (GitHub, GitLab, Jira)
- File Systems (Local files, Google Drive, Dropbox)
- Databases (PostgreSQL, MongoDB, Redis)
- Utilities (Time, Weather, Web Search)
- And many more

### Step 2: Install GitHub MCP Server

**Query 3**: Install GitHub MCP server for repository access

**Action**:
1. In the MCP registry, find "GitHub MCP Server"
2. Click "Install"
3. Choose "User Profile" (to use across all projects) or "Workspace" (current project only)

**Expected Configuration**: Your mcp.json is updated automatically:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

**Query 4**: Authenticate GitHub MCP server

**Action**:
1. A popup appears: "MCP Server Definition wants to authenticate to GitHub"
2. Click "OK"
3. Browser opens for GitHub authorization
4. Click "Authorize" to grant permissions

**Expected Result**: GitHub MCP server is now active and can access your repositories.

### Step 3: Install Time MCP Server

**Query 5**: Install Time MCP server for timezone operations

**Action**:

First, install the Time MCP server globally:

```bash
pip install mcp-server-time
```

**Expected Output**:
```
Successfully installed mcp-server-time-0.1.0
```

**Query 6**: Add Time server to configuration

**Action**:
1. Open MCP configuration (Ctrl+Shift+P > "MCP: Show Configuration")
2. Add the Time server configuration:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "time": {
      "command": "python",
      "args": ["-m", "mcp_server_time"],
      "env": {
        "TIMEZONE": "America/New_York"
      }
    }
  }
}
```

3. Save the file
4. VS Code prompts: "Trust this MCP server?"
5. Click "Yes, I trust this server"

**Expected Result**: Time MCP server starts automatically.

### Step 4: Install Filesystem MCP Server

**Query 7**: Install Filesystem MCP server for local file operations

**Action**:

Install the Filesystem MCP server:

```bash
npm install -g @modelcontextprotocol/server-filesystem
```

**Expected Output**:
```
added 45 packages in 3s
```

**Query 8**: Add Filesystem server to configuration

**Action**: Update mcp.json:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "time": {
      "command": "python",
      "args": ["-m", "mcp_server_time"],
      "env": {
        "TIMEZONE": "America/New_York"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/yourname/projects"
      ]
    }
  }
}
```

Replace `/Users/yourname/projects` with your actual project directory.

**Security Note**: Only allow access to directories you trust. The filesystem server can read/write files in specified directories.

---

## Part 4: Using MCP Servers with GitHub Copilot

### Step 1: Verify MCP Servers are Running

**Query 9**: Check MCP server status

**Action**:
1. Press Ctrl+Shift+P (Cmd+Shift+P on Mac)
2. Type: "MCP: Show Server Status"
3. Press Enter

**Expected Result**:

```
MCP Servers Status:
- github: Running (Connected)
- time: Running (PID: 12345)
- filesystem: Running (PID: 12346)
```

### Step 2: Use Time MCP Server

**Query 10**: Test Time MCP server with Copilot Chat

**Action**:
1. Open GitHub Copilot Chat (Ctrl+Shift+I)
2. Type: "What time is it in Tokyo right now?"
3. Press Enter

**Expected Response**:

```
Using the Time MCP server, I can tell you that the current time in Tokyo (Asia/Tokyo timezone) is:

March 4, 2026, 9:45 PM JST

Tokyo is 13 hours ahead of New York (Eastern Time).
```

**Query 11**: Convert timezones

**Action**: In Copilot Chat, type:

```
Convert 2:00 PM EST to PST, London time, and Tokyo time
```

**Expected Response**:

```
Converting 2:00 PM EST to other timezones:

- PST (Pacific): 11:00 AM
- London (GMT): 7:00 PM
- Tokyo (JST): 4:00 AM (next day)

[Source: Time MCP Server]
```

### Step 3: Use GitHub MCP Server

**Query 12**: Search GitHub issues

**Action**: In Copilot Chat, type:

```
@agent Find open issues in this repository labeled as "bug"
```

**Expected Response**:

```
Found 3 open issues with label "bug":

1. Issue #45: "Login button not responding on mobile"
   Created: 3 days ago
   Author: user123
   Link: https://github.com/yourrepo/issues/45

2. Issue #38: "Database connection timeout"
   Created: 1 week ago
   Author: dev456
   Link: https://github.com/yourrepo/issues/38

3. Issue #12: "CSS layout broken in Firefox"
   Created: 2 weeks ago
   Author: designer789
   Link: https://github.com/yourrepo/issues/12

[Source: GitHub MCP Server]
```

**Query 13**: Get pull request information

**Action**: In Copilot Chat, type:

```
@agent Show me the most recent pull requests in this repository
```

**Expected Response**:

```
Recent pull requests:

1. PR #102: "Add user authentication feature"
   Status: Open
   Author: dev123
   Created: 2 hours ago
   Reviews: 1 approval, 0 changes requested

2. PR #101: "Fix responsive design issues"
   Status: Merged
   Author: designer456
   Merged: 1 day ago

3. PR #100: "Update dependencies"
   Status: Open
   Author: bot789
   Created: 3 days ago

[Source: GitHub MCP Server]
```

### Step 4: Use Filesystem MCP Server

**Query 14**: Search project files

**Action**: In Copilot Chat, type:

```
@agent List all Java files in my project directory
```

**Expected Response**:

```
Found 12 Java files in your project:

src/main/java/
├── com/example/
│   ├── Application.java
│   ├── controller/
│   │   ├── UserController.java
│   │   └── ProductController.java
│   ├── service/
│   │   ├── UserService.java
│   │   └── ProductService.java
│   └── model/
│       ├── User.java
│       └── Product.java

src/test/java/
├── com/example/
    ├── UserControllerTest.java
    ├── ProductControllerTest.java
    └── UserServiceTest.java

[Source: Filesystem MCP Server]
```

**Query 15**: Analyze project structure

**Action**: In Copilot Chat, type:

```
@agent Analyze the structure of my project and suggest improvements
```

**Expected Response**:

```
Project Structure Analysis:

Current Structure:
- Well-organized package structure
- Separation of concerns (controller, service, model)
- Test files present

Suggestions:
1. Add a repository layer for database operations
2. Create a config package for configuration classes
3. Add a util package for helper methods
4. Consider adding a dto package for data transfer objects
5. Missing integration tests - recommend adding

Would you like me to help create any of these suggested additions?

[Source: Filesystem MCP Server + GitHub Copilot Analysis]
```

---

## Part 5: Advanced MCP Configuration

### Workspace-Specific MCP Servers

**Query 16**: Create workspace-specific configuration

**Action**:
1. Create `.vscode` folder in your project root
2. Create `.vscode/mcp.json` file
3. Add workspace-specific MCP servers:

```json
{
  "servers": {
    "project-database": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://localhost:5432/mydb"
      ],
      "env": {
        "PGUSER": "postgres",
        "PGPASSWORD": "your-password"
      }
    },
    "project-docs": {
      "command": "python",
      "args": ["-m", "mcp_server_docs", "./docs"]
    }
  }
}
```

**Security Note**: Never commit credentials to version control. Use environment variables or .env files (added to .gitignore).

### Using Docker for MCP Servers

**Query 17**: Configure Docker-based MCP server

**Action**: Update mcp.json to use Docker:

```json
{
  "servers": {
    "redis-docker": {
      "command": "docker",
      "args": [
        "run",
        "--rm",
        "-i",
        "redis-mcp-server:latest"
      ]
    }
  }
}
```

### Environment Variables

**Query 18**: Use environment variables for sensitive data

**Action**:
1. Create `.env` file in project root:

```env
GITHUB_TOKEN=ghp_your_token_here
DATABASE_URL=postgresql://localhost:5432/mydb
API_KEY=your_api_key_here
```

2. Update mcp.json to reference environment variables:

```json
{
  "servers": {
    "github-custom": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

3. Add `.env` to `.gitignore`

---

## Part 6: Managing MCP Servers

### View Active MCP Servers

**Query 19**: List all active MCP servers

**Action**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Show Active Servers"
3. Press Enter

**Expected Result**:

```
Active MCP Servers:
┌──────────────┬──────────┬────────┬─────────┐
│ Name         │ Type     │ Status │ PID     │
├──────────────┼──────────┼────────┼─────────┤
│ github       │ HTTP     │ Active │ N/A     │
│ time         │ Python   │ Active │ 12345   │
│ filesystem   │ Node.js  │ Active │ 12346   │
└──────────────┴──────────┴────────┴─────────┘
```

### Restart MCP Server

**Query 20**: Restart a specific MCP server

**Action**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Restart Server"
3. Select the server to restart from the list
4. Press Enter

**Expected Result**:
```
Restarting MCP server "time"...
Server "time" restarted successfully (PID: 12367)
```

### Stop MCP Server

**Query 21**: Stop a running MCP server

**Action**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Stop Server"
3. Select the server to stop
4. Press Enter

**Expected Result**:
```
Stopping MCP server "filesystem"...
Server "filesystem" stopped successfully
```

### Uninstall MCP Server

**Query 22**: Uninstall an MCP server

**Action**:
1. Open mcp.json
2. Remove the server configuration
3. Save the file
4. VS Code automatically stops the server

**Or use Command Palette**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Uninstall Server"
3. Select server to uninstall
4. Confirm removal

---

## Part 7: Debugging MCP Servers

### View MCP Server Logs

**Query 23**: Check MCP server logs

**Action**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Show Server Logs"
3. Select the server
4. Press Enter

**Expected Result**: Output panel shows server logs:

```
[2026-02-04 14:30:15] MCP Server "time" starting...
[2026-02-04 14:30:15] Loading timezone configuration...
[2026-02-04 14:30:16] Server ready on stdio
[2026-02-04 14:30:45] Received request: get_current_time
[2026-02-04 14:30:45] Response sent: 2026-02-04T14:30:45-05:00
```

### Test MCP Server Connection

**Query 24**: Test MCP server connectivity

**Action**: In Copilot Chat, type:

```
@agent Test the connection to the Time MCP server
```

**Expected Response**:

```
Testing Time MCP server connection...

Status: Connected
Response Time: 45ms
Server Version: 0.1.0
Capabilities: get_current_time, convert_timezone, get_timezone_info

Test successful!
```

### Common Issues and Solutions

**Issue 1: Server Not Starting**

**Error**: "MCP server 'time' failed to start"

**Solution**:
1. Check server logs
2. Verify command and args are correct
3. Ensure dependencies are installed
4. Check file permissions

**Issue 2: Authentication Failed**

**Error**: "Authentication required for GitHub MCP server"

**Solution**:
1. Press Ctrl+Shift+P
2. Type: "MCP: Authenticate Server"
3. Select "github"
4. Follow authentication flow

**Issue 3: Server Timeout**

**Error**: "MCP server 'filesystem' timeout"

**Solution**:
1. Increase timeout in settings:

```json
{
  "mcp.serverTimeout": 30000
}
```

2. Restart the server

---

## Part 8: Create Your Own MCP Server

### Simple Python MCP Server

**Query 25**: Create a custom Weather MCP server

**Action**:

1. Create project directory:

```bash
mkdir my-weather-mcp
cd my-weather-mcp
```

2. Create `weather_server.py`:

```python
#!/usr/bin/env python3
import sys
import json
from datetime import datetime

def get_weather(location):
    # Simulated weather data
    weather_data = {
        "location": location,
        "temperature": 72,
        "condition": "Sunny",
        "humidity": 65,
        "timestamp": datetime.now().isoformat()
    }
    return weather_data

def handle_request(request):
    method = request.get("method")
    params = request.get("params", {})

    if method == "get_weather":
        location = params.get("location", "New York")
        result = get_weather(location)
        return {"result": result}
    else:
        return {"error": "Unknown method"}

def main():
    # Read JSON-RPC requests from stdin
    for line in sys.stdin:
        try:
            request = json.loads(line)
            response = handle_request(request)
            print(json.dumps(response))
            sys.stdout.flush()
        except Exception as e:
            error_response = {"error": str(e)}
            print(json.dumps(error_response))
            sys.stdout.flush()

if __name__ == "__main__":
    main()
```

3. Make it executable:

```bash
chmod +x weather_server.py
```

4. Add to mcp.json:

```json
{
  "servers": {
    "my-weather": {
      "command": "python",
      "args": ["/full/path/to/weather_server.py"]
    }
  }
}
```

5. Test in Copilot Chat:

```
@agent What's the weather in San Francisco using my-weather server?
```

**Expected Response**:

```
Current weather in San Francisco:
- Temperature: 72°F
- Condition: Sunny
- Humidity: 65%
- Last updated: 2026-02-04T14:45:30

[Source: my-weather MCP Server]
```

### Node.js MCP Server

**Query 26**: Create a Calculator MCP server in Node.js

**Action**:

1. Create project:

```bash
mkdir calculator-mcp
cd calculator-mcp
npm init -y
```

2. Create `calculator-server.js`:

```javascript
#!/usr/bin/env node

const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
  terminal: false
});

function calculate(operation, a, b) {
  switch(operation) {
    case 'add': return a + b;
    case 'subtract': return a - b;
    case 'multiply': return a * b;
    case 'divide': return b !== 0 ? a / b : 'Error: Division by zero';
    default: return 'Unknown operation';
  }
}

rl.on('line', (line) => {
  try {
    const request = JSON.parse(line);
    const { method, params } = request;

    if (method === 'calculate') {
      const { operation, a, b } = params;
      const result = calculate(operation, a, b);
      console.log(JSON.stringify({ result }));
    } else {
      console.log(JSON.stringify({ error: 'Unknown method' }));
    }
  } catch (error) {
    console.log(JSON.stringify({ error: error.message }));
  }
});
```

3. Make executable:

```bash
chmod +x calculator-server.js
```

4. Add to mcp.json:

```json
{
  "servers": {
    "calculator": {
      "command": "node",
      "args": ["/full/path/to/calculator-server.js"]
    }
  }
}
```

5. Test:

```
@agent Calculate 125 * 48 using the calculator server
```

**Expected Response**:

```
125 * 48 = 6000

[Source: calculator MCP Server]
```

---

## Part 9: Best Practices and Security

### Security Guidelines

1. **Trust Only Verified Servers**
   - Only install MCP servers from trusted sources
   - Review server code before installation
   - Check publisher reputation

2. **Limit Server Permissions**
   - Grant minimal required access
   - Use specific directory paths for filesystem servers
   - Avoid giving servers write access unless necessary

3. **Protect Credentials**
   - Never hardcode credentials in mcp.json
   - Use environment variables
   - Add .env to .gitignore
   - Use token rotation for long-lived tokens

4. **Regular Updates**
   - Keep MCP servers updated
   - Monitor security advisories
   - Remove unused servers

### Performance Optimization

1. **Lazy Loading**
   - Configure servers to start on-demand
   - Stop unused servers

2. **Caching**
   - Enable caching for frequently accessed data
   - Set appropriate cache TTL

3. **Resource Limits**
   - Set memory limits for servers
   - Configure request timeouts

### Configuration Example with Best Practices

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "timeout": 30000,
      "cache": {
        "enabled": true,
        "ttl": 300
      }
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${PROJECT_DIR}/src"
      ],
      "env": {
        "PROJECT_DIR": "${workspaceFolder}"
      },
      "autoStart": false
    }
  },
  "settings": {
    "logLevel": "info",
    "maxServerMemory": "512MB",
    "serverTimeout": 30000
  }
}
```

---

## Part 10: Practical Examples

### Example 1: Code Review Workflow

**Query 27**: Set up automated code review

**Action**: In Copilot Chat:

```
@agent Review the latest commit in this repository and check for:
1. Code quality issues
2. Security vulnerabilities
3. Test coverage
4. Documentation completeness
```

**Expected Response**:

```
Code Review for Commit abc123:

Code Quality:
- Found 2 complex methods that could be refactored
- 3 unused imports in UserService.java
- Naming conventions mostly followed

Security:
- No critical vulnerabilities detected
- Recommendation: Add input validation in UserController.java line 45

Test Coverage:
- Current coverage: 78%
- Missing tests for: ProductService.calculateDiscount()

Documentation:
- 2 public methods missing JavaDoc
- README.md needs update for new API endpoints

[Sources: GitHub MCP Server, Filesystem MCP Server]
```

### Example 2: Project Documentation Generation

**Query 28**: Generate project documentation

**Action**: In Copilot Chat:

```
@agent Create comprehensive documentation for this project including:
- Project structure
- Setup instructions
- API documentation
- Contributing guidelines
```

**Expected Response**:

```
Documentation Generated:

1. Created README.md with project overview
2. Created SETUP.md with installation steps
3. Created API.md with endpoint documentation
4. Created CONTRIBUTING.md with guidelines

Files created in /docs directory.

Would you like me to also generate:
- Architecture diagram?
- Database schema documentation?
- Deployment guide?

[Source: Filesystem MCP Server + Copilot Analysis]
```

### Example 3: Multi-Server Integration

**Query 29**: Use multiple MCP servers together

**Action**: In Copilot Chat:

```
@agent Check the current time in Tokyo, find open issues in this repository related to internationalization, and suggest which files might need timezone handling
```

**Expected Response**:

```
Multi-Server Analysis:

Current Time:
- Tokyo: 11:30 PM JST (2026-02-04)
[Time MCP Server]

Related Issues:
Found 2 issues tagged "i18n":
- Issue #67: "Date formatting incorrect for Asian locales"
- Issue #71: "Timezone conversion needed for event scheduling"
[GitHub MCP Server]

Files Requiring Timezone Handling:
1. src/main/java/com/example/service/EventService.java
   - Lines 45-67: Date creation without timezone
   - Recommendation: Use ZonedDateTime

2. src/main/java/com/example/util/DateFormatter.java
   - Lines 23-45: Hardcoded UTC timezone
   - Recommendation: Make timezone configurable

3. src/test/java/com/example/EventServiceTest.java
   - Missing timezone tests
[Filesystem MCP Server]

Would you like me to create a refactoring plan or open a new issue?

[Combined Analysis from 3 MCP Servers]
```

---

## Summary of All Queries

| Query | Purpose | Expected Result |
|-------|---------|-----------------|
| Query 1 | Open MCP configuration | mcp.json file opens |
| Query 2 | Access MCP registry | Browser shows available servers |
| Query 3 | Install GitHub server | GitHub MCP configured |
| Query 4 | Authenticate GitHub | OAuth flow completes |
| Query 5 | Install Time server | Python package installed |
| Query 6 | Configure Time server | Server added to mcp.json |
| Query 7 | Install Filesystem server | NPM package installed |
| Query 8 | Configure Filesystem | Server configured with directory |
| Query 9 | Check server status | Shows running servers |
| Query 10 | Test Time server | Returns current time |
| Query 11 | Timezone conversion | Converts time across zones |
| Query 12 | Search GitHub issues | Lists repository issues |
| Query 13 | Get pull requests | Shows recent PRs |
| Query 14 | List project files | Shows file structure |
| Query 15 | Analyze project | Suggests improvements |
| Query 16 | Workspace config | Creates .vscode/mcp.json |
| Query 17 | Docker configuration | Docker-based server setup |
| Query 18 | Environment variables | Secure credential management |
| Query 19 | List active servers | Shows server status table |
| Query 20 | Restart server | Server restarts |
| Query 21 | Stop server | Server stops |
| Query 22 | Uninstall server | Server removed |
| Query 23 | View logs | Shows server output |
| Query 24 | Test connection | Verifies server works |
| Query 25 | Create Python server | Custom weather server |
| Query 26 | Create Node.js server | Custom calculator server |
| Query 27 | Code review workflow | Automated review analysis |
| Query 28 | Generate documentation | Creates project docs |
| Query 29 | Multi-server integration | Combined server analysis |

---

## Troubleshooting Guide

### Problem: MCP Server Won't Start

**Symptoms**: Server shows as "Failed" or "Stopped"

**Solutions**:
1. Check server logs (Query 23)
2. Verify command and args in mcp.json
3. Ensure dependencies installed
4. Check file permissions
5. Try restarting VS Code

### Problem: Copilot Not Using MCP Server

**Symptoms**: Copilot gives generic responses

**Solutions**:
1. Ensure Agent Mode is enabled
2. Use @agent prefix in chat
3. Verify server is running (Query 9)
4. Restart the specific server (Query 20)
5. Check server authentication

### Problem: Server Timeout

**Symptoms**: "Server did not respond in time"

**Solutions**:
1. Increase timeout in settings
2. Check network connection (for HTTP servers)
3. Reduce query complexity
4. Restart server

---

## Key Takeaways

In this lab, you learned to:
- Understand Model Context Protocol architecture
- Install and configure MCP servers in VS Code
- Use pre-built MCP servers with GitHub Copilot
- Manage server lifecycle (start, stop, restart)
- Debug and troubleshoot MCP servers
- Create custom MCP servers in Python and Node.js
- Apply security best practices
- Integrate multiple MCP servers for complex workflows

---

## Additional Resources

- [VS Code MCP Documentation](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [GitHub Copilot MCP Guide](https://docs.github.com/en/copilot/concepts/agents/coding-agent/mcp-and-coding-agent)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)

---

## Next Steps

1. Explore more MCP servers from the official registry
2. Create custom MCP servers for your team's specific needs
3. Integrate MCP servers with your CI/CD pipeline
4. Share MCP server configurations across your team
5. Contribute to the MCP community by publishing your servers

---

## Project Cleanup

To remove MCP servers:

1. Stop all servers (Query 21)
2. Remove server configurations from mcp.json
3. Uninstall packages:

```bash
pip uninstall mcp-server-time
npm uninstall -g @modelcontextprotocol/server-filesystem
```

4. Delete workspace configuration:

```bash
rm .vscode/mcp.json
```
