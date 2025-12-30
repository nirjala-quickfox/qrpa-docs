
## 6. Vault (Secrets Management)

### Understanding Secure Credential Storage

**Vaults** provide enterprise-grade security for storing sensitive information like passwords, API keys, database connection strings, and certificates. Instead of hardcoding credentials in robot code or configuration files (a major security risk), robots request credentials from vaults at runtime, ensuring secrets remain encrypted and access-controlled.

#### Why Vaults Matter

**Security Risks Without Vaults:**  
- Credentials exposed in code repositories  
- Passwords visible in log files  
- Secrets stored in plain text configuration files  
- No audit trail of credential access  
- Difficult to rotate passwords without updating code  

**Benefits of Vault Storage:**  
- **Encryption at Rest**: All secrets encrypted in the database using strong encryption algorithms  
- **Encryption in Transit**: Secrets are re-encrypted specifically for the requesting robot using secure key exchange  
- **Access Control**: Only authorized robots and users can access specific vaults  
- **Audit Logging**: Every credential access is logged for compliance and security monitoring  
- **Centralized Management**: Update credentials in one place without modifying robot code  
- **Secret Rotation**: Change passwords/keys easily with minimal disruption  

#### Vault Structure

**Vault** (Container)  
- Named collection of secrets (e.g., "SAP_Credentials", "Email_Accounts", "API_Keys")  
- Belongs to a specific workspace  
- Has access permissions (which users/robots can read or modify)  
- Contains multiple key-value pairs  

**Vault Data** (Individual Secrets)  
- Key-value pairs stored within a vault  
- **Key**: Identifier for the secret (e.g., "sap_username", "sap_password", "api_token")  
- **Value**: The actual secret data (encrypted in database)  
- Can be text (passwords, tokens) or files (certificates, key files)  

**[Image Placeholder: Vaults Dashboard]**

### Creating and Managing Vaults

#### Creating a Vault

**Prerequisites:** Vault Management permissions in the target workspace

**1. Navigate to vaults**  
   - Click **"Security" → "Vaults"** in the main navigation  
   - Ensure you're in the correct workspace  

**2. Create new vault**  
   - Click **"+ New Vault"**  

**3. Define vault properties**  
   - **Vault Name**: Descriptive name indicating what secrets it contains (e.g., "Production_Database_Credentials")  
   - **Description**: Explain what secrets are stored and which robots use them  
   - **Workspace**: Confirm workspace assignment  

**4. Configure access permissions**  
   - **Read Access**: Which users/robots can retrieve secrets  
   - **Write Access**: Which users can add/modify/delete secrets  
   - **Admin Access**: Which users can manage vault permissions  

**5. *Set security policies** (optional)  
   - **Access Logging**: Enhanced logging of all secret retrievals (recommended for highly sensitive vaults)  
   - **Rotation Reminder**: Remind admins to rotate secrets every X days  

**6. Create the vault**  
   - Click **"Create Vault"**  
   - The vault is created and ready to store secrets  

**[Image Placeholder: Create Vault Form]**

#### Adding Secrets to a Vault

**1. Open the vault**  
   - From the vaults list, click the vault name  
   - Click the **"Secrets"** tab  

**2. Add a new secret**  
   - Click **"+ Add Secret"**  

**3. Provide secret information**  
   - **Secret Key**: Identifier for this secret (e.g., "database_password")  
     - Use clear, consistent naming conventions  
     - Avoid spaces (use underscores: "api_key" not "api key")  
   
   - **Secret Value**: The actual sensitive data  
     - For text secrets: Enter the password, token, or key directly  
     - For file secrets: Upload the certificate or key file  

   - **Secret Type**: Text or File  
   
   - **Description**: Optional note explaining what this secret is for  

**4. Save the secret**  
   - Click **"Add Secret"**  
   - The secret is encrypted and stored  
   - **Important**: The plain text value is never displayed again after initial creation  

**[Image Placeholder: Add Secret to Vault Form]**

#### Viewing and Updating Secrets

**Viewing Secret Keys**

**1. Open the vault**  
   - Navigate to the vault detail page  
   - The **"Secrets"** tab shows all secret keys in the vault  

