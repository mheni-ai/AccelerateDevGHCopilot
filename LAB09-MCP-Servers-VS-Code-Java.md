# LAB 09 - Install and Use MCP Servers in VS Code (Java Focus)

## Description
Learn to install, configure, and use Model Context Protocol (MCP) servers in Visual Studio Code with GitHub Copilot, focusing on Java development scenarios.

**Estimated Duration**: 45 minutes

---

## Learning Objectives

By the end of this exercise, you will be able to:
- Understand what MCP servers are and how they work
- Install MCP servers from the VS Code Extensions view
- Configure MCP servers for your workspace
- Use MCP servers with GitHub Copilot Chat
- Create custom MCP server configurations
- Work with filesystem and GitHub MCP servers for Java projects
- Troubleshoot MCP server issues

---

## Prerequisites

- Visual Studio Code 1.102 or higher
- GitHub Copilot subscription (active)
- GitHub Copilot Chat extension installed
- JDK 17 or higher
- Maven 3.8 or higher
- Node.js 18+ (for NPX-based MCP servers)
- Docker (optional, for containerized MCP servers)
- Git configured with GitHub account

---

## What is MCP (Model Context Protocol)?

### Overview

Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context to Large Language Models (LLMs). It enables GitHub Copilot to:

- Access external tools and data sources
- Interact with your filesystem securely
- Connect to GitHub repositories, issues, and pull requests
- Query databases and APIs
- Execute custom commands and scripts

### Key Components

1. **MCP Server**: Provides specific capabilities (tools, resources, prompts)
2. **MCP Client**: GitHub Copilot in VS Code acts as the client
3. **Protocol**: Standardized communication between server and client

### Benefits for Java Development

- Access project files and Maven dependencies
- Query Git history and branches
- Interact with GitHub issues and PRs
- Read configuration files (pom.xml, application.properties)
- Execute Maven commands
- Search through Java codebases

---

## Part 1: Enable MCP Support in VS Code

### Step 1: Verify VS Code Version

**Action**: Check your VS Code version

1. Open VS Code
2. Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
3. Type "About"
4. Verify version is 1.102 or higher

**Expected Result**:

```
Version: 1.102.0 (or higher)
```

If your version is lower, update VS Code:
- Help > Check for Updates

### Step 2: Verify GitHub Copilot is Active

**Action**: Check Copilot status

1. Look at the bottom-right corner of VS Code
2. You should see the GitHub Copilot icon
3. Click on it to verify status shows "Ready"

**Expected Result**:

```
GitHub Copilot: Ready
Subscription: Active
```

### Step 3: Enable MCP Support (if needed)

**Action**: Check MCP settings

1. Open Settings (Ctrl+, or Cmd+,)
2. Search for "MCP"
3. Verify "GitHub Copilot > MCP: Enabled" is checked

**Expected Result**:

```
[✓] GitHub Copilot: MCP Enabled
```

**Note**: For enterprise users, your administrator must enable the "MCP servers in Copilot" policy.

---

## Part 2: Install MCP Servers from Extensions View

### Step 1: Access MCP Servers in Extensions

**Query 1**: Open MCP servers registry

**Action**:

1. Click on the Extensions icon in the Activity Bar (Ctrl+Shift+X or Cmd+Shift+X)
2. In the search box, type "MCP"
3. Click on "Model Context Protocol Servers" section

**Expected Result**:

You will see a list of available MCP servers from the GitHub MCP registry, including:

- **GitHub MCP Server**: Access repository data, issues, PRs
- **Filesystem MCP Server**: Read and write files securely
- **Git MCP Server**: Query Git history and branches
- **Memory MCP Server**: Persistent conversation memory
- **Postgres MCP Server**: Query PostgreSQL databases
- **Brave Search MCP Server**: Web search capabilities

### Step 2: Install Filesystem MCP Server

**Query 2**: Install filesystem server for Java project access

**Action**:

1. In the Extensions MCP section, search for "filesystem"
2. Find "Filesystem MCP Server"
3. Click "Install" button

**Expected Result**:

VS Code will:
1. Download the MCP server package
2. Install it in your user profile
3. Show "Installed" status
4. Display configuration options

**Alternative: Install in Workspace**

If you want the MCP server only for current project:

1. Right-click "Filesystem MCP Server"
2. Select "Install in Workspace"
3. This creates `.vscode/mcp.json` in your workspace

### Step 3: Configure Filesystem MCP Server

**Query 3**: Configure allowed directories

