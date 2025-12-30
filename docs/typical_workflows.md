## Typical Workflow Example

To understand how these personas collaborate within QuickRPA, let's walk through a complete end-to-end workflow showing how a typical automation execution unfolds.

### Scenario: Processing Daily Invoice Queue

**Step 1: User Initiates Execution**

A **Business Operator** logs into the QuickRPA web interface at 8:00 AM to begin the day's invoice processing. The front-end application communicates with the Django backend API to authenticate the user and load the dashboard.

The operator navigates to the "Invoice Processing Robot" and clicks "Start Now" to begin processing invoices that accumulated overnight in the queue.

**Step 2: Backend Records the Request**

The Django API receives the execution request and performs several actions:

- Validates that the user has permission to run this robot
- Creates a new **Run** record in the PostgreSQL database with status "Pending"
- Checks which agent machine is available and capable of running this robot
- Publishes a task message to **RabbitMQ** containing execution details

The API immediately returns a response to the user: "Invoice Processing Robot scheduled successfully. Run ID: 12345."

**Step 3: Task Distribution**

A **Celery Worker** monitoring RabbitMQ picks up the execution task. The worker:

- Retrieves full robot details from the database
- Identifies the assigned agent machine
- Prepares execution metadata (queue information, configuration parameters)
- Marks the run as "Queued" in the database

**Step 4: Agent Requests Work**

The designated **Agent Machine** (which polls the backend every 30 seconds) makes an API call: "Are there any jobs assigned to me?"

The backend API responds: "Yes, run Invoice Processing Robot (Run ID: 12345). Here's the package location and configuration."

**Step 5: Agent Prepares for Execution**

The agent machine begins preparation:

- **Package Download**: Downloads the Invoice Processing Robot package (version 2.3.1) from the storage system—either local file storage or Amazon S3
- **Secret Retrieval**: Requests necessary credentials from the vault (SAP login password, email account for notifications)
- **Queue Access**: Connects to the "Invoices_To_Process" queue and retrieves the first batch of 10 invoice items
- **Environment Setup**: Extracts the robot package, sets up required dependencies, configures environment variables

**Step 6: Robot Execution**

The agent launches the robot, which begins processing:

- Opens each invoice PDF from the queue
- Extracts data (vendor name, amount, date, account codes)
- Validates data against business rules
- Enters data into the SAP system
- Marks each queue item as "Completed" or "Failed" (with error details)

Throughout execution, the agent sends status updates to the backend every 60 seconds: "Processing item 3 of 10..."

**Step 7: Status Reporting**

As the robot processes invoices, the agent continuously updates the backend:

- **Progress Updates**: "5 of 10 items completed"
- **Log Streaming**: Detailed execution logs are uploaded periodically
- **Error Notifications**: If an invoice fails validation, the agent immediately reports the exception
- **Heartbeat Signals**: Regular "I'm still alive" messages ensure the backend knows execution hasn't stalled

**Step 8: Backend Updates and Notifications**

The Django backend receives all status updates and takes action:

- **Database Updates**: Updates the Run record with current progress and status
- **Queue Management**: Marks processed queue items appropriately in the database
- **Cache Updates**: Updates Redis cache with latest statistics for dashboard displays
- **Real-time Push**: If WebSockets are enabled, pushes live updates to the operator's browser
- **Notification Triggers**: When certain conditions are met (completion, failure threshold reached), Celery workers send email or webhook notifications

After all 10 invoices are processed (8 successful, 2 failed with validation errors), the agent reports: "Execution completed. 8 succeeded, 2 failed. Logs uploaded."

**Step 9: Dashboard and Reporting**

The backend performs final updates:

- Marks the Run record as "Completed with Exceptions"
- Updates daily/weekly/monthly statistics
- Triggers a scheduled report generation showing processing metrics
- The **Business Operator** sees updated dashboard showing completion status
- The **Organization Admin** receives a summary email: "Daily invoice processing: 80% success rate, 2 items require manual review"

**Step 10: Exception Handling**

The operator reviews the 2 failed items:

- Opens the queue and filters for "Failed" status
- Reads error messages: "Invoice #1234: Vendor not found in master data"
- Corrects the data issue in the source system
- Marks items for reprocessing
- Starts another robot run to process only the corrected items

---
