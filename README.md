
# MID Server Application Communication Process in ServiceNow - Create a MID Server, Role and Validation (ITOM)
## How To Create a MID Server User in ServiceNow

The Management, Instrumentation, and Discovery (MID) Server is a sophisticated Java application designed to operate as a Windows service or a UNIX daemon within a server in an enterprise local area network (LAN). Its primary function is to facilitate robust communication and seamless data transfer between a ServiceNow instance and various external applications, data sources, devices, applications or services that reside within a customer's network (behind firewall). It's used by several applications, primarily in the IT Operations Management (ITOM) and Integration space.

**ServiceNow Mid Server Usage** 


--

```plaintext
                   +----------------------+
                   |  ServiceNow Instance |
                   |   (cloud-based)      |
                   +----------------------+
                             ▲
                             │ REST/SOAP
                             │
                      +-------------+
                      |  MID Server |
                      | (Windows/Linux)
                      +-------------+
                             │
        ┌────────────────────┼─────────────────────┐
        │                    │                     │
+---------------+   +----------------+   +-----------------+
| On-Prem Tools |   | Infrastructure |   | External APIs   |
| (e.g. SCCM,   |   | (e.g. Servers,  |   | (e.g. AWS, LDAP)|
| SolarWinds)   |   | Routers, etc.) |   |                 |
+---------------+   +----------------+   +-----------------+

```

For a strategic investment in ServiceNow Discovery, intending to identify and monitor various assets and services within its operational environment. To effectively accomplish this goal, it is crucial to have a validated and fully operational MID Server in place.

Once the MID Server is installed, configured, and linked to the ServiceNow instance, it must undergo a validation process. This step is critical, as only a validated MID Server can authenticate and access automation credentials or execute outbound ECC (External Communication Channel) probes, which are essential for discovering and interfacing with external systems.

