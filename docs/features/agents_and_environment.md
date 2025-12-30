
## 9. Agents & Environments

### Understanding Agents

**Agents** (also called robot runners or execution environments) are the machines where your robots actually execute. While QuickRPA orchestrates and manages automation, agents are the distributed workers that do the heavy lifting—opening applications, processing files, interacting with systems.

#### Agent Architecture

**Environment** (Database Record)
- Represents a registered agent machine in QuickRPA
- Contains: Agent name, connection details, capabilities, current status
- Belongs to a workspace
- Can be assigned to specific robots

**Agent Software** (Installed on Machine)
- Lightweight application running on Windows, Linux, or containerized environment
- Communicates with QuickRPA backend via REST APIs
- Downloads robot packages and executes them locally
- Reports status, uploads logs, and submits results

**[Image Placeholder: Agent Architecture Diagram]**

#### Agent Lifecycle

**1. Registration**
- Agent installs and starts
- Calls QuickRPA API: "I'm a new agent, here's my information"
- QuickRPA creates Environment record and returns authentication credentials

**2. Active Operation**
- Agent periodically polls: "Do you have work for me?"
- When assigned, downloads robot package and executes
- Sends heartbeat signals every minute: "I'm still alive"
- Reports progress and status throughout execution

**3. Maintenance**
- Agent may go offline for updates, reboots, or maintenance
- QuickRPA detects absence via missed heartbeats
- Status changes to "Offline" with last-seen timestamp

**4. Decommission**
- Agent uninstalls or is retired
- Administrator marks Environment as inactive in QuickRPA

### Setting Up and Managing Agents

#### Installing an Agent

**Prerequisites:**  
- Access to the machine where agent will run  
- Network connectivity from machine to QuickRPA API  
- Necessary permissions on the machine (admin/root for installation)  

**1. Prepare the machine**  
   - Ensure operating system is supported (Windows 10+, Ubuntu 20.04+, etc.)  
   - Install required dependencies (Python runtime, .NET Framework, Java—depends on your robots)  
   - Configure firewall to allow outbound HTTPS to QuickRPA server  

**2. Download agent installer**  
   - Log into QuickRPA web interface  
   - Navigate to **"Agents" → "Download Agent"**  
   - Select your operating system  
   - Download the installer package  

**3. To Be Continued**  