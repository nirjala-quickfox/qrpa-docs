
## 3. Robots & Robot Versions

### Understanding Robots

**Robots** are the heart of QuickRPA—they're the automated processes that perform work on your behalf. A robot represents a specific automation workflow, like "Invoice Processing" or "Customer Onboarding."

#### Robot Entities

**Robot (High-Level Entity)**  
- Represents a logical automation process  
- Contains metadata: name, description, category, workspace assignment  
- Can have multiple versions over its lifetime  
- Can be scheduled for automatic execution  
- Can be manually triggered by authorized users  
- Can be archived when no longer needed (soft delete—data remains for audit purposes)  

**Robot Version**  
- Represents a specific implementation of a robot  
- Contains the actual code packaged as a ZIP file  
- Includes all dependencies, configuration files, and assets needed for execution  
- Multiple versions can exist simultaneously (for testing, rollback, or A/B comparison)  
- One version is marked as "active"—this is the version that executes when the robot runs  
- Version history provides complete audit trail of changes  

### Managing Robots

**[Image Placeholder: Robots Dashboard]**

#### Creating a New Robot

**Prerequisites:** Robot Developer role or equivalent permissions in the target workspace

1. **Navigate to robots**
   - Click **"Robots"** in the main navigation
   - Ensure you're in the correct workspace using the workspace selector

2. **Initiate robot creation**
   - Click **"+ New Robot"** button

3. **Complete basic information**
   - **Robot Name**: Clear, descriptive name (e.g., "Monthly Invoice Processing")
   - **Description**: Detailed explanation of what this robot does and its purpose
   - **Category**: Select or create a category for organization (e.g., "Finance", "HR", "Operations")
   - **Tags**: Add searchable tags (e.g., "invoices", "SAP", "high-priority")

4. **Configure workspace assignment**
   - **Workspace**: Select the workspace this robot belongs to (defaults to current workspace)

5. **Set execution parameters**
   - **Timeout**: Maximum runtime before automatic termination (e.g., 60 minutes)
   - **Retry Policy**: How to handle failures (e.g., retry 3 times with 5-minute delays)
   - **Priority**: Execution priority when multiple robots are queued (Low, Normal, High, Critical)

6. **Create the robot**
   - Click **"Create Robot"**
   - You'll be redirected to the robot's detail page where you can upload the first version

**[Image Placeholder: Create Robot Form]**

#### Uploading a Robot Version

**Prerequisites:** Edit permissions for the robot

1. **Access the robot's detail page**
   - Navigate to the robot you want to update
   - Click the **"Versions"** tab

2. **Start version upload**
   - Click **"+ Upload New Version"**

3. **Prepare your robot package**
   - Ensure your robot code is packaged as a ZIP file
   - The ZIP should contain all necessary files: main script, dependencies, config files, assets
   - Follow your organization's robot packaging standards

4. **Upload the package**
   - **Version Number**: Enter a version identifier (e.g., "1.0.0", "2.1.3") following semantic versioning
   - **Release Notes**: Describe what changed in this version (bug fixes, new features, improvements)
   - **Package File**: Click **"Choose File"** and select your ZIP package
   - **Upload**: The system validates the package and uploads it

5. **Set version status**
   - **Mark as Active**: Check this box if this version should become the active version immediately
   - If unchecked, the version is uploaded but not used for execution (useful for testing)

6. **Save the version**
   - Click **"Upload Version"**
   - The system processes the package and makes it available for execution

**[Image Placeholder: Upload Robot Version Form]**

#### Managing Robot Versions

1. **View all versions**
   - From the robot's detail page, click the **"Versions"** tab
   - See a list of all versions with their status (Active, Inactive, Testing)

2. **Compare versions**
   - Select multiple versions using checkboxes
   - Click **"Compare Versions"**
   - View differences in release notes and metadata

3. **Activate a different version**
   - Find the version you want to activate
   - Click the **"Actions"** dropdown (⋮)
   - Select **"Set as Active"**
   - Confirm the change

4. **Download a version**
   - Useful for backing up or reviewing code
   - Click **"Actions"** (⋮) next to the version
   - Select **"Download Package"**
   - The ZIP file downloads to your computer

5. **Delete a version**
   - Click **"Actions"** (⋮) next to the version
   - Select **"Delete Version"**
   - Confirm deletion (note: active versions cannot be deleted until another is activated)

**[Image Placeholder: Robot Versions List]**

#### Running Robots

Robots can be executed in three ways:

**Manual Execution**

1. **Navigate to the robot**
   - Find your robot in the robots list
   - Click its name to open the detail page

2. **Start a manual run**
   - Click the **"Run Now"** button
   - A dialog appears for run configuration

3. **Configure the run** (optional)
   - **Agent Selection**: Choose a specific agent machine or allow automatic assignment
   - **Input Parameters**: Provide any required input data
   - **Queue Selection**: If the robot processes queue items, specify which queue

4. **Execute**
   - Click **"Start Run"**
   - You're redirected to the run detail page showing real-time progress

**[Image Placeholder: Manual Robot Run Dialog]**

**Scheduled Execution**

Robots can run automatically based on defined schedules (covered in detail in section 4).

**Queue-Based Execution**

Robots can automatically process items from queues (covered in detail in section 5).

#### Archiving Robots

When a robot is no longer needed but you want to preserve its history:

1. **Navigate to the robot**
   - Open the robot's detail page

2. **Archive the robot**
   - Click **"Actions"** in the top-right
   - Select **"Archive Robot"**
   - Add an optional **Archive Reason** (e.g., "Process no longer used after system upgrade")
   - Confirm archival

3. **Archived robot behavior**
   - Robot no longer appears in default lists (use "Show Archived" filter to see it)
   - Cannot be executed or scheduled
   - All historical data remains accessible for audit and compliance
   - Can be unarchived if needed later

**[Image Placeholder: Archive Robot Confirmation]**

#### Best Practices

- **Version Control Integration**: Keep your robot code in Git or similar version control before packaging
- **Semantic Versioning**: Use version numbers that indicate the scope of changes (e.g., 1.0.0 → 1.0.1 for bug fixes, 1.1.0 for new features)
- **Detailed Release Notes**: Always document what changed—your future self will thank you
- **Testing Versions**: Upload new versions as inactive first, test them with manual runs, then activate
- **Rollback Plan**: Keep at least one previous stable version available for quick rollback if issues arise
- **Documentation**: Maintain separate documentation describing what the robot does, its inputs/outputs, and error handling

---