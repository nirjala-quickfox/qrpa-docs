

## 7. Storage Buckets (Files)

### Understanding File Storage

**Storage Buckets** provide centralized file management for your automation ecosystem. Whether robots need to process PDFs, generate Excel reports, download source data, or upload results, storage buckets handle all file operations with proper organization and access control.

#### Storage Architecture

QuickRPA supports two storage backends to accommodate different deployment scenarios:

**Local Storage**  
- Files stored on the QuickRPA server's filesystem (within `MEDIA_ROOT` directory)  
Suitable for:  
  - Development and testing environments  
  - Small-scale deployments  
  - Environments where cloud storage isn't needed or allowed  
  - On-premises deployments with local disk capacity  

**Amazon S3 Storage**  
- Files stored in Amazon S3 buckets  
Suitable for:  
  - Production environments requiring scalability  
  - Multi-server or containerized deployments  
  - Organizations needing redundancy and durability  
  - Global deployments requiring low-latency access across regions  
  - Large file volumes or sizes  

**Configuration**: System administrators configure storage backend during QuickRPA setup. The backend is transparent to users—the experience is identical regardless of underlying storage.

#### Storage Bucket Structure

**Storage Bucket** (Container)  
- Named collection of files (e.g., "Invoice_PDFs", "Monthly_Reports", "Robot_Packages")  
- Belongs to a specific workspace  
- Has configuration: storage backend (local or S3), retention policies, access permissions  
- Contains multiple files (StorageBucketData)  

**Storage Bucket Data** (Individual Files)  
- Each file in a bucket is tracked as a separate record  
- **Metadata**: Filename, size, upload date, content type, uploader  
- **Location**: Storage path (abstracted—system handles whether file is local or S3)  
- **Permissions**: Who can access this file  
- **Versions**: Optionally track file versions (if versioning enabled)  

**[Image Placeholder: Storage Buckets Dashboard]**

### Creating and Managing Storage Buckets

#### Creating a Storage Bucket

**Prerequisites:** Storage Management permissions in the target workspace

**1. Navigate to storage buckets**  
   - Click **"Storage" → "Buckets"** in the main navigation  
   - Ensure you're in the correct workspace  

**2. Create new bucket**  
   - Click **"+ New Bucket"**  

**3. Define bucket properties**  
   - **Bucket Name**: Clear, descriptive name (e.g., "Invoice_Archives", "Robot_Outputs")  
   - **Description**: Explain the bucket's purpose and what files it contains  
   - **Workspace**: Confirm workspace assignment  

**4. Configure storage settings**  
   - **Storage Backend**: Local or S3 (if you have multiple configured)  
   - **S3 Bucket Name** (if S3): The actual AWS S3 bucket name  
   - **S3 Region** (if S3): AWS region for the bucket  
   - **S3 Access Keys** (if S3): AWS credentials for bucket access  

**5. Set retention policies** (optional)  
   - **Retention Period**: How long to keep files before automatic deletion (e.g., 90 days)  
   - **Archive After**: Move files to cheaper storage tier after X days (S3 only)  

**6. Configure access permissions**  
   - **Read Access**: Who can view and download files  
   - **Write Access**: Who can upload files  
   - **Delete Access**: Who can remove files (typically restricted to admins)  

**7. Create the bucket**  
   - Click **"Create Bucket"**  
   - The bucket is ready to receive files  

**[Image Placeholder: Create Storage Bucket Form]**

#### Uploading Files

**Single File Upload**

**1. Navigate to the bucket**  
   - Find your bucket in the buckets list  
   - Click its name to open the detail page  

**2. Upload file**
   - Click **"+ Upload File"**  

**3. Select file**  
   - **Choose File**: Browse and select file from your computer  
   - **File Description**: Optional notes about this file  

**4. Configure upload options**  
   - **Filename**: Optionally rename before upload  
   - **Overwrite**: If file with same name exists, choose to overwrite or create new version  

**5. Upload**  
   - Click **"Upload"**  
   - Progress bar shows upload status  
   - File appears in bucket file list when complete  

**[Image Placeholder: File Upload Interface]**

**Multiple File Upload**

**1. Access bulk upload**  
   - From bucket detail page, click **"Upload Multiple Files"**  

**2. Select files**  
   - Click **"Choose Files"** and select multiple files (Ctrl+Click or Cmd+Click)  
   - Or drag-and-drop files onto the upload area  

**3. Review file list**  
   - See all selected files with size and type  
   - Remove any files you don't want to upload  