---
**MID Server Installed in Customer Network/DMZ**
![MID Server Installed in Customer Network/DMZ](https://github.com/Gerald-Star/MID-Server-User-in-ServiceNow-and-Role/blob/main/MID%20Server%20Installed%20in%20Customer%20DMZ.png?raw=true)

**The MID Server is installed on-prem (within the customer’s network eg, in a DMZ or behind a firewall).**
**It executes commands and collects data on behalf of the instance.**
**It’s secure, runs as a Windows service or Linux daemon, and only initiates outbound communication.**
**It securely initiates outbound communication to ServiceNow and interacts with**
         -- Local/on-prem systems (SCCM, LDAP, AD)
         -- Network infrastructure (Windows/Linux servers, routers)
         -- Cloud services or external APIs (AWS, Azure, etc)


MID Servers play a pivotal role in ensuring smooth and efficient communication between ServiceNow and other external systems, enabling comprehensive data integration and automated workflows. By leveraging MID Servers, organizations can significantly enhance their IT service management capabilities, improving process efficiency and drastically reducing the reliance on manual efforts.

---
![What a MID Server Does](https://github.com/Gerald-Star/MID-Server-User-in-ServiceNow-and-Role/blob/main/What%20MID%20Server%20Does.png?raw=true)

Organizations frequently deploy MID Servers to establish connections with various on-premises databases, applications, and services, thereby streamlining operational workflows and improving overall data accessibility. This streamlined approach not only enhances productivity but also reduces the potential for errors that can occur during manual data handling.

IT professionals often collaborate and utilize best practices to optimize the deployment, operation, and management of MID Servers. This teamwork is essential in achieving enhanced service outcomes, ensuring that the organization can deliver high-quality IT services and respond effectively to business needs. By continuously monitoring and fine-tuning the performance of MID Servers, organisations can enhance their resilience and adaptability in IT operations.

---
## How To Create a MID Server User in ServiceNow

## MID Server Communication Process 

```plaintext
+-------------------+            +------------------------+
|  ServiceNow       |            |   MID Server Host      |
|  (Cloud Instance) |<---------->|   MID Server Service   |
+-------------------+   HTTPS    +------------------------+
         ^                                   |
         |                                   |  Protocols
         |                                   v
         |                          +--------------------+
         |                          |  Target Systems    |
         |                          |  (e.g., Servers,   |
         |                          |   Databases, etc.) |
         |                          +--------------------+
         |
         |   1. ServiceNow sends discovery/integration jobs to ECC Queue
         |   2. MID Server pulls jobs securely via `mid.user`
         |   3. MID Server executes jobs on target systems
         |   4. MID Server sends results back to ServiceNow ECC Queue

```
---

![Lab: Create MID server User and Role](https://github.com/Gerald-Star/MID-Server-User-in-ServiceNow-and-Role/blob/main/Create%20a%20MID%20Server%20User%20Add%20roles.png?raw=true)

---

## Steps to Create a MID Server User

### 1. Login as Admin
Log in to ServiceNow with an `admin` account.

---

### 2. Navigate to User Management
- Go to **User Administration > Users**  
- Or use search/filter: `sys_user.list`

---

### 3. Create New MID Server User
- Click **New**
- Fill in:
  - **User ID**: `mid.user`
  - **First name**: `MID`
  - **Last name**: `User`
  - **Password**: *(strong password, save for later use)*
  - **Active**: ✅
  - **Web service access only**: ✅

---

### 4. Assign Role
- In **Roles** related list → **Edit**
- Add:
  - `mid_server` ✅ *(Required)*
- Optional:
  - `import_transformer`
  - `ecc_queue_reader`
  - `ecc_queue_writer`

---

### 5. Save the User
Click **Save** or **Update**.

---

### 6. Use Account in MID Server Install
During MID Server installation:
- **Username**: `mid.user`
- **Password**: *(from step 3)*

---

###  Create via Script (optional)

```javascript
var user = new GlideRecord('sys_user');
user.initialize();
user.user_name = 'mid.user';
user.first_name = 'MID';
user.last_name = 'User';
user.password = 'StrongPassword123!';
user.active = true;
user.web_service_access_only = true;
var userID = user.insert();

var role = new GlideRecord('sys_user_has_role');
role.initialize();
role.user = userID;
role.role.setDisplayValue('mid_server');
role.insert();

```

![Mid Server user created](https://github.com/Gerald-Star/MID-Server-User-in-ServiceNow-and-Role/blob/main/MID%20Server%20User%20Created.png?raw=true)

---

This step is critical, as only a validated MID Server can authenticate and access automation credentials or execute outbound ECC (External Communication Channel) probes, which are essential for discovering and interfacing with external systems.

![Validate a MID Server]()


## ServiceNow Applications that Use a MID Server

```plaintext
              +-----------------------+
                          |  ServiceNow Instance  |
                          +----------+------------+
                                     |
                                 REST/SOAP
                                     |
                          +----------v-----------+
                          |      MID Server      |
                          |   (Windows/Linux)    |
                          +----------+-----------+
                                     |
     +----------------+-------------+---------------+------------------+
     |                |                             |                  |
+----v----+     +-----v-----+                 +-----v-----+      +-----v-----+
|Discovery|     |Orchestration|               |Cloud Mgmt|      |Event Mgmt |
+---------+     +-----------+                 +-----------+      +-----------+
     |                |                             |                  |
     |         +------v-------+             +--------v------+   +------v------+
     |         | LDAP/SCCM    |             | AWS, Azure    |   | Monitoring   |
     |         +--------------+             +---------------+   | Tools (e.g.  |
     |                                                       |   | SolarWinds) |
     |                                                       +---------------+
     |
+----v-----+
| Service  |
| Mapping  |
+----------+
     |
+----v-----+        +------------+
|   CMDB   |<-------+ Integration|
+----------+        +------------+
                       |
               +-------v--------+
               | IntegrationHub |
               | (Spokes, APIs) |
               +----------------+

```

| Application/Feature                   | Purpose of MID Server                         |
| ------------------------------------- | --------------------------------------------- |
| **Discovery**                         | Scans and identifies infrastructure (CI data) |
| **Service Mapping**                   | Maps services to infrastructure               |
| **Orchestration**                     | Runs scripts/workflows on remote systems      |
| **Cloud Management**                  | Integrates with AWS, Azure, etc. via APIs     |
| **Event Management (via connectors)** | Gathers events from monitoring tools          |
| **IntegrationHub (Spokes)**           | Connects to systems like SAP, LDAP, etc.      |
| **Software Asset Management (SAM)**   | For fetching data from SCCM or other tools    |
| **Hardware Asset Management (HAM)**   | Integrates with discovery sources like SCCM   |
| **CMDB**                              | Populated through Discovery/Integrations      |
| **LDAP Integration**                  | Connects to local directory services          |
| **SCCM Integration**                  | Imports inventory and software data           |


---









