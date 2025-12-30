
## 2. Users, Roles & Permissions

### Understanding Access Control

QuickRPA implements a robust role-based access control (RBAC) system that ensures users have exactly the permissions they need—nothing more, nothing less. This security model protects sensitive data while enabling efficient collaboration.

#### User Accounts

**Users** are individuals who interact with QuickRPA through the web interface, API, or mobile applications.

**User Properties:**  
- Belong to one organization  
- Can have access to one or more workspaces within that organization  
- Authenticate using username/password, LDAP, or OIDC  
- Have assigned roles that determine their permissions  
- Can be active or inactive (for access suspension without deletion)  

#### Authentication Methods

QuickRPA supports multiple authentication mechanisms to integrate with your existing identity infrastructure:

**Username/Password Authentication**  
- Traditional credential-based login  
- Passwords are securely hashed and never stored in plain text  
- Supports password reset workflows  
- Ideal for small teams or standalone deployments  

**LDAP Integration**  
- Connects to Active Directory or other LDAP-compliant directory services  
- Users authenticate with their existing corporate credentials  
- Reduces password fatigue and improves security  
- Enables centralized user management  
- Perfect for enterprises with established directory services  

**OIDC (OpenID Connect)**  
- Modern federated authentication protocol  
- Supports single sign-on (SSO) with identity providers like Okta, Auth0, Azure AD  
- Users log in once and access multiple systems seamlessly  
- Provides enhanced security with token-based authentication  
- Ideal for cloud-first organizations and those requiring SSO  

#### Roles & Permissions

**Roles** are collections of permissions that define what actions users can perform. Instead of assigning permissions individually to each user, you assign roles—making access management scalable and maintainable.

**Permission Granularity:**

QuickRPA enforces fine-grained permissions across nearly every API endpoint, ensuring comprehensive security:

- **Robot Permissions**: Can view robots, can edit robots, can delete robots, can execute robots, can create robot versions
- **Queue Permissions**: Can view queues, can create queue items, can modify queue items, can delete queues
- **Vault Permissions**: Can view vault contents, can create secrets, can update secrets, can delete vaults
- **Workspace Permissions**: Can manage workspace settings, can add users to workspace, can remove users
- **Organization Permissions**: Can manage organization settings, can create workspaces, can manage billing
- **System Permissions**: Can access system administration, can manage integrations, can view audit logs

### Managing Users, Roles & Permissions

**[Image Placeholder: User Management Dashboard]**

#### Creating a New User

**Prerequisites:** Organization Administrator or User Management permissions

1. **Access user management**
   - Navigate to **"Administration" → "Users"**

2. **Initiate user creation**
   - Click **"+ Add User"** button

3. **Enter user information**
   - **Full Name**: User's complete name
   - **Email Address**: Primary contact and potentially username
   - **Username**: Login identifier (if different from email)
   - **Authentication Method**: Choose username/password, LDAP, or OIDC

4. **Set organizational membership**
   - **Organization**: Select the organization this user belongs to
   - **Primary Workspace**: Assign the user's default workspace

5. **Assign roles**
   - Click **"+ Assign Role"**
   - Select appropriate roles from the dropdown (e.g., "Robot Developer", "Business Operator")
   - Add multiple roles if needed

6. **Configure workspace access**
   - In the **"Workspace Access"** section, click **"+ Add Workspace"**
   - Select each workspace the user should access
   - Define permissions for each workspace (View, Edit, Execute)

7. **Complete user creation**
   - Click **"Create User"**
   - If using username/password, optionally **"Send Welcome Email"** with temporary credentials

**[Image Placeholder: Create User Form]**

#### Managing Roles

**Prerequisites:** Organization Administrator or Roles Management permissions

1. **Navigate to roles management**
   - Go to **"Administration" → "Roles"**

2. **View existing roles**
   - Review the list of available roles and their descriptions
   - Click any role to see its assigned permissions

3. **Create a custom role**
   - Click **"+ Create Role"**
   - **Role Name**: Descriptive name (e.g., "Finance Robot Developer")
   - **Description**: Explain who should have this role and why
   
4. **Assign permissions to the role**
   - Browse through permission categories (Robots, Queues, Vaults, etc.)
   - Check each permission this role should include
   - Use **"Select All"** for administrative roles or granularly select specific permissions

5. **Save the role**
   - Click **"Create Role"**
   - The role is now available for assignment to users

**[Image Placeholder: Create Role Form with Permissions Checklist]**

#### Configuring LDAP Authentication

**Prerequisites:** System Administrator role

1. **Access authentication settings**
   - Navigate to **"System Settings" → "Authentication"**
   - Select the **"LDAP"** tab

2. **Configure LDAP connection**
   - **LDAP Server URL**: Your directory server address (e.g., `ldap://ldap.company.com:389`)
   - **Bind DN**: Service account for QuickRPA (e.g., `cn=quickrpa,ou=service,dc=company,dc=com`)
   - **Bind Password**: Service account password
   - **Base DN**: Where to search for users (e.g., `ou=users,dc=company,dc=com`)

3. **Configure user mapping**
   - **Username Attribute**: LDAP field for username (typically `sAMAccountName` or `uid`)
   - **Email Attribute**: LDAP field for email (typically `mail`)
   - **Full Name Attribute**: LDAP field for name (typically `displayName` or `cn`)

4. **Test connection**
   - Click **"Test Connection"** to verify settings
   - Attempt a test login with an LDAP user account

5. **Enable LDAP authentication**
   - Toggle **"Enable LDAP Authentication"** to active
   - Click **"Save Settings"**

**[Image Placeholder: LDAP Configuration Screen]**

#### Configuring OIDC Authentication

**Prerequisites:** System Administrator role

1. **Access OIDC settings**
   - Navigate to **"System Settings" → "Authentication"**
   - Select the **"OIDC"** tab

2. **Register QuickRPA with your identity provider**
   - Log into your OIDC provider (Okta, Azure AD, Auth0, etc.)
   - Create a new application registration for QuickRPA
   - Note the **Client ID** and **Client Secret** provided
   - Configure the **Redirect URI** (QuickRPA will display the correct URL to use)

3. **Configure OIDC in QuickRPA**
   - **Provider Name**: Friendly name (e.g., "Okta SSO")
   - **Client ID**: From your identity provider
   - **Client Secret**: From your identity provider
   - **Discovery URL**: Your OIDC provider's discovery endpoint (e.g., `https://your-tenant.okta.com/.well-known/openid-configuration`)

4. **Configure claim mapping**
   - **Username Claim**: Which token claim contains username (typically `preferred_username` or `email`)
   - **Email Claim**: Which claim contains email (typically `email`)
   - **Name Claim**: Which claim contains full name (typically `name`)

5. **Test and enable**
   - Click **"Test OIDC Flow"** to verify configuration
   - Toggle **"Enable OIDC Authentication"**
   - Click **"Save Settings"**

**[Image Placeholder: OIDC Configuration Screen]**

#### Best Practices

- **Principle of Least Privilege**: Grant users only the permissions necessary for their job function
- **Regular Audits**: Periodically review user permissions and remove unnecessary access
- **Role Standardization**: Create standard roles for common job functions to maintain consistency
- **Segregation of Duties**: Ensure no single user has excessive control (e.g., separate deployment and approval permissions)
- **Onboarding/Offboarding**: Implement processes to promptly grant access to new employees and revoke access when they leave

---