**Action**:

1. After installation, click "Configure" next to the installed server
2. Or manually create/edit configuration

**For User Profile Configuration**:

Location: `~/.vscode/mcp.json` (Linux/Mac) or `%USERPROFILE%\.vscode\mcp.json` (Windows)

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/your/java/projects"
      ]
    }
  }
}
```

**For Workspace Configuration**:

Location: `.vscode/mcp.json` in your workspace

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}"
      ]
    }
  }
}
```

**Important**: Replace `/path/to/your/java/projects` with your actual Java project directory path.

**Expected Result**:

```
Filesystem MCP Server configured
Allowed directories: /path/to/your/java/projects
Status: Ready
```

### Step 4: Verify MCP Server is Running

**Query 4**: Check server status

**Action**:

1. Open GitHub Copilot Chat (Ctrl+Shift+I or Cmd+Shift+I)
2. Click on the "Attach Context" button (paperclip icon)
3. Look for "MCP Servers" section

**Expected Result**:

You should see:

```
Available MCP Servers:
  • filesystem (Connected)
    Tools: read_file, write_file, list_directory, search_files
```

---

## Part 3: Install and Configure GitHub MCP Server

### Step 1: Install GitHub MCP Server

**Query 5**: Install GitHub server for repository access

**Action**:

1. In Extensions view, search for "GitHub MCP Server"
2. Click "Install"

**Expected Result**:

The GitHub MCP server is installed automatically with default configuration for the current repository.

### Step 2: Configure GitHub MCP Server for Broader Access

**Query 6**: Configure custom GitHub token

**Action**:

By default, GitHub MCP server has read-only access to current repository. To access multiple repositories or write data:

1. Create a GitHub Personal Access Token:
   - Go to https://github.com/settings/tokens
   - Click "Generate new token (classic)"
   - Select scopes: `repo`, `read:org`, `read:user`
   - Generate and copy the token

2. Configure the MCP server in `.vscode/mcp.json`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your_token_here"
      }
    }
  }
}
```

**Security Note**: Never commit tokens to version control. Use environment variables or VS Code secrets.

**Better approach using environment variable**:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

Then set `GITHUB_TOKEN` in your system environment variables.

**Expected Result**:

```
GitHub MCP Server configured
Authentication: Personal Access Token
Status: Connected
```

---

## Part 4: Create a Java Project and Use MCP Servers

### Step 1: Create Sample Java Maven Project

**Action**: Create a new Java project

```bash
mvn archetype:generate -DgroupId=com.example.mcp -DartifactId=mcp-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd mcp-demo
code .
```

**Project Structure**:

```
mcp-demo/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── example/
    │               └── mcp/
    │                   └── App.java
    └── test/
        └── java/
            └── com/
                └── example/
                    └── mcp/
                        └── AppTest.java
```

### Step 2: Configure Filesystem MCP for This Project

**Query 7**: Create workspace MCP configuration

**Action**:

Create `.vscode/mcp.json` in your project:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}/src",
        "${workspaceFolder}/target"
      ]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    }
  }
}
```

This configuration allows MCP to access:
- `src/` directory (source code)
- `target/` directory (compiled classes)

**Expected Result**:

```
MCP Configuration loaded
Servers:
  • filesystem: Connected (2 directories)
  • github: Connected
```

---

## Part 5: Use MCP Servers with GitHub Copilot Chat

### Step 1: Use Filesystem MCP to Read Files

**Query 8**: Ask Copilot to analyze pom.xml

**Action**:

1. Open GitHub Copilot Chat (Ctrl+Shift+I or Cmd+Shift+I)
2. Type this prompt:

```
Read the pom.xml file and tell me what dependencies are configured
```

**Expected Copilot Behavior**:

1. Copilot detects you want to read a file
2. It uses the filesystem MCP server
3. Reads `pom.xml` using the `read_file` tool
4. Analyzes the content
5. Returns the dependency list

**Expected Response**:

```
I've read your pom.xml file. Here are the configured dependencies:

1. JUnit Jupiter (version 5.9.3)
   - GroupId: org.junit.jupiter
   - ArtifactId: junit-jupiter
   - Scope: test

This is a basic Maven project with only JUnit 5 for testing.

Would you like me to suggest additional dependencies for your project?
```

**Behind the scenes**: Copilot called the filesystem MCP server's `read_file` tool with parameter `path: "pom.xml"`.

### Step 2: Use Filesystem MCP to List Java Files

**Query 9**: List all Java source files

