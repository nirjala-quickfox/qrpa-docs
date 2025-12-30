## 1. Organizations & Workspaces

### Understanding the Hierarchy

QuickRPA uses a two-tier organizational structure that provides flexibility and logical separation for different business units, clients, or projects.

#### Organizations

An **Organization** represents the top-level entity in QuickRPA's hierarchy—typically your company, a major division, or an individual client if you're providing automation services.

**Key Characteristics:**  
- Acts as the primary container for all automation assets  
- Provides complete data isolation from other organizations  
- Establishes billing and licensing boundaries  
- Represents the highest level of administrative control  

**Common Use Cases:**  
- **Single Company**: One organization representing your entire company  
- **Multi-Client Service Provider**: Separate organizations for each client you serve  
- **Large Enterprise**: Different organizations for major business divisions (e.g., North America, Europe, APAC)

#### Workspaces

A **Workspace** is a sub-division within an organization, providing granular separation for teams, projects, or functional areas.

**Key Characteristics:**  
- All automation resources (robots, queues, vaults, storage buckets) belong to a specific workspace  
- Enables project-based or team-based organization  
- Allows different permission sets for different areas  
- Users can have access to multiple workspaces within their organization

**Common Use Cases:**  
- **Department-Based**: Finance Workspace, HR Workspace, Operations Workspace  
- **Project-Based**: Invoice Processing Project, Customer Onboarding Project  
- **Environment-Based**: Development Workspace, Testing Workspace, Production Workspace  
- **Client Service Model**: Different workspaces for different client projects within your organization

### Setting Up Organizations & Workspaces

**[Image Placeholder: Organizations & Workspaces Dashboard]**

#### Creating an Organization

**Prerequisites:** System Administrator or Organization Administrator role

**1. Navigate to the Organizations section**  
   - Click on **"Administration"** in the main navigation menu  
   - Select **"Organizations"** from the submenu  

**2. Initiate organization creation**  
   - Click the **"+ New Organization"** button in the top-right corner  

**3. Complete the organization form**  
   - Enter **Organization Name** (e.g., "Acme Corporation")  
   - Provide an optional **Description** explaining the organization's purpose  
   - Set **Organizational Settings** such as timezone and default language  
   - Configure **License Allocation** if applicable  

**4. Save the organization**  
   - Click **"Create Organization"** to finalize  
   - You'll be redirected to the organization's detail page  

**[Image Placeholder: Create Organization Form]**

#### Creating a Workspace

**Prerequisites:** Organization Administrator role for the target organization

**1. Navigate to the Workspaces section**  
   - From your organization's dashboard, click **"Workspaces"** in the navigation  
   - Alternatively, go to **Administration → Workspaces**  

**2. Start workspace creation**  
   - Click the **"+ New Workspace"** button  

**3. Fill in workspace details**  
   - **Workspace Name**: Descriptive name (e.g., "Finance Automation")  
   - **Parent Organization**: Select the organization this workspace belongs to  
   - **Description**: Explain the workspace's purpose and scope  
   - **Workspace Settings**: Configure default robot execution settings  

**4. Configure access settings**  
   - Set **Default Permissions** for workspace users  
   - Configure **Visibility Settings** (who can see this workspace)  

**5. Create the workspace**  
   - Click **"Create Workspace"** to complete setup  
   - You'll see the new workspace in your workspaces list  

**[Image Placeholder: Create Workspace Form]**

#### Best Practices

- **Plan Your Structure**: Before creating multiple workspaces, map out your organizational structure to avoid confusion later
- **Naming Conventions**: Use clear, consistent naming schemes (e.g., "Department_Function" format)
- **Start Simple**: Begin with fewer workspaces and add more as needs become clear
- **Regular Review**: Periodically audit workspaces and archive those no longer in use

---
