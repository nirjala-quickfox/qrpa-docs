
## 5. Queues & Queue Items

### Understanding Queue-Based Processing

**Queues** enable transactional, fault-tolerant automation by breaking large workloads into individual items that robots process one-by-one. This approach provides resilience, parallel processing, and precise error tracking—essential for high-volume automation.

#### Why Use Queues?

Traditional automation runs sequentially—if processing 1,000 invoices and an error occurs on invoice #500, you might lose track of which items succeeded and which failed. Queues solve this by treating each invoice as a separate item with its own status tracking.

**Benefits:**
- **Granular Status Tracking**: Know exactly which items succeeded, failed, or are still pending
- **Automatic Retry**: Failed items can be automatically retried without reprocessing successful ones
- **Parallel Processing**: Multiple robots can process different items from the same queue simultaneously
- **Priority Handling**: Mark certain items as high-priority to process them first
- **Work Distribution**: Intelligently distribute work across available agent machines
- **Resilience**: If a robot crashes, only the current item is affected—others remain safe
- **Progress Visibility**: Real-time monitoring of queue depth and processing rates

#### Queue Structure

**Queue** (Container)
- Named collection of work items (e.g., "Invoices_To_Process", "Customer_Onboarding_Requests")
- Belongs to a specific workspace
- Has configuration: retry policies, item timeout, priority rules
- Can have multiple robots consuming from it

**Queue Item** (Individual Work Unit)
- Represents one piece of work (one invoice, one customer record, one file to process)
- Contains data needed for processing (JSON payload with business data)
- Has a lifecycle status: NEW → PROCESSING → DONE/FAILED/ARCHIVED
- Tracks processing attempts, error messages, and processing duration
- Can have priority levels (1-5, where 1 is lowest, 5 is highest)

**[Image Placeholder: Queue Dashboard Showing Multiple Queues]**

### Creating and Managing Queues

#### Creating a Queue

**Prerequisites:** Queue Management permissions in the target workspace

1. **Navigate to queues**
   - Click **"Queues"** in the main navigation
   - Ensure you're in the correct workspace

2. **Create new queue**
   - Click **"+ New Queue"**

3. **Define queue properties**
   - **Queue Name**: Descriptive, unique name (e.g., "Daily_Invoice_Processing")
   - **Description**: Explain what this queue is for and what data items contain
   - **Workspace**: Confirm the workspace assignment

4. **Configure processing rules**
   - **Max Retries**: How many times to retry failed items (e.g., 3)
   - **Retry Delay**: Wait time between retries (e.g., 5 minutes)
   - **Item Timeout**: Maximum time to process one item (e.g., 10 minutes)
   - **Processing Order**: FIFO (first in, first out), Priority-based, or LIFO

5. **Set queue limits** (optional)
   - **Maximum Queue Size**: Cap on total items (prevents queue overflow)
   - **Warning Threshold**: Get notified when queue depth exceeds this number

6. **Create the queue**
   - Click **"Create Queue"**
   - The queue is now ready to receive items

**[Image Placeholder: Create Queue Form]**

#### Adding Queue Items

**Single Item Addition**

1. **Navigate to the queue**
   - Find your queue in the queues list
   - Click its name to open the detail page

2. **Add item**
   - Click **"+ Add Item"**

3. **Provide item data**
   - **Item Name**: Unique identifier (e.g., "Invoice_12345")
   - **Data (JSON)**: The business data needed for processing
   
   Example:
   ```json
   {
     "invoice_number": "INV-2024-12345",
     "vendor": "Acme Supplies",
     "amount": 1500.00,
     "due_date": "2025-01-15",
     "account_code": "5000-100"
   }
   ```

4. **Set priority** (optional)
   - **Priority**: 1 (low) to 5 (high)
   - Higher priority items are processed first

5. **Add the item**
   - Click **"Add Item"**
   - The item appears in the queue with status NEW

**[Image Placeholder: Add Single Queue Item Form]**

**Bulk Item Upload**

For high-volume processing, upload multiple items at once from a file:

1. **Prepare your data file**
   - **CSV Format**: Create a CSV with columns matching your queue item schema
   - **Excel Format**: Create an Excel file with headers in the first row
   - **JSON Format**: Create a JSON array with item objects

   Example CSV:
   ```
   item_name,invoice_number,vendor,amount,due_date,priority
   INV_001,INV-2024-12345,Acme Supplies,1500.00,2025-01-15,3
   INV_002,INV-2024-12346,Widget Corp,2300.00,2025-01-20,
   2
   INV_003,INV-2024-12347,Supply Co,750.00,2025-01-10,5
   ```