**4. Upload all**  
   - Click **"Upload All Files"**  
   - Progress indicator shows overall progress and per-file status  

**[Image Placeholder: Bulk File Upload Interface]**

#### Managing Files in Buckets

**Viewing Bucket Contents**

**1. Open the bucket**  
   - Navigate to the bucket detail page  
   - The **"Files"** tab shows all files in the bucket  

**2. File list displays**  
   - **Filename**: Name of each file  
   - **Size**: File size (KB, MB, GB)  
   - **Type**: Content type/MIME type  
   - **Uploaded**: Date and time of upload  
   - **Uploaded By**: User or robot that uploaded the file  

**3. Sort and filter**  
   - **Sort**: By name, size, date, or type  
   - **Filter by Type**: Show only PDFs, Excel files, images, etc.  
   - **Search**: Find files by name  

**[Image Placeholder: Storage Bucket File List]**

**Downloading Files**

**1. Locate the file**  
   - Find the file in the bucket's file list  

**2. Download**  
   - Click the **"Download"** icon (⬇️) next to the file  
   - File downloads to your computer's default download location  

**3. Bulk download** (if needed)  
   - Select multiple files using checkboxes  
   - Click **"Bulk Actions" → "Download Selected"**  
   - Files download as a ZIP archive  

**Deleting Files**

**1. Select file(s) to delete**  
   - Use checkboxes to select one or more files  

**2. Delete**  
   - Click **"Delete Selected"** button  
   - Confirm deletion (this action cannot be undone)  

**3. Files are removed**  
   - Deleted files no longer appear in the bucket  
   - Storage space is freed immediately (local) or after S3 deletion processes  

**File Versioning** (if enabled)

Some buckets have versioning enabled, preserving previous versions of files:  

**1. View file versions**  
   - Click the file name to open its detail page  
   - **"Versions"** tab shows all versions with upload dates  

**2. Download specific version**  
   - Select the version you need  
   - Click **"Download This Version"**  

**3. Restore previous version**  
   - Select an older version  
   - Click **"Restore as Current"**  
   - This version becomes the active file  

**[Image Placeholder: File Detail Page with Version History]**

#### How Robots Use Storage

Robots interact with storage buckets through APIs during execution:

**Listing Files**

Robot code example concept:
```
files = storage.list_files(bucket="Invoice_PDFs", workspace="Finance")
# Returns list of available files in the bucket
```

**Downloading Files**

Robot retrieves a specific file:
```
file_data = storage.download_file(
    bucket="Invoice_PDFs",
    filename="invoice_12345.pdf",
    workspace="Finance"
)
# Robot receives file content to process
```

**Uploading Files**

Robot uploads generated output:
```
storage.upload_file(
    bucket="Robot_Outputs",
    filename="report_2025-01-15.xlsx",
    file_data=generated_report,
    workspace="Finance"
)
# File is stored in the bucket for users to download
```

**Use Cases:**  
- **Input Data**: Robots download source files (CSVs, PDFs, images) to process  
- **Output Results**: Robots upload generated reports, extracted data, screenshots  
- **Logging**: Detailed execution logs can be stored as files for long-term retention  
- **Artifacts**: Robots save intermediate files for debugging or audit purposes  

#### Storage Quotas and Monitoring

**1. View storage usage**  
   - From bucket detail page, see **"Storage Statistics"**  
   - Shows: Total files, total size, growth rate  

**2. Workspace-level quotas**  
   - Navigate to **"Workspace Settings" → "Storage"**  
   - View storage consumed vs. quota allocated  

**3. Set up alerts**  
   - Configure notifications when storage reaches thresholds (e.g., 80% of quota)  
   - **"Settings" → "Notifications" → "Storage Alerts"**  

**[Image Placeholder: Storage Usage Dashboard]**

#### Best Practices

- **Bucket Organization**: Create separate buckets for different purposes (inputs vs. outputs, by robot, by data type)
- **Naming Conventions**: Use clear, consistent filenames that robots can parse (e.g., `invoice_YYYYMMDD_ID.pdf`)
- **Retention Policies**: Automatically delete old files to control storage costs and maintain performance
- **Access Control**: Grant robots Read access only to input buckets, Write access only to output buckets
- **File Validation**: Have robots validate files before processing (check size, type, format)
- **Cleanup Automation**: Create maintenance robots that archive or delete old files based on business rules
- **Monitoring**: Regularly review storage usage to identify growth trends and potential issues

---
