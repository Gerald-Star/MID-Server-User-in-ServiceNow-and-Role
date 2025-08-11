# Creating a MID Server User in ServiceNow


## Communication Process Diagram

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
--












