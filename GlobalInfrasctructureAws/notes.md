# AWS Hands-On Learning Notes 🚀

Ye notes file meri AWS learning journey document kar rahi hai, with **problems faced and solutions**.

---

## Step 1: AWS Fundamentals

**Tasks Completed:**

- AWS Global Infrastructure: Regions, Availability Zones, Edge Locations
- Created IAM User & Group (`S3User`)
- Assigned AdministratorAccess (for learning purpose)
- Billing & Cost Management setup

**Problems & Solutions:**

1. **Region Selection:** Confusion about which region to use**Solution:** Chose **Mumbai region** as closest to Pakistan for better latency
2. **IAM Role Assignment:** Kaise user ko group + policy attach kare**Solution:** Created group `S3User` → added IAM user → attached Admin policy
3. **Cost Alerts:** Kaise prevent overspending
   **Solution:** Used **AWS Budgets** & alerts

---

## Step 2: EC2 Launch & Server Setup

**Tasks Completed:**

- Launched EC2 instance (t2.micro, Ubuntu)
- SSH access setup using `.pem` key
- Installed Apache HTTP server
- Hosted basic HTML page

**Problems & Solutions:**

1. **AWS CLI Missing:** `apt install awscli` failed**Solution:** Installed **AWS CLI v2** using official instructions (`curl → unzip → install`)
2. **SSH Connection Issues:** Wrong key/permissions**Solution:** Correct `.pem` key + security group rules (port 22)
3. **Server Not Serving:** Apache not running
   **Solution:** `sudo systemctl start httpd` + `sudo systemctl enable httpd`

---

## Step 3: S3 + CloudFront Static Website

**Tasks Completed:**

- Created S3 bucket `taimoor-static-site-123`
- Uploaded `index.html`
- Enabled Static Website Hosting
- Created CloudFront distribution for global delivery

**Problems & Solutions:**

1. **Access Denied on CloudFront**
   - Cause: CloudFront origin pointing to wrong S3 URL
   - Solution:

     - Use **S3 static website endpoint** as origin
     - Bucket Policy allows public read:

     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Sid": "PublicRead",
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::taimoor-static-site-123/*"
         }
       ]
     }
     ```

     - Refresh CloudFront cache (invalidate `/*`)
2. **File Upload Confusion**
   - Cause: Wrong file name / folder
   - Solution: Upload `index.html` via S3 **Upload → Add files**

---

## Step 4: EC2 ↔ S3 Secure Connection (IAM Role)

**Tasks Completed:**

- Created IAM Role: `EC2-S3-Access-Role`
- Attached role to EC2 instance
- Tested S3 access via AWS CLI v2:

```bash
aws s3 ls s3://taimoor-static-site-123/
```