**2. Secret list displays**  
   - **Key names**: Visible to authorized users  
   - **Value preview**: Masked (e.g., "••••••••") for security  
   - **Last modified date**: When the secret was last updated  
   - **Created by**: Who added the secret  

**Updating a Secret Value**

**1. Locate the secret**  
   - Find the secret key in the vault's secrets list  

**2. Update value**  
   - Click the **"Edit"** icon (✏️) next to the secret  
   - **New Value**: Enter the updated password, token, or upload new file  
   - **Update Reason**: Document why the secret changed (e.g., "Scheduled quarterly rotation", "Compromised credential")  

**3. Save changes**  
   - Click **"Update Secret"**  
   - The new value is encrypted and stored  
   - Old value is overwritten (not versioned, document elsewhere if version history is needed)  

**Rotating Secrets**  

Regular secret rotation is a security best practice:

**1. Plan rotation**  
   - Identify secrets that need rotation (expired certificates, passwords older than policy allows)  
   - Coordinate with system owners to ensure new credentials work  

**2. Update the secret in QuickRPA**  
   - Follow the update process above  
  
**3. Test robot access**  
   - Manually run a robot that uses this secret to verify it works  
   - Monitor logs for authentication errors  

**4. Document rotation**  
   - Log rotation date and reason in your security documentation  

**[Image Placeholder: Vault Secrets List]**

#### How Robots Access Secrets

Understanding the secure retrieval process helps you design secure automation:

**1. Robot Requests Secret**  
   - Robot (via agent) calls the API: "Get secret 'database_password' from vault 'Production_Database_Credentials'"  

**2. QuickRPA Validates Access**  
   - Verifies the robot has read permission for this vault  
   - Checks that the requesting agent is authorized  

**3. Secure Decryption & Re-Encryption**  
   - QuickRPA decrypts the secret from the database  
   - Generates a temporary encryption key specific to this robot/agent session  
   - Re-encrypts the secret using the temporary key  
   - Sends encrypted secret to the agent  

**4. Agent Decrypts & Uses**  
   - Agent decrypts the secret using the temporary key  
   - Provides the plain text secret to the robot for use  
   - Robot uses the credential (e.g., logs into a system)  
   - Secret is cleared from memory after use  

**5. Audit Logging**  
   - Access is logged: timestamp, robot, agent, secret key accessed  
   - Logs available for security review and compliance  

**Important Security Notes:**    
- Secrets are **never** transmitted or stored in plain text    
- Secrets are **never** written to logs (properly designed robots should never log secrets)    
- Each retrieval generates a unique encryption key    
- Access is logged for every retrieval    

**[Image Placeholder: Diagram of Secure Secret Retrieval Flow]**

#### Managing Vault Access

**1. View current permissions**  
   - Open vault detail page  
   - Click **"Permissions"** tab  
   - See users and robots with access  

**2. Grant access to a user**  
   - Click **"+ Add User Permission"**  
   - Select user from dropdown  
   - Choose permission level: Read, Write, or Admin  
   - Click **"Grant Access"**  

**3. Grant access to a robot**  
   - Click **"+ Add Robot Permission"**  
   - Select robot from dropdown  
   - Typically grant Read access only (robots shouldn't modify secrets)  
   - Click **"Grant Access"**  

**4. Revoke access**  
   - Find the user/robot in the permissions list  
   - Click **"Revoke"** button  
   - Confirm revocation  
   - Access is immediately removed  

**[Image Placeholder: Vault Permissions Management Screen]**

#### Best Practices

- **Vault Organization**: Create separate vaults for different systems or security levels (e.g., "Production_SAP", "Test_Environment", "API_Keys")
- **Least Privilege**: Grant Read access to robots, limit Write access to administrators only
- **Naming Conventions**: Use consistent, clear naming for secret keys (e.g., "{system}_{credential_type}" like "sap_username", "sap_password")
- **Regular Rotation**: Establish rotation schedules (e.g., every 90 days) and set reminders
- **Audit Reviews**: Periodically review access logs to identify unusual access patterns
- **No Logging**: Train developers never to log secret values, even for debugging
- **Backup Strategy**: Document secrets externally in a secure password manager as backup
- **Decommissioning**: When robots are retired, revoke their vault access promptly

---