2. **Navigate to bulk upload**
   - From the queue detail page, click **"Bulk Actions" → "Upload Items"**

3. **Upload file**
   - **Select File**: Choose your CSV, Excel, or JSON file
   - **Column Mapping**: Map file columns to queue item fields
   - **Preview**: Review first few items to verify correct mapping

4. **Configure upload options**
   - **Duplicate Handling**: Skip duplicates or overwrite existing items
   - **Default Priority**: For items without specified priority
   - **Validation**: Enable data validation before upload

5. **Upload items**
   - Click **"Upload Items"**
   - Progress bar shows upload status
   - Summary report shows: Items added, duplicates skipped, validation errors

**[Image Placeholder: Bulk Upload Queue Items Interface]**

#### Processing Queue Items (Robot Perspective)

While operators manage queues through the UI, robots access queue items through APIs. Here's what happens:

1. **Robot Requests Work**
   - Robot (via agent) calls the API: "Give me the next item from queue X"
   - QuickRPA returns the highest-priority NEW item and marks it PROCESSING

2. **Robot Processes Item**
   - Robot reads the item's data JSON
   - Executes business logic (validates invoice, enters data into system, etc.)
   - May take seconds or minutes depending on complexity

3. **Robot Reports Result**
   - **Success**: Robot marks item DONE via API, optionally uploads result data
   - **Failure**: Robot marks item FAILED via API, includes error message

4. **Automatic Retry (if configured)**
   - If item FAILED and retry count < max retries, QuickRPA automatically:
     - Waits for the retry delay period
     - Returns item to NEW status
     - Item becomes available for processing again

**[Image Placeholder: Queue Item Lifecycle Diagram]**

#### Monitoring Queue Items

1. **View queue contents**
   - Open the queue's detail page
   - Click the **"Items"** tab
   - See all items with their current status

2. **Filter items**
   - **By Status**: NEW, PROCESSING, DONE, FAILED, ARCHIVED
   - **By Priority**: Show high-priority items first
   - **By Date**: Items added in specific time range
   - **By Search**: Find items by name or data content

3. **Inspect item details**
   - Click any item to see:
     - Full data payload
     - Processing history (all attempts)
     - Error messages (if failed)
     - Processing duration
     - Assigned robot/agent

4. **View processing statistics**
   - **Queue Metrics Dashboard** shows:
     - Total items: NEW, PROCESSING, DONE, FAILED
     - Processing rate (items per hour)
     - Average processing time
     - Success rate percentage
     - Current queue depth

**[Image Placeholder: Queue Items List with Status Filters]**

#### Managing Failed Items

1. **Identify failed items**
   - Filter queue items by **Status: FAILED**
   - Items that exceeded max retries appear here

2. **Review failure reason**
   - Click the failed item
   - Read **Error Message** and **Error Details**
   - Check **Processing Attempts** to see retry history

3. **Determine corrective action**
   - **Data Issue**: Edit the item's data to correct the problem
   - **Temporary System Issue**: Reset the item to retry
   - **Invalid Item**: Mark as archived or delete

4. **Edit item data**
   - Click **"Edit Item"**
   - Modify the JSON data to fix the issue
   - Click **"Save"**

5. **Reset for reprocessing**
   - Click **"Actions" → "Reset to NEW"**
   - Item becomes available for processing again
   - Retry counter resets

6. **Archive item**
   - For items that cannot or should not be processed
   - Click **"Actions" → "Archive"**
   - Add archive reason (e.g., "Duplicate entry", "Invalid vendor")
   - Item moves out of active queue but remains for audit

**[Image Placeholder: Failed Queue Item Detail Page]**

#### Best Practices

- **Meaningful Item Names**: Use unique identifiers that help you locate items (e.g., invoice numbers, customer IDs)
- **Clean Data Structure**: Keep JSON payloads organized and documented—include only necessary data
- **Priority Strategy**: Reserve high priority (4-5) for truly urgent items to maintain meaningful prioritization
- **Regular Cleanup**: Archive or delete processed items periodically to maintain performance
- **Monitoring Alerts**: Set up notifications when queues exceed threshold depths or failure rates spike
- **Idempotency**: Design robots to handle duplicate processing gracefully in case an item is processed twice
- **Batch Sizing**: For very large workloads (10,000+ items), consider breaking into multiple queues by criteria (date, region, etc.)

---
