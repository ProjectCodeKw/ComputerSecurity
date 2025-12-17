Table of Contents:
1. Access Control
2. Discretionary Access Control (DAC)
	1. Matrix
	2. Access Control List (ACL)
	3. Capability Lists
3. UNIX Access Control
4. Role-Based Access Control (RBAC)
5. Attribute-Based Access Control (ABAC)
_____________

**Main 3 Pillars of Access Control:**
1. Prevent Un-auth users from accessing resources
2. Prevent legit users from accessing un-authorized resources
3. Make users only access their auth. resources

***Access Control Requirements***
- Least privilege
- Separation of duty: divide functions among multiple individuals

### POLICIES

**(1) Discretionary access control (DAC)**
Based on the requester *ID* and the rules they can do.
	`Called discretionary because an entity might enable another entity to access some resource`

**(2) Mandatory access control (MAC)**
Based on *comparing security labels* with security clearance (used in trusted/military systems).

**(3) Role-based access control**
Based on the *roles* that users have and what they can do.

**(4) Attribute-based access control (ABAC)**
Based on the *attributes* the user has.

_________________

### Discretionary Access Control (DAC)
	How rules and authentication tokens are stored (privileges) in a system.

<center>

![[Pasted image 20251216025959.png | 400]]

</center>

#### 1. Access Matrix
	The full 2D table showing all subjects (rows) and objects (columns).

**Advantages:**
- Simple to explain
- Complete view of permissions
- Easy Authorization Queries 

**Disadvantages:**
- Sparse and wasteful (most cells are empty)
- Does not Scale
- Hard to update

#### 2. Access Control Lists (ACL) - Column-wise
	Store permissions **per file** (each column becomes a list).

 **Advantages:**
- Space efficient: Only stores actual permissions (no empty entries)
- Natural for file systems
- Good for protection: File-centric security model
- Easy to revoke access to a file: Remove all entries for that file
- **Easy to answer:** "Who can access this file?"

**Disadvantages:**
- Difficult user revocation: Must search all ACLs to remove a user
- Redundancy!!!!: Same user appears in many ACLs
- Performance: Checking access requires reading file's ACL
- **Hard to answer**: "What files can User X access?" (must scan all files)

#### 3. Capability Lists - Row-wise
	Store permissions **per user** (each row becomes a list).

 **Advantages:**
- Space efficient: Only stores actual permissions
- Easy delegation: User can pass capabilities to others
- Efficient access checking: User carries their own capabilities
- Fast revocation of user: Delete user's capability list
- **Easy to answer:** "What can User X access?"

**Disadvantages:**
- Difficult to revoke access to files: Must search all capability lists
- Forgery risk: Capabilities are like "tickets" - must be protected from tampering
- Propagation control: Hard to track who shared capabilities with whom
- Revocation complexity: Copied capabilities are hard to invalidate
- **Hard to answer:** "Who can access this file?" (must scan all users)

| **Question/Operation**        | **Matrix** | **ACL (b)** | **Capabilities (c)** |
| ----------------------------- | ---------- | ----------- | -------------------- |
| **"What can User X access?"** | **Easy**   | *Hard*      | **Easy**             |
| **"Who can access File Y?"**  | **Easy**   | **Easy**    | *Hard*               |
| **Space efficiency**          | Poor       | Good        | Good                 |
| **Revoke user's access**      | **Easy**   | *Hard*      | Easy                 |
| **Revoke access to file**     | **Easy**   | **Easy**    | *Hard*               |
| **Implementation complexity** | Simple     | Medium      | Complex              |
| **Forgery protection needed** | No         | No          | Yes                  |
| **Real-world usage**          | None       | Common      | Rare                 |
#### EXTENDED Access Control Model
	Goes beyond just files, it includes all system resources.

