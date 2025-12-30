
## 8. Assistant (Simplified Robot Runner)

### Understanding the Assistant Feature

The **Assistant** provides a streamlined, user-friendly interface for business users who need to run specific robots without navigating the full QuickRPA platform. Think of it as a "personal robot launcher" where users see only the robots relevant to their work, with a simplified workflow for execution.

#### Why Assistant Exists

**The Challenge:**  
- Full QuickRPA interface contains many features business users don't need  
- Users may be overwhelmed by robots, queues, vaults, and technical details  
- Some users only need to trigger specific robots occasionally  

**The Solution:**  
- Assistant provides a curated list of robots for each user  
- Simple, focused interface: "Which robot do you want to run?"  
- Hides technical complexity while maintaining security and audit trail  
- Perfect for empowering business stakeholders without technical training  

#### Assistant Architecture

**AssistantUserRobot** (Link Record)  
- Connects a specific user to a specific robot  
- Defines which robots a user can access through the Assistant interface  
- Can include default parameters or configurations for easier execution  

**Assistant APIs**  
- Specialized endpoints that show only allowed robots  
- Simplified run initiation (fewer options, sensible defaults)  
- Status checking and log downloading for runs the user initiated  

**[Image Placeholder: Assistant Dashboard - User View]**

### Setting Up Assistant Access

#### Assigning Robots to Users

**Prerequisites:** Assistant Management permissions or Organization Administrator role

1. **Navigate to Assistant configuration**
   - Go to **"Administration" → "Assistant Users"**

2. **Select a user**
   - Find the user you want to configure
   - Click **"Configure Assistant Access"**

3. **Add robots to user's assistant**
   - Click **"+ Add Robot"**
   - **Select Robot**: Choose from available robots in user's workspace(s)
   - **Display Name**: Optionally customize how the robot appears to this user (e.g., "Generate My Monthly Report" instead of technical name)
   - **Description**: User-friendly explanation of what this robot does

4. **Configure default parameters** (optional)
   - **Pre-filled Inputs**: Set default values for robot parameters
   - **Queue Selection**: If the robot processes queues, pre-select the appropriate queue
   - **Agent Assignment**: Pre-assign a specific agent if needed

5. **Set permissions**
   - **Can Run**: User can execute this robot
   - **Can View Logs**: User can see execution logs
   - **Can Stop**: User can cancel running executions

6. **Save configuration**
   - Click **"Add Robot to Assistant"**
   - Repeat for each robot this user should access

**[Image Placeholder: Assign Robot to Assistant User Form]**

#### Managing Assistant Robot Assignments

1. **View user's assigned robots**
   - From **"Assistant Users"**, click a user
   - See list of all robots they can access through Assistant

2. **Edit robot configuration**
   - Click **"Edit"** next to a robot assignment
   - Modify display name, description, or default parameters
   - Click **"Save Changes"**

3. **Remove robot from user**
   - Click **"Remove"** next to a robot assignment
   - Confirm removal
   - User no longer sees this robot in their Assistant interface

4. **Bulk assignment**
   - Click **"Bulk Assign"**
   - Select multiple users
   - Select multiple robots
   - Assign all selected robots to all selected users at once
   - Useful for teams that need access to the same robots

**[Image Placeholder: Manage Assistant Robot Assignments]**

### Using the Assistant (User Perspective)

#### Accessing the Assistant

1. **Log into QuickRPA**
   - Use your standard credentials (username/password, LDAP, or OIDC)

2. **Navigate to Assistant**
   - Click **"Assistant"** in the main navigation
   - Or access the direct Assistant URL if provided (e.g., `https://quickrpa.company.com/assistant`)

3. **View available robots**
   - See only the robots assigned to you
   - Each robot shows:
     - Display name
     - Description of what it does
     - Last run status (if you've run it before)

**[Image Placeholder: Assistant Interface - Available Robots List]**

#### Running a Robot via Assistant

1. **Select the robot**
   - Click the robot you want to run
   - Or click **"Run"** button next to the robot

2. **Provide inputs** (if required)
   - Some robots need input parameters:
     - **Date Range**: Select start and end dates
     - **Account Number**: Enter specific account to process
     - **File Upload**: Upload a source file
     - **Dropdown Selections**: Choose from predefined options
   - Other robots have no inputs and run immediately

3. **Review configuration** (if shown)
   - **Queue**: Confirm which queue will be processed (if applicable)
   - **Priority**: Set priority if you have multiple pending runs

4. **Start the robot**
   - Click **"Run Robot"**
   - Confirmation message: "Robot started successfully. Run ID: 12345"

**[Image Placeholder: Assistant Robot Run Form]**

#### Monitoring Robot Execution

1. **View run status**
   - After starting a robot, see the status page showing:
     - **Status**: Running, Completed, or Failed
     - **Progress**: Percentage complete (if supported)
     - **Start Time**: When execution began
     - **Estimated Completion**: Calculated based on typical runtime

2. **Real-time updates**
   - Page automatically refreshes to show latest status
   - No need to manually reload

3. **View results**
   - When completed, see:
     - **Success Message**: "Report generated successfully"
     - **Output Files**: Download generated files (reports, data exports, etc.)
     - **Summary**: Key statistics or results from the run

**[Image Placeholder: Assistant Run Status Page]**

#### Viewing Run History

1. **Access history**
   - From the Assistant dashboard, click **"My Run History"**

2. **View past runs**
   - See all robots you've executed through Assistant
   - Columns: Robot name, run date/time, status, duration

3. **Filter history**
   - **By Robot**: Show only runs of a specific robot
   - **By Status**: Show only successful or failed runs
   - **By Date**: View runs in a specific time period

4. **Access previous results**
   - Click any past run to view its details
   - Download output files from previous executions
   - Review logs if something went wrong

**[Image Placeholder: Assistant Run History]**

#### Handling Errors

If a robot fails:

1. **Review error message**
   - The Assistant shows a user-friendly error explanation
   - Example: "Report generation failed because the source data file was not found"

2. **View basic logs** (if permitted)
   - Click **"View Details"**
   - See simplified log information (technical details may be hidden)

3. **Contact support or retry**
   - If error is unclear, contact your automation team
   - If error is temporary (e.g., system unavailable), try running again later
   - Click **"Retry Run"** if the option is available

#### Best Practices for Assistant Users

- **Understand What the Robot Does**: Read the description carefully before running
- **Check Prerequisites**: Ensure required files or data are ready before execution
- **Monitor Progress**: Don't close the browser immediately—wait for completion confirmation
- **Download Results Promptly**: Save generated files soon after completion
- **Report Issues**: If a robot consistently fails or produces incorrect results, inform your automation team

### Assistant Administration Best Practices

**For administrators configuring Assistant access:**

- **User-Friendly Naming**: Use plain language for robot display names and descriptions
- **Appropriate Access**: Only assign robots that users genuinely need
- **Set Sensible Defaults**: Pre-fill parameters when possible to reduce user errors
- **Provide Guidance**: Include clear descriptions explaining when and how to use each robot
- **Regular Reviews**: Periodically audit Assistant assignments and remove access for robots no longer needed
- **Training**: Provide brief training or documentation for Assistant users
- **Feedback Loop**: Encourage users to report issues or suggest improvements

---