**Action**: In Copilot Chat, type:

```
List all Java files in the src/main/java directory
```

**Expected Copilot Behavior**:

1. Uses filesystem MCP `list_directory` tool
2. Recursively lists files in `src/main/java`
3. Filters for `.java` files

**Expected Response**:

```
I found the following Java files in src/main/java:

1. com/example/mcp/App.java

The project currently has one main class: App.java in the com.example.mcp package.

Would you like me to analyze the code in App.java?
```

### Step 3: Use Filesystem MCP to Create New Java Class

**Query 10**: Create a new service class

**Action**: In Copilot Chat, type:

```
Create a new Java class called UserService in src/main/java/com/example/mcp/service/ with methods to:
- Create a user
- Get user by ID
- List all users
- Update user
- Delete user

Use a simple in-memory list to store users.
```

**Expected Copilot Behavior**:

1. Uses filesystem MCP `create_directory` tool to create `service/` directory
2. Uses `write_file` tool to create `UserService.java`
3. Generates appropriate Java code

**Expected Response**:

```
I've created the UserService class at src/main/java/com/example/mcp/service/UserService.java

The class includes:
- In-memory user storage using ArrayList
- CRUD operations (Create, Read, Update, Delete)
- User model class nested inside
- Auto-incrementing ID generation

Here's the created code:
[Code snippet shown]

The file has been written to your project. Would you like me to:
1. Create unit tests for this service?
2. Add validation logic?
3. Create a REST controller to expose these operations?
```

**Verify**: Check that the file was actually created:

```bash
ls -la src/main/java/com/example/mcp/service/
cat src/main/java/com/example/mcp/service/UserService.java
```

### Step 4: Use Filesystem MCP to Search Code

**Query 11**: Search for specific patterns

**Action**: In Copilot Chat, type:

```
Search for all occurrences of "public static void main" in the project
```

**Expected Copilot Behavior**:

1. Uses filesystem MCP `search_files` tool
2. Searches for the pattern across all files
3. Returns matching files and line numbers

**Expected Response**:

```
I found 1 occurrence of "public static void main":

1. src/main/java/com/example/mcp/App.java (line 8)
   - This is the main entry point of the application

Would you like me to show the complete main method?
```

---

## Part 6: Use GitHub MCP Server

### Step 1: Initialize Git Repository

**Action**: Set up Git for the project

```bash
git init
git add .
git commit -m "Initial commit"
```

### Step 2: Create GitHub Repository and Push

**Action**:

1. Create repository on GitHub: https://github.com/new
2. Name it "mcp-demo"
3. Push local code:

```bash
git remote add origin https://github.com/YOUR_USERNAME/mcp-demo.git
git branch -M main
git push -u origin main
```

### Step 3: Use GitHub MCP to Query Repository

**Query 12**: Get repository information

**Action**: In Copilot Chat, type:

```
What is the current repository and how many commits do we have?
```

**Expected Copilot Behavior**:

1. Uses GitHub MCP server to query repository data
2. Retrieves repository name, owner, and commit history

**Expected Response**:

```
Current repository: YOUR_USERNAME/mcp-demo

Repository information:
- Name: mcp-demo
- Owner: YOUR_USERNAME
- Default branch: main
- Total commits: 1
- Last commit: "Initial commit"
- Visibility: Public

Would you like me to show the commit history or repository statistics?
```

### Step 4: Use GitHub MCP to List Issues

**Query 13**: Create and list issues

**Action**:

First, create an issue manually on GitHub or ask Copilot:

```
Create a new issue titled "Add user authentication" with description "Implement JWT-based authentication for UserService"
```

Then ask:

```
List all open issues in this repository
```

**Expected Copilot Behavior**:

1. Uses GitHub MCP `create_issue` tool (if creating)
2. Uses GitHub MCP `list_issues` tool
3. Retrieves and displays issue information

**Expected Response**:

```
Open issues in mcp-demo:

1. Issue #1: "Add user authentication"
   - State: Open
   - Created: 2026-02-04
   - Labels: None
   - Description: Implement JWT-based authentication for UserService

Total: 1 open issue

Would you like me to:
1. Start working on this issue?
2. Create a branch for this issue?
3. Add more details to the issue?
```

### Step 5: Use GitHub MCP for Pull Request Information

**Query 14**: Query pull requests

**Action**: In Copilot Chat, type:

```
List all pull requests in this repository
```

**Expected Response**:

