# DBML (Database Markup Language)

ER Diagram - https://dbdiagram.io/d/673d28aae9daa85aca093c4d

![Cyber Threat Monitoring Dashboard ER Diagram](er-diagram.png?raw=true "ER Diagram")

```
// Users Table
Table users {
  id integer [primary key]
  username varchar [unique, note: 'Username of the user']
  email varchar [unique, note: 'Email address']
  password varchar
  role varchar [note: 'Role of the user, e.g., Analyst, Admin']
  created_at timestamp [default: `CURRENT_TIMESTAMP`]
}

// Threats Table
Table threats {
  id integer [primary key]
  name varchar [note: 'Name of the threat']
  description text
  severity enum('Low', 'Medium', 'High') [note: 'Threat severity level']
  status enum('Pending', 'Resolved', 'Escalated') [default: 'Pending', note: 'Current status of the threat']
  created_at timestamp [default: `CURRENT_TIMESTAMP`]
  updated_at timestamp [default: `CURRENT_TIMESTAMP`]
  assigned_to integer [note: 'User assigned to this threat']
}

// Tags Table
Table tags {
  id integer [primary key]
  name varchar [unique, note: 'Name of the tag']
  description text
}

// Threat_Tags Join Table (Many-to-Many Relationship)
Table threat_tags {
  threat_id integer [primary key]
  tag_id integer [primary key]
}

// Logs Table
Table logs {
  id integer [primary key]
  user_id integer [note: 'User performing the action']
  threat_id integer [note: 'Threat being acted upon']
  action varchar [note: 'Action performed, e.g., Resolved, Escalated']
  timestamp timestamp [default: `CURRENT_TIMESTAMP`]
}

// Vulnerabilities Table
Table vulnerabilities {
  id integer [primary key]
  name varchar [note: 'Name of the vulnerability']
  description text
  risk_score integer [note: 'Risk score indicating severity']
  threat_id integer [note: 'Threat associated with this vulnerability']
}

// Relationships
Ref: threats.assigned_to > users.id
Ref: logs.user_id > users.id
Ref: logs.threat_id > threats.id
Ref: threat_tags.threat_id > threats.id
Ref: threat_tags.tag_id > tags.id
Ref: vulnerabilities.threat_id > threats.id
```

# Description

Here’s a short description of all the tables in the proposed database schema:

---

### **1. Users**
- **Purpose**: Stores information about users interacting with the system, such as administrators and analysts.
- **Key Fields**:
  - `username`: Unique name for identifying users.
  - `email`: Contact information for the user.
  - `role`: User's role in the system (e.g., Admin, Analyst).
- **Use Case**: Manages user access and assigns responsibilities for threats or vulnerabilities.

---

### **2. Threats**
- **Purpose**: Tracks potential or actual security incidents detected by the AI model or reported manually.
- **Key Fields**:
  - `name`: Descriptive name for the threat (e.g., "Phishing Attempt").
  - `severity`: Indicates the threat's risk level (Low, Medium, High, Critical).
  - `status`: Current state of the threat (Pending, Resolved, Escalated).
- **Use Case**: Helps monitor, prioritize, and mitigate cybersecurity risks.

---

### **3. Vulnerabilities**
- **Purpose**: Maintains a record of system weaknesses that could be exploited by threats.
- **Key Fields**:
  - `name`: Name or description of the vulnerability (e.g., "Outdated TLS Protocol").
  - `risk_score`: A numerical representation of the vulnerability's severity.
- **Use Case**: Allows analysts to link threats to their root causes and prioritize patching efforts.

---

### **4. Threat_Tags**
- **Purpose**: Implements a many-to-many relationship between threats and tags for categorization.
- **Key Fields**:
  - `threat_id`: Reference to the associated threat.
  - `tag_id`: Reference to the associated tag.
- **Use Case**: Enables efficient filtering and grouping of threats based on predefined categories.

---

