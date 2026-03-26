# AWS IAM Notes – Roles vs Users vs Groups

## 📌 Overview

In AWS IAM (Identity and Access Management), permissions can be assigned to:

- Users
- Groups
- Roles

Understanding the difference is critical for building **secure and scalable systems**.

---

# 👤 Users

## Definition

A user represents a **person or application** that interacts with AWS.

## Key Features

- Has permanent credentials (username/password or access keys)
- Permissions are directly attached or via groups
- Used for **long-term access**

## Example

- Developer account
- Admin account

---

# 👥 Groups

## Definition

A group is a **collection of users**.

## Key Features

- Permissions are assigned to the group
- All users in the group inherit permissions
- Simplifies permission management

## Example

- Dev Team → S3 access
- Admin Team → Full access

---

# 🔁 Roles

## Definition

A role is an identity with permissions that can be **assumed temporarily**.

## Key Features

- No permanent credentials
- Provides **temporary security credentials**
- Can be assumed by:
  - Users
  - AWS services (EC2, Lambda)
  - External accounts

---

# ⏳ Temporary Credentials (Important)

When a role is assumed:

1. AWS generates temporary credentials
2. These credentials expire after a fixed time

### Default Duration

- 1 hour (can be extended up to ~12 hours)

---

# 🔥 Core Difference

| Feature     | Users             | Groups          | Roles                    |
| ----------- | ----------------- | --------------- | ------------------------ |
| Type        | Identity          | Collection      | Temporary Identity       |
| Credentials | Permanent         | No credentials  | Temporary                |
| Used by     | Humans            | Humans          | Humans + Services        |
| Expiry      | ❌ No             | ❌ No           | ✅ Yes                   |
| Best Use    | Individual access | Team management | Secure, temporary access |

---

# ❌ Problem with Direct Permissions

## Issues:

- Hard to manage at scale
- Security risks (permanent access)
- No temporary access control

---

# ✅ Why Roles are Important

## 1. Temporary Access

- Access only when needed
- Automatically expires

## 2. Better Security

- No hardcoded credentials
- Follows **least privilege principle**

## 3. Service Access

- Services like EC2 can access resources without storing keys

## Example

EC2 accessing S3:

- Attach role to EC2
- No need to store AWS keys

## 4. Cross-Account Access

- One AWS account can access another using roles

---

# 🧠 Key Concept (Must Remember)

❌ Wrong:

> Roles have limited-time permissions

✅ Correct:

> Roles provide **temporary credentials with permissions**

---

# 🧠 Memory Trick

- User → Always access
- Group → Manage users
- Role → Temporary access

---

# 🔐 Real-World Scenario

## Without Roles ❌

- Store AWS keys in application
- Risk: Key leakage

## With Roles ✅

- Attach role to service
- AWS manages credentials automatically

---

# 🎯 Interview Answer

**Q: Why use roles instead of assigning permissions directly?**

**Answer:**
Roles provide temporary, secure, and scalable access. They eliminate the need for permanent credentials, support cross-account and service-based access, and enforce least privilege more effectively.

---

# 🚀 Summary

- Users & Groups → Permanent permissions
- Roles → Temporary access via assumed credentials
- Roles are essential for:
  - Security
  - Scalability
  - Real-world AWS architectures