```
There are currently no open pull requests in this repository.

Would you like me to:
1. Create a new branch for a feature?
2. Help you prepare a pull request?
3. Show closed pull requests?
```

---

## Part 7: Advanced MCP Usage with Java

### Step 1: Use MCP to Analyze Maven Dependencies

**Query 15**: Analyze dependency tree

**Action**: In Copilot Chat, type:

```
Read the pom.xml file and suggest additional dependencies I should add for:
- REST API development with Spring Boot
- Database access with JPA
- JSON processing
- Testing with Mockito
```

**Expected Response**:

```
Based on your pom.xml, here are recommended dependencies to add:

1. Spring Boot Starter Web (REST API):
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.2.0</version>
</dependency>

2. Spring Boot Starter Data JPA (Database):
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <version>3.2.0</version>
</dependency>

3. H2 Database (In-memory for development):
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

4. Mockito (Enhanced testing):
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.3.1</version>
    <scope>test</scope>
</dependency>

Would you like me to update your pom.xml with these dependencies?
```

### Step 2: Let MCP Update pom.xml

**Query 16**: Update configuration file

**Action**: In Copilot Chat, type:

```
Yes, please update pom.xml with all the suggested dependencies
```

**Expected Copilot Behavior**:

1. Uses filesystem MCP `read_file` to read current pom.xml
2. Modifies the content to add dependencies
3. Uses `write_file` to save updated pom.xml

**Expected Response**:

```
I've updated your pom.xml file with the following dependencies:

Added:
- spring-boot-starter-web (3.2.0)
- spring-boot-starter-data-jpa (3.2.0)
- h2 database
- mockito-core (5.3.1)

The file has been saved. You should now run:
mvn clean install

Would you like me to create a Spring Boot application class to use these dependencies?
```

**Verify**:

```bash
cat pom.xml
mvn clean install
```

### Step 3: Use MCP to Create Complete Feature

**Query 17**: Complex multi-file feature creation

**Action**: In Copilot Chat, type:

```
Create a complete REST API for user management with:
1. User entity class with JPA annotations
2. UserRepository interface extending JpaRepository
3. UserService with business logic
4. UserController with REST endpoints
5. Unit tests for UserService
6. Integration tests for UserController

Place files in appropriate directories following Spring Boot conventions.
```

**Expected Copilot Behavior**:

1. Uses filesystem MCP to create multiple directories
2. Writes multiple Java files
3. Organizes files following Spring Boot structure

**Expected Response**:

```
I've created a complete REST API for user management:

Files created:
1. src/main/java/com/example/mcp/entity/User.java
   - JPA entity with ID, name, email, createdAt

2. src/main/java/com/example/mcp/repository/UserRepository.java
   - JpaRepository interface with custom query methods

3. src/main/java/com/example/mcp/service/UserService.java
   - Business logic for CRUD operations

4. src/main/java/com/example/mcp/controller/UserController.java
   - REST endpoints: GET, POST, PUT, DELETE

5. src/test/java/com/example/mcp/service/UserServiceTest.java
   - Unit tests with Mockito

6. src/test/java/com/example/mcp/controller/UserControllerTest.java
   - Integration tests with MockMvc

Project structure now follows Spring Boot best practices.

Next steps:
1. Run: mvn clean test (to verify tests pass)
2. Run: mvn spring-boot:run (to start the application)
3. Test endpoints at http://localhost:8080/api/users

Would you like me to create a README.md with API documentation?
```

---

## Part 8: Install Additional MCP Servers

### Step 1: Install Git MCP Server

**Query 18**: Install Git server for version control

**Action**:

1. In Extensions view, search "Git MCP Server"
2. Click "Install"

Or configure manually in `.vscode/mcp.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "${workspaceFolder}"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"]
    }
  }
}
```

**Use Case**: Query Git history

**Action**: In Copilot Chat, type:

```
Show me the commit history for the last 5 commits with details
```

**Expected Response**:

```
Last 5 commits:

1. abc1234 - "Add user REST API" (2026-02-04)
   - Added User entity, repository, service, controller
   - Added comprehensive tests

2. def5678 - "Update pom.xml with Spring Boot dependencies" (2026-02-04)
   - Added Spring Boot starters
   - Added testing dependencies

3. ghi9012 - "Create UserService" (2026-02-04)
   - Added in-memory user management

4. jkl3456 - "Initial commit" (2026-02-04)
   - Project setup with Maven

Would you like me to show the diff for any of these commits?
```

### Step 2: Install Memory MCP Server