### **5. Tags**
- **Purpose**: Stores labels or categories that describe the nature of threats (e.g., "Phishing", "Critical").
- **Key Fields**:
  - `name`: Name of the tag.
  - `description`: Optional explanation of the tag's purpose.
- **Use Case**: Allows analysts to organize threats for better reporting and prioritization.

---

### **6. Logs**
- **Purpose**: Tracks actions taken by users on threats for auditing and accountability.
- **Key Fields**:
  - `user_id`: Reference to the user performing the action.
  - `threat_id`: Reference to the related threat.
  - `action`: Describes what was done (e.g., "Resolved", "Escalated").
  - `timestamp`: Records when the action was performed.
- **Use Case**: Provides an audit trail for security operations.

---

### **Relationships Summary**
- **Users ↔ Threats**: Users can be assigned to manage specific threats.
- **Threats ↔ Vulnerabilities**: Each threat might exploit one or more vulnerabilities.
- **Threats ↔ Tags (via Threat_Tags)**: Threats can be categorized with multiple tags.
- **Logs**: Links user actions to specific threats for a historical record.

---

# Realistic Database Example

Here’s a set of **realistic example data** for the database schema. This example mimics a cybersecurity monitoring system used to track threats, users, vulnerabilities, and actions.

---

### **Users Table**
| **id** | **username**  | **email**              | **password**    | **role**  | **created_at**        |
|--------|---------------|------------------------|-----------------|----------|-----------------------|
| 1      | admin1        | admin1@security.com    | hashed_pwd_001  | Admin    | 2024-11-01 08:00:00   |
| 2      | analyst1      | analyst1@security.com  | hashed_pwd_002  | Analyst  | 2024-11-02 09:30:00   |
| 3      | analyst2      | analyst2@security.com  | hashed_pwd_003  | Analyst  | 2024-11-03 10:00:00   |

---

### **Threats Table**
| **id** | **name**                 | **description**                                                   | **severity** | **status**  | **created_at**        | **updated_at**        | **assigned_to** |
|--------|--------------------------|-------------------------------------------------------------------|--------------|-------------|-----------------------|-----------------------|-----------------|
| 1      | Phishing Email           | Email posing as a legitimate bank asking for user credentials.    | High         | Pending     | 2024-11-05 08:30:00   | 2024-11-05 08:30:00   | 2               |
| 2      | Malware Detected         | Malicious software found in a company laptop.                     | Critical     | Escalated   | 2024-11-06 09:00:00   | 2024-11-06 10:00:00   | 3               |
| 3      | Insider Threat           | Unusual login activity from an internal employee.                 | Medium       | Resolved    | 2024-11-07 11:15:00   | 2024-11-08 08:00:00   | 2               |

---

### **Tags Table**
| **id** | **name**        | **description**                            |
|--------|-----------------|--------------------------------------------|
| 1      | Phishing        | Any threats related to phishing activities.|
| 2      | Malware         | Threats involving malware.                 |
| 3      | Internal        | Threats that involve internal sources.     |
| 4      | Critical        | Highly urgent threats requiring immediate action. |

---

### **Threat_Tags Table** (Many-to-Many Relationship)
| **threat_id** | **tag_id** |
|---------------|------------|
| 1             | 1          |
| 2             | 2          |
| 2             | 4          |
| 3             | 3          |

---

### **Logs Table**
| **id** | **user_id** | **threat_id** | **action**   | **timestamp**        |
|--------|-------------|---------------|--------------|----------------------|
| 1      | 2           | 1             | Investigated | 2024-11-05 09:00:00  |
| 2      | 3           | 2             | Escalated    | 2024-11-06 09:30:00  |
| 3      | 2           | 3             | Resolved     | 2024-11-08 09:00:00  |

---