| **Object Type**   | **Examples**                         | **Access Rights**                                 |
| ------------- | -------------------------------- | --------------------------------------------- |
| **Processes** | Running programs, tasks, threads | - Delete process                              |
|               |                                  | - Stop/Block process                          |
|               |                                  | - Wakeup/Resume process                       |
|               |                                  |                                               |
| **Devices**   | Disk drives, printers, network   | - Read from device                            |
|               | interfaces, USB devices          | - Write to device                             |
|               |                                  | - Control operations (e.g., disk seek, eject) |
|               |                                  | - Block/Unblock device for use                |
|               |                                  |                                               |
| **Memory**    | RAM locations, memory regions,   | - Read memory locations/regions               |
|               | address spaces, segments         | - Write memory locations/regions              |
|               |                                  | - Default: Access NOT allowed (protected)     |
|               |                                  |                                               |
| **Subjects**  | Users, processes acting on       | - Grant access rights to objects              |
|               | behalf of users                  | - Delete/Revoke access rights from objects    |
|               |                                  | - Modify subject's own permissions            |

_______________________________

### UNIX Access Control Concepts
	UNIX file access control is the **security system** that determines **who can do what** with files and resources on a UNIX/Linux system

**Main User Privileges:**
- RWX: Owner, they can read write and execute
- RW-: only read and write
- ---: do nothing

| Concept            | Explanation                                                                             |
| ------------------ | --------------------------------------------------------------------------------------- |
| **Inode**          | Data structures that store all file metadata (permissions, ownership, size, timestamps) |
| **Stores**         | Attributes, permissions, timestamps, file size, pointers                                |
| **Multiple names** | Same inode can have several filenames (hard links)                                      |
| **Inode table**    | On-disk table listing all files on a disk, or copied to memory when disk is mounted     |
Every file has **12 protection bits** (rwxrwxrwx+3 special bits) stored in its inode. These are divided into **three classes of users**:
- **Owner** (first rwx): The person who created the file. Can read, write, and execute.
- **Group** (second rwx): Members of the file's group. Get whatever permissions the owner grants them.
- **Others** (third rwx): Everyone else on the system.

| **3 Special Bits** | **Description**                                                               |
| -------------- | ------------------------------------------------------------------------- |
| **SetGID**     | Program runs with file's group permissions                                |
| **Sticky bit** | On directories: only file owner can delete their own files (used in /tmp) |
| **Superuser**  | Root (UID 0) bypasses all access control                                  |
>Table shows how non-root users can run programs that need root access.

#### Extended ACLs: Going Beyond Owner/Group/Other

BEHOLD... ***The Mask***: When you add named users/groups, the **group class becomes a mask**. This mask defines the **maximum permissions** that any named user or group can have. Even if you grant a named user rwx, if the mask only allows r-x, the user effectively gets r-x (*Any permission not in the mask is automatically blocked*).

**How ACLs work**:
1. Owner and Other classes work the same as traditional UNIX
2. Named users and named groups each get 3-bit rwx permissions
3. The *group class* becomes the mask that caps all named permissions
4. Effective permission = ACL permission AND mask permission

___________________________
### Role-Based Access Control (RBAC)

Instead of giving permissions directly to individual users (like *"Alice can read file X"*), RBAC assigns permissions to *roles* (like "Manager" or "Doctor"), and then assigns users to those roles.

**In RBAC matrix representation it leads to *principle of least privilege* why?**
1. Fewer roles than objects, better permission management 
2. Easier to audit and maintain 
3. Prevents permission creep: when someone changes jobs, you just change their role assignment - they automatically lose old permissions and gain new ones.

>[!warning] Ok now MEMORIZATION HELL starts... GL!

#### (1) RBAC models

| **RBAC Model** | **Description**        | **Key Features**                                                                        |
| ---------- | ------------------ | ----------------------------------------------------------------------------------- |
| **RBAC₀**  | Base model         | Minimum functionality - users, roles, permissions, sessions                         |
| **RBAC₁**  | Role hierarchies   | Adds inheritance - senior roles inherit permissions from junior roles               |
| **RBAC₂**  | Constraints        | Adds restrictions: mutually exclusive roles, cardinality limits, prerequisite roles |
| **RBAC₃**  | Consolidated model | Combines RBAC₀ + RBAC₁ + RBAC₂ (full functionality)                                 |
### (2) Basic entities of RBAC