**Query 19**: Install persistent memory server

**Action**:

Configure in `.vscode/mcp.json`:

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    }
  }
}
```

**Use Case**: Remember project context across sessions

**Action**: In Copilot Chat, type:

```
Remember that this project uses Spring Boot 3.2.0 with H2 database and follows REST API best practices. The main entity is User with fields: id, name, email, createdAt.
```

**Expected Response**:

```
I've stored this information:
- Framework: Spring Boot 3.2.0
- Database: H2 (in-memory)
- API Style: REST
- Main Entity: User (id, name, email, createdAt)

This context will be remembered in future conversations.
```

Later, in a new chat session:

```
What database is this project using?
```

**Expected Response**:

```
Based on my memory, this project uses H2 database (in-memory) with Spring Boot 3.2.0.
```

---

## Part 9: Create Custom MCP Server Configuration

### Example: Custom Java Tools MCP Configuration

**Query 20**: Create custom configuration for Java development

**Action**: Create comprehensive `.vscode/mcp.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}/src/main/java",
        "${workspaceFolder}/src/main/resources",
        "${workspaceFolder}/src/test/java",
        "${workspaceFolder}/target"
      ],
      "description": "Access to Java source, resources, and build output"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      },
      "description": "GitHub repository access"
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"],
      "description": "Git version control operations"
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "Persistent conversation memory"
    }
  }
}
```

**Benefits**:

- Organized access to all Java project directories
- GitHub integration for issues and PRs
- Git history and branch management
- Persistent context across sessions

---

## Part 10: Troubleshooting MCP Servers

### Common Issues and Solutions

#### Issue 1: MCP Server Not Connecting

**Symptoms**:
- Server shows "Disconnected" in Copilot Chat
- Tools not available

**Solutions**:

1. Check VS Code Output panel:
   - View > Output
   - Select "GitHub Copilot" from dropdown
   - Look for MCP-related errors

2. Verify NPX is installed:
```bash
npx --version
```

3. Manually test MCP server:
```bash
npx -y @modelcontextprotocol/server-filesystem /path/to/directory
```

4. Restart VS Code

#### Issue 2: Permission Denied Errors

**Symptoms**:
- Filesystem operations fail
- "Path not allowed" errors

**Solution**:

Check allowed directories in configuration:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}"
      ]
    }
  }
}
```

Ensure the path exists and is accessible.

#### Issue 3: GitHub MCP Authentication Failures

**Symptoms**:
- Cannot access repositories
- "Unauthorized" errors

**Solution**:

1. Verify GitHub token is valid:
   - Go to https://github.com/settings/tokens
   - Check token hasn't expired
   - Verify required scopes are enabled

2. Set token in environment:
```bash
export GITHUB_TOKEN=your_token_here
```

3. Restart VS Code after setting environment variable

#### Issue 4: MCP Server Crashes

**Symptoms**:
- Server repeatedly disconnects
- VS Code shows error notifications

**Solution**:

1. Check Node.js version:
```bash
node --version  # Should be 18 or higher
```

2. Clear NPX cache:
```bash
npm cache clean --force
```

3. Reinstall MCP server:
```bash
npx clear-npx-cache
```

4. Check VS Code logs:
   - Help > Toggle Developer Tools
   - Console tab for errors

---

## Part 11: Best Practices for MCP Usage

### Security Best Practices

1. **Limit Filesystem Access**:
   - Only grant access to necessary directories
   - Use workspace-specific configurations
   - Never allow access to sensitive directories (home, system)

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}/src",
        "${workspaceFolder}/docs"
      ]
    }
  }
}
```

2. **Protect Tokens and Secrets**:
   - Never commit tokens in `.vscode/mcp.json`
   - Use environment variables
   - Add `.vscode/mcp.json` to `.gitignore` if it contains sensitive data

3. **Review MCP Operations**:
   - Always review file changes before accepting
   - Verify GitHub operations before confirming
   - Use git to track MCP-generated changes

### Performance Best Practices

1. **Minimize Allowed Directories**:
   - More directories = slower initialization
   - Only include directories you need

2. **Use Workspace Configurations**:
   - Workspace configs are project-specific
   - Faster than global configurations

3. **Restart MCP Servers**:
   - If servers become slow, restart VS Code
   - Clear caches periodically

---

## Summary of All Queries Used

| Query | Purpose | MCP Server Used | Result |
|-------|---------|----------------|--------|
| Query 1 | Open MCP registry | N/A | Extensions view |
| Query 2 | Install filesystem server | N/A | Server installed |
| Query 3 | Configure allowed dirs | filesystem | Configuration created |
| Query 4 | Verify server status | filesystem | Server connected |
| Query 5 | Install GitHub server | N/A | Server installed |
| Query 6 | Configure GitHub token | github | Authentication setup |
| Query 7 | Create workspace config | filesystem | Project configuration |
| Query 8 | Read pom.xml | filesystem | Dependencies analyzed |
| Query 9 | List Java files | filesystem | File list returned |
| Query 10 | Create new class | filesystem | UserService.java created |
| Query 11 | Search code patterns | filesystem | Pattern found |
| Query 12 | Get repo info | github | Repository details |
| Query 13 | List issues | github | Issues displayed |
| Query 14 | List pull requests | github | PR list shown |
| Query 15 | Analyze dependencies | filesystem | Suggestions provided |
| Query 16 | Update pom.xml | filesystem | File updated |
| Query 17 | Create REST API | filesystem | Multiple files created |
| Query 18 | Install Git server | git | Server configured |
| Query 19 | Install Memory server | memory | Persistent context |
| Query 20 | Custom configuration | multiple | Complete setup |

---

## Verification Checklist

Before completing this lab, verify:

- [ ] VS Code version is 1.102 or higher
- [ ] GitHub Copilot is active and connected
- [ ] MCP support is enabled in settings
- [ ] Filesystem MCP server is installed and running
- [ ] GitHub MCP server is installed and authenticated
- [ ] You can read files using MCP in Copilot Chat
- [ ] You can create files using MCP in Copilot Chat
- [ ] You can query GitHub data using MCP
- [ ] Custom MCP configuration is working
- [ ] All MCP servers show "Connected" status

---

## Additional Challenges (Optional)

### Challenge 1: Create Database MCP Server Configuration

**Task**: Configure a Postgres MCP server to query your local database

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgresql://user:pass@localhost:5432/mydb"
      }
    }
  }
}
```

**Use**: Ask Copilot to query database tables and generate Java entities

### Challenge 2: Create Custom MCP Server in Java

**Task**: Build your own MCP server using the Java MCP SDK

Resources:
- https://modelcontextprotocol.io/sdk/java/mcp-server
- https://github.com/codeboyzhou/mcp-java-sdk-examples

### Challenge 3: Integrate Jira MCP Server

**Task**: Add Jira MCP server to track issues and sprint progress

**Use**: Ask Copilot about sprint status and link commits to Jira tickets

---

## Key Takeaways

In this lab, you learned to:
- Install MCP servers from VS Code Extensions
- Configure filesystem access for Java projects
- Use GitHub MCP server for repository operations
- Leverage MCP servers with Copilot Chat
- Create custom MCP configurations
- Troubleshoot common MCP issues
- Follow security best practices
- Optimize MCP performance

---

## MCP Resources

### Official Documentation

- [MCP Specification](https://modelcontextprotocol.io/introduction)
- [VS Code MCP Guide](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [MCP Server Registry](https://registry.modelcontextprotocol.io/)
- [GitHub MCP Documentation](https://docs.github.com/en/copilot/concepts/agents/coding-agent/mcp-and-coding-agent)

### MCP Server Development

- [Java MCP SDK](https://modelcontextprotocol.io/sdk/java/mcp-server)
- [Build MCP Server Guide](https://modelcontextprotocol.io/docs/develop/build-server)
- [MCP Java Examples](https://github.com/codeboyzhou/mcp-java-sdk-examples)

### Popular MCP Servers for Java Development

- **Filesystem**: File operations
- **GitHub**: Repository access
- **Git**: Version control
- **Memory**: Persistent context
- **Postgres**: Database queries
- **Maven**: Build automation (custom)
- **JUnit**: Test execution (custom)

---

## Next Steps

After completing this lab:

1. Explore additional MCP servers in the registry
2. Create custom MCP servers for your specific needs
3. Share your MCP configurations with your team
4. Integrate MCP into your CI/CD pipelines
5. Build organization-specific MCP servers

---

## Project Cleanup

If you want to remove the demo project:

```bash
cd ..
rm -rf mcp-demo
```

To uninstall MCP servers:

1. Open Extensions view
2. Find installed MCP servers
3. Click "Uninstall"

Or remove from configuration files:
- Delete `~/.vscode/mcp.json` (user profile)
- Delete `.vscode/mcp.json` (workspace)
