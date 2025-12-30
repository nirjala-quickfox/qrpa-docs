

## 4. Runs & Scheduling

### Understanding Execution

Every time a robot executes, QuickRPA creates a **Run** record that captures everything about that execution—when it started, how long it took, whether it succeeded, what errors occurred, and more. This comprehensive tracking enables monitoring, troubleshooting, and compliance.

#### Run Records

A **Run** represents a single execution of a robot from start to finish.

**Run Properties:**
- **Status**: Active (running), Completed (success), Error (failed), Cancelled, Timeout
- **Timestamps**: Start time, end time, duration
- **Agent Information**: Which agent machine executed the run
- **Version**: Which robot version was used
- **Environment Data**: Operating system, runtime details, resource usage
- **Logs**: Detailed execution logs for troubleshooting
- **Results**: Output data or artifacts produced
- **Error Details**: If failed, comprehensive error information including stack traces

**[Image Placeholder: Run Detail Page]**

#### Scheduling System

**Scheduling** enables robots to run automatically at specified times without manual intervention—perfect for recurring tasks like nightly data backups, daily report generation, or hourly status checks.

QuickRPA uses **crontab-like syntax** for schedule definition, providing flexible scheduling patterns:

**Schedule Components:**
- **Minute** (0-59): Which minute(s) of the hour to run
- **Hour** (0-23): Which hour(s) of the day to run
- **Day of Month** (1-31): Which day(s) of the month to run
- **Month** (1-12): Which month(s) of the year to run
- **Day of Week** (0-6): Which day(s) of the week to run (0 = Sunday)

**How It Works:**
- **Celery Beat** (the scheduling component) continuously monitors the database for active schedules
- When a schedule's time criteria are met, Beat automatically creates a run request
- The run request enters the execution queue just like manually triggered runs
- Assigned agent machines pick up and execute the scheduled runs

### Creating and Managing Runs

#### Viewing Run History

1. **Access runs list**
   - Navigate to **"Monitoring" → "Runs"**
   - Or from any robot's detail page, click the **"Runs"** tab

2. **Filter runs**
   - **By Status**: Show only running, completed, or failed runs
   - **By Robot**: Filter to specific robot(s)
   - **By Date Range**: View runs within a specific time period
   - **By Agent**: See runs from specific agent machines

3. **View run details**
   - Click any run in the list
   - See comprehensive execution details including:
     - Timeline visualization
     - Log viewer with real-time updates (for active runs)
     - Input/output data
     - Resource usage metrics
     - Error information (if applicable)

**[Image Placeholder: Runs List with Filters]**

#### Analyzing Failed Runs

1. **Identify failed runs**
   - In the runs list, filter by **Status: Error**
   - Failed runs are highlighted in red

2. **Open the failed run**
   - Click the run to view details

3. **Review error information**
   - **Error Message**: High-level description of what went wrong
   - **Error Type**: Classification (e.g., "Connection Timeout", "Data Validation Error", "System Exception")
   - **Stack Trace**: Detailed technical error trace for developers
   - **Failure Point**: Which step in the process failed

4. **Examine logs**
   - Click the **"Logs"** tab
   - Scroll to entries marked with ERROR or WARNING
   - Use the search function to find specific issues

5. **Determine corrective action**
   - **Temporary Issue**: Retry the run if the error was environmental (network hiccup, system temporarily unavailable)
   - **Data Issue**: Correct the input data and retry
   - **Bug**: Report to developers for robot code correction

6. **Retry the run** (if appropriate)
   - Click **"Retry Run"**
   - The robot re-executes with the same configuration

**[Image Placeholder: Failed Run Detail with Error Information]**

### Scheduling Robots

#### Creating a Schedule

**Prerequisites:** Schedule Management permissions for the robot's workspace

1. **Navigate to the robot**
   - Open the robot you want to schedule
   - Click the **"Schedules"** tab