| **RBAC Entity**    | **Definition**                                                                                   |
| -------------- | -------------------------------------------------------------------------------------------- |
| **User**       | Individual with access to the system                                                         |
| **Role**       | Named job function within the organization that controls system access                       |
| **Permission** | Approval for a particular mode of access to objects (access right, privilege, authorization) |
| **Session**    | Mapping between a user and an activated subset of their assigned roles                       |
### (3) Role hierarchies

| **RBAC Feature**         | **Explanation**                                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Role Hierarchies** | Upper roles inherit all permissions from lower roles; enables one role to inherit from multiple subordinate roles |
| **Constraints**      | Rules that adapt RBAC to organizational policies                                                                  |
### (4) Constraints - RBAC
	A defined relationship among roles or a condition related to roles

| **Constraint Type**              | **Purpose**                                                              | **Example**                                        |
| ---------------------------- | -------------------------------------------------------------------- | ---------------------------------------------- |
| **Mutually exclusive roles** | User can only be assigned one role from a set (*prevents collusion*) | User is either Accountant OR Auditor, not both |
| **Cardinality**              | Limits maximum number of users for a role                            | Only one Department Head allowed               |
| **Prerequisite roles**       | User must have Role A before getting Role B                          | Must be Engineer before becoming Project Lead  |

____________

### Attribute-Based Access Control (ABAC)

ABAC makes access decisions by evaluating **multiple properties (attributes)** from different sources, not just who you are or what your role is.

**RBAC is like**: "Are you a Manager? Yes? Come in."

**ABAC is like**: "Are you a Manager? Is it during business hours? What's your ELO? Are you on company premises? Are you accessing a financial document? Is your security clearance level 3 or higher? Is the document classified as sensitive?"

#### (1) ABAC Key Features

| **Aspect**             | **Description**                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------ |
| **Key Feature**    | Defines authorization based on conditions/properties of BOTH resource AND subject                |
| **Strength**       | Flexibility and expressive power                                                                 |
| **Main Challenge** | Performance impact of evaluating predicates on both resource and user properties for each access |
| **Technology**     | eXtensible Access Control Markup Language (XACML) - pioneered by web services                    |
| **Current Trend**  | Considerable interest in applying to *cloud services*                                            |
#### (2) ABAC Three Attribute Types

| **Attribute Type**  | **What It Describes**                             | **University Library Examples**                                                                                                    |
| --------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Subject**     | Who is requesting access (the active entity)  | Student/Faculty/Staff, Year (Freshman/Sophomore), Department (CS/Math), Access level (Admin/Regular)                           |
| **Object**      | What is being accessed (the passive resource) | Resource type (Book/Journal/Computer), Classification (Reference/Restricted/Open Access), Availability (Available/Checked Out) |
| **Environment** | Context/situation of the access attempt       | Time (Weekday/Weekend/After Hours), Location (On-campus/Off-campus), Device type (Mobile/Laptop)                               |

#### (3) RBAC vs ABAC Comparison

| **Dimension**           | **RBAC**                                                                    | **ABAC**                                                                  |
| ----------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **What it checks**      | "What role do you have?"                                                    | "What are your attributes + resource attributes + environmental context?" |
| **Scope**               | role-level                                                                  | attribute-level                                                           |
| **Context awareness**   | None (doesn't consider time/location/conditions)                            | High (explicitly includes environmental attributes)                       |
| **Flexibility**         | Limited to predefined roles                                                 | Very flexible - can express complex conditional policies                  |
| **Policy complexity**   | Simple - easy to understand                                                 | Complex - many attributes and combinations                                |
| **Performance**         | Fast (simple role lookup)                                                   | Slower (must evaluate multiple predicates)                                |
| **Dynamic conditions**  | Cannot express "only during business hours" or "only from secure locations" | Built-in support for time, location, device, etc.                         |
| **Resource properties** | Ignored (doesn't look at what you're accessing)                             | Central (explicitly considers object attributes)                          |
| **Scalability**         | Good (fewer roles than users)                                               | Challenging (many attributes to manage and evaluate)                      |
| **Best use case**       | Organizations with stable job roles                                         | Dynamic environments needing context-dependent access                     |
| **Management overhead** | Low - assign users to roles                                                 | Higher - maintain many attributes and complex policies                    |