### **Vulnerabilities Table**
| **id** | **name**              | **description**                                              | **risk_score** | **threat_id** |
|--------|-----------------------|--------------------------------------------------------------|----------------|---------------|
| 1      | Weak Password Policy  | Password policy allowing less than 8 characters without complexity. | 70             | 3             |
| 2      | Unpatched Software    | Known vulnerability in the outdated software version used.      | 90             | 2             |

---

### **Explanation of the Data**:

1. **Users**: 
   - Three users: `admin1`, `analyst1`, and `analyst2`. Admin has full privileges, while analysts are assigned tasks related to threat management.

2. **Threats**:
   - **Phishing Email**: High severity, still under review by analyst1.
   - **Malware Detected**: Critical severity, escalated for immediate attention, assigned to analyst2.
   - **Insider Threat**: Identified and resolved by analyst1, linked to weak password policies.

3. **Tags**:
   - Tags help categorize threats: `Phishing`, `Malware`, `Internal`, and `Critical`.
   - Threats are linked to relevant tags: `Phishing` to "Phishing Email", `Malware` and `Critical` to "Malware Detected", and `Internal` to "Insider Threat".

4. **Threat_Tags**:
   - This table forms a many-to-many relationship between threats and tags. For example:
     - Threat 1 ("Phishing Email") is tagged as "Phishing".
     - Threat 2 ("Malware Detected") is tagged as both "Malware" and "Critical".

5. **Logs**:
   - Records actions performed by users on threats. For example, `analyst1` investigates the "Phishing Email", `analyst2` escalates the "Malware Detected", and `analyst1` resolves the "Insider Threat".

6. **Vulnerabilities**:
   - Vulnerabilities are linked to specific threats. For example:
     - "Weak Password Policy" (70 risk score) is associated with "Insider Threat".
     - "Unpatched Software" (90 risk score) is associated with "Malware Detected".

---

This set of realistic data demonstrates how a cybersecurity monitoring system tracks and handles various threats, their severity, related vulnerabilities, and actions taken by users.

---
# Realistic Example

In a cybersecurity system:

1. **AI detects threats** like phishing or malware by scanning logs and data. It automatically adds these to the database.
   
2. **Analysts review and validate** AI-detected threats, link them to vulnerabilities, and take necessary actions (like escalating, resolving, or mitigating threats).

3. **Logs** track each action, ensuring an audit trail for accountability.

4. **Vulnerabilities** are linked to threats. Analysts identify weak points (e.g., outdated software) and take corrective actions.

This combined AI and manual effort helps organizations manage cybersecurity effectively, with AI handling detection and analysts handling validation, action, and remediation.

Let's walk through a **realistic example** of how the cybersecurity system could be used, using the database schema and example data you've provided.

---

### **Scenario: Detecting and Responding to a Phishing Threat**

#### **1. Threat Detection by AI**
- **AI** scans system logs and identifies suspicious email patterns (e.g., multiple failed login attempts after receiving an email from a seemingly legitimate bank).
- The AI system flags this activity as a potential **phishing threat** and **automatically populates the `Threats` table** with the following data:
  - **Name**: Phishing Email
  - **Description**: Email posing as a legitimate bank asking for user credentials.
  - **Severity**: High
  - **Status**: Pending
  - **Assigned to**: Analyst1 (the analyst who will investigate this issue)
  - **Created At**: 2024-11-05 08:30:00

#### **2. Analyst Review**
- **Analyst1** logs into the system and sees the new **Phishing Email** threat in the dashboard.
- The **severity** is marked as "High", and the status is still "Pending".
- **Analyst1** reviews the details of the threat, noting that it involves suspicious emails targeting users. This corresponds to the tag **Phishing**.
- Analyst1 can now investigate the source, review logs for more details, and attempt to verify the legitimacy of the email using external tools or internal checks.

#### **3. Actions and Logs**
- **Analyst1** investigates the **Phishing Email** and determines it's a real phishing attempt.
- The analyst updates the **status** of the threat to **"Escalated"** in the system because it’s a critical issue that needs immediate attention by the security team.
- Analyst1 logs the **action** taken into the **Logs table**:
  - **Action**: Investigated
  - **Timestamp**: 2024-11-05 09:00:00
  - The log entry helps maintain an audit trail of the actions taken on each threat.