2. **Create new schedule**
   - Click **"+ Add Schedule"**

3. **Define schedule timing**
   - **Schedule Name**: Descriptive name (e.g., "Nightly Processing", "Hourly Status Check")
   - **Schedule Type**: Choose from:
     - **Simple**: Predefined patterns (Daily, Weekly, Monthly)
     - **Advanced**: Custom crontab expression

4. **Configure simple schedule** (if selected)
   - **Frequency**: Every day, specific weekdays, or specific dates
   - **Time**: What time to run (e.g., 2:00 AM)
   - **Timezone**: Ensure correct timezone for execution

5. **Configure advanced schedule** (if selected)
   - **Cron Expression**: Enter crontab syntax
   - Examples:
     - `0 2 * * *` = Daily at 2:00 AM
     - `0 */4 * * *` = Every 4 hours
     - `0 9 * * 1-5` = Weekdays at 9:00 AM
     - `0 0 1 * *` = First day of each month at midnight
   - Use the **"Cron Helper"** tool to build expressions visually

6. **Set schedule parameters**
   - **Agent Assignment**: Which agent(s) can execute this schedule
   - **Queue**: If processing queue items, specify the queue
   - **Active**: Toggle to enable/disable the schedule without deleting it

7. **Configure notifications** (optional)
   - **On Success**: Send notification when completed successfully
   - **On Failure**: Send notification when errors occur
   - **Recipients**: Who receives notifications

8. **Save the schedule**
   - Click **"Create Schedule"**
   - The schedule becomes active immediately if toggled on

**[Image Placeholder: Create Schedule Form with Cron Builder]**

#### Managing Schedules

1. **View all schedules**
   - From the robot's **"Schedules"** tab, see all defined schedules
   - **Active** schedules show a green indicator
   - **Inactive** schedules show a gray indicator

2. **Edit a schedule**
   - Click the **"Edit"** icon (✏️) next to the schedule
   - Modify timing, parameters, or notification settings
   - Click **"Save Changes"**

3. **Temporarily disable a schedule**
   - Useful for maintenance windows or when suspending automation temporarily
   - Toggle the **"Active"** switch to off
   - The schedule remains configured but won't trigger runs

4. **Re-enable a schedule**
   - Toggle the **"Active"** switch back on
   - Scheduled runs resume immediately

5. **Delete a schedule**
   - Click the **"Delete"** icon (🗑️) next to the schedule
   - Confirm deletion
   - Note: This doesn't affect historical runs triggered by this schedule

6. **View schedule execution history**
   - Click the schedule name to see its detail page
   - The **"History"** tab shows all runs triggered by this schedule
   - Analyze patterns: success rates, average duration, common failure times

**[Image Placeholder: Schedules List]**

#### Common Scheduling Patterns

**Business Hours Only:**
```
0 9-17 * * 1-5
```
(Every hour from 9 AM to 5 PM, Monday through Friday)

**Nightly Processing:**
```
0 2 * * *
```
(Every day at 2:00 AM—common time for overnight batch processing)

**Every 15 Minutes:**
```
*/15 * * * *
```
(Four times per hour—good for status checking or monitoring)

**Monthly Reports:**
```
0 8 1 * *
```
(First day of each month at 8:00 AM)

**Quarter-End Processing:**
```
0 23 31 3,6,9,12 *
```
(11:00 PM on the last day of March, June, September, and December)

#### Best Practices

- **Off-Peak Scheduling**: Schedule resource-intensive robots during low-traffic periods (nights, weekends)
- **Stagger Schedules**: Don't schedule multiple heavy robots at the exact same time
- **Monitoring**: Set up failure notifications for critical scheduled robots
- **Testing**: Test schedules initially by setting them to run frequently, then adjust to production schedule
- **Documentation**: Document why specific schedule times were chosen for future reference
- **Timezone Awareness**: Always verify the timezone used—especially important for organizations spanning multiple regions

---