---

### **Scenario: Resolving Malware Threat**

#### **1. Threat Detection by AI**
- The AI system detects **malware** on an employee’s laptop based on unusual file activity (e.g., sudden file encryption).
- The AI system automatically populates the `Threats` table:
  - **Name**: Malware Detected
  - **Description**: Malicious software found in a company laptop.
  - **Severity**: Critical
  - **Status**: Escalated (because it requires immediate action)
  - **Assigned to**: Analyst2 (who specializes in malware threats)
  - **Created At**: 2024-11-06 09:00:00
  - **Updated At**: 2024-11-06 10:00:00

#### **2. Analyst Review**
- **Analyst2** reviews the **Malware Detected** threat in the system.
- Analyst2 sees that the malware is linked to a vulnerability in outdated software (which was previously identified as an issue in the system).
- The **threat** is linked to a **vulnerability** in the **Vulnerabilities table**:
  - **Vulnerability**: Unpatched Software
  - **Risk Score**: 90 (indicating a high risk)
  - **Description**: Known vulnerability in the outdated software version used.
  - **Associated Threat**: Malware Detected

#### **3. Action Taken**
- **Analyst2** decides to escalate the threat to the incident response team, who immediately isolate the infected laptop.
- The system’s **status** for the threat is changed to **Resolved** once the malware is eradicated, and all systems are patched.
- Analyst2 logs the **action** taken:
  - **Action**: Malware cleaned and systems patched.
  - **Timestamp**: 2024-11-06 11:00:00
- The **Logs** table gets updated to reflect this resolution.

---

### **Scenario: Addressing Insider Threat**

#### **1. Threat Detection**
- **Analyst1** notices **suspicious login activity** from an employee’s account, possibly indicating an **insider threat**.
- Analyst1 manually adds this to the `Threats` table since this was not detected by the AI model:
  - **Name**: Insider Threat
  - **Description**: Unusual login activity from an internal employee.
  - **Severity**: Medium
  - **Status**: Pending
  - **Assigned to**: Analyst1
  - **Created At**: 2024-11-07 11:15:00

#### **2. Vulnerability Review**
- Analyst1 links the **insider threat** to a **vulnerability** in the **Vulnerabilities table**:
  - **Vulnerability**: Weak Password Policy
  - **Description**: Password policy allowing weak passwords.
  - **Risk Score**: 70
  - **Associated Threat**: Insider Threat

#### **3. Actions Taken**
- Analyst1 adjusts the **password policy** to enforce stronger passwords and enable multi-factor authentication (MFA).
- The system is updated with the **status** set to **Resolved** after corrective actions are taken.

#### **4. Final Logs**
- Analyst1 logs the action taken:
  - **Action**: Policy updated, MFA enabled.
  - **Timestamp**: 2024-11-08 09:00:00

---

### **How the System Is Used in Real Life:**

1. **Automated Detection (AI)**: The AI model continuously scans for threats, such as phishing emails, malware, or suspicious behavior. It adds these threats to the system and assigns a severity level.
   
2. **Manual Validation and Action (Analyst)**: Security analysts validate the AI’s findings, ensuring that the threats are real. They link relevant vulnerabilities (like unpatched software) and take necessary actions to resolve or escalate the issue.
   
3. **Audit and Reporting**: Every action taken by analysts is logged, creating a traceable history of threat management and vulnerability remediation. This ensures accountability and supports compliance with security standards.

4. **Continuous Improvement**: As threats are resolved and new ones arise, the AI system is updated and improved based on analyst feedback and new threat intelligence.

---

This workflow allows organizations to efficiently manage cybersecurity threats and vulnerabilities, blending automated AI threat detection with human expertise for validation and remediation.

