# Amazon S3 Fundamentals

## Bucket Creation, Presigned URLs & Static Website Hosting

### 1. Introduction to Amazon S3

Amazon S3 = **Amazon Simple Storage Service**

S3 is an **object storage service** used to store and retrieve files over the internet.

**Basic terminology:**

```text
Bucket  = Container used to store objects
Object  = File stored inside a bucket
Key     = Unique name/path of an object
Region  = AWS location where bucket is created
```

**Common use cases:**

```text
Backup and Restore
Images and Videos
Application Files
Log Storage
Data Lakes
Static Website Hosting
Software Downloads
Archive Storage
```

### Points to Remember

```text
1. S3 is Object Storage.

2. Data is stored as Objects inside Buckets.

3. S3 is not a traditional file system.

4. Bucket names must be globally unique within an AWS partition.

5. Maximum object size = 5 TB.

6. S3 provides very high durability.

7. Buckets are private by default.

8. Public access should be enabled only when required.

9. IAM Policies and Bucket Policies can control access.

10. S3 supports:
    - Versioning
    - Encryption
    - Lifecycle Rules
    - Replication
    - Presigned URLs
    - Static Website Hosting
```

---

# 2. Create S3 Bucket & Upload Objects

### Steps

```text
1. Login to AWS Console

2. Search:
   S3

3. Open:
   Amazon S3

4. Click:
   Create bucket

5. Enter Bucket Name:
   my-s3-practice-<unique-number>

6. Select AWS Region:
   e.g. us-east-1

7. Keep:
   Block all public access = Enabled

8. Keep other settings as default.

9. Click:
   Create bucket

10. Open the created bucket.

11. Click:
    Upload

12. Click:
    Add files

13. Select a file:
    example.txt
    image.jpg
    index.html

14. Click:
    Upload

15. Open the uploaded object.

16. Check:
    Object name
    Object URL
    Size
    Storage class
    Permissions
```

### Points to Remember

```text
Bucket
  |
  +-- index.html
  +-- image.jpg
  +-- documents/
        |
        +-- notes.pdf
```

```text
Bucket name        = Globally unique
Object name/key    = Unique within the bucket
Default access     = Private
Default storage    = S3 Standard
```

---

# 3. Create a Presigned URL

A **Presigned URL** provides **temporary access** to a private S3 object without making the object public.

### Architecture

```text
Private S3 Object
       |
       v
Generate Presigned URL
       |
       v
Temporary URL
       |
       v
User accesses object
       |
       v
URL expires
```

### AWS Console Steps

```text
1. Open:
   Amazon S3

2. Open your bucket.

3. Select the private object.

4. Open:
   Object actions

5. Select:
   Share with a presigned URL

6. Configure expiration time.

7. Create the presigned URL.

8. Copy the URL.

9. Open it in another browser/private window.

10. Verify the object is accessible.
```

### AWS CLI Example

```bash
aws s3 presign s3://my-s3-practice-12345/image.jpg --expires-in 3600
```

```text
3600 seconds = 1 hour
```

### Points to Remember

```text
Presigned URL = Temporary Access

Object can remain private.

No need to make the entire bucket public.

URL automatically expires.

Anyone possessing the URL can use it until it expires,
subject to the permissions and validity of the signing credentials.

Useful for:
- Temporary downloads
- Sharing private files
- Application downloads
- Secure file distribution
```

---

# 4. Host a Static Website Using S3

S3 can host **static web content**.

Supported:

```text
HTML
CSS
JavaScript
Images
Static Files
```

Not directly supported by S3 website hosting:

```text
PHP
Python
Java
Node.js server-side applications
Databases
Server-side processing
```

### Create index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>My S3 Website</title>
</head>
<body>
    <h1>Welcome to Amazon S3</h1>
    <h2>Static Website Hosting Practice</h2>
</body>
</html>
```

### Hosting Steps

```text
1. Create an S3 bucket.

2. Open the bucket.

3. Upload:
   index.html

4. Open:
   Properties

5. Scroll to:
   Static website hosting

6. Click:
   Edit

7. Select:
   Enable

8. Hosting type:
   Host a static website

9. Index document:
   index.html

10. Save changes.

11. Note the:
    Bucket website endpoint
```

---

# 5. Configure Public Access

For a simple public S3 website lab:

```text
1. Open the bucket.

2. Go to:
   Permissions

3. Find:
   Block public access

4. Click:
   Edit

5. Disable the bucket-level public-access blocks needed
   for the public bucket policy.

6. Confirm the warning.

7. Save changes.

8. Go to:
   Bucket Policy

9. Add a policy allowing public read access
   to the website objects.
```

### Example Bucket Policy

Replace `YOUR-BUCKET-NAME`.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

### Test Website

```text
1. Open:
   Properties

2. Go to:
   Static website hosting

3. Copy:
   Bucket website endpoint

4. Open the endpoint in browser.

5. Verify:
   index.html loads successfully.
```

---

# Important Points to Remember

```text
S3 = Simple Storage Service

S3 Type = Object Storage

Bucket = Container
Object = File
Key = Object name/path

Bucket names must be globally unique within an AWS partition.

Maximum S3 object size = 5 TB.

S3 buckets are private by default.

Presigned URL:
Private Object -> Temporary Access

Static Website:
S3 -> index.html -> Public Read -> Website Endpoint

S3 static website hosting supports static content only.

S3 website endpoints use HTTP, not HTTPS.

For a production HTTPS website:
S3 + CloudFront + ACM + Route 53

Avoid making buckets public unless public access is actually required.
```

### Quick Practice Flow

```text
Amazon S3
   |
   +--> Create Bucket
   |
   +--> Upload Object
   |
   +--> Access Private Object
   |       |
   |       +--> Presigned URL
   |
   +--> Upload index.html
           |
           +--> Enable Static Website Hosting
           |
           +--> Configure Public Access
           |
           +--> Add Bucket Policy
           |
           +--> Open Website Endpoint
```

# Amazon S3 Versioning, Replication, Storage Classes & Lifecycle Management

## 1. Amazon S3 Versioning

### Concept

S3 Versioning keeps **multiple versions of the same object** inside a bucket.

Example:

```text
Bucket: mybucket

index.html
├── Version 1 → Old file
├── Version 2 → Updated file
└── Version 3 → Latest file
```

If an object is accidentally modified or deleted, an older version can be restored.

### Enable Versioning

```text
AWS Console
→ S3
→ Select Bucket
→ Properties
→ Bucket Versioning
→ Edit
→ Enable
→ Save Changes
```

### Practice

```text
1. Create bucket → my-version-bucket
2. Enable Versioning
3. Upload → test.txt
4. Modify test.txt locally
5. Upload test.txt again with the same name
6. Open bucket
7. Enable "Show versions"
8. Observe multiple versions
9. Delete the latest version/object
10. Restore or download an older version
```

### Points to Remember

```text
• Versioning is configured at bucket level.
• Every updated object receives a unique Version ID.
• Older versions continue consuming storage.
• Versioning protects against accidental overwrite/deletion.
• Deleting a versioned object normally creates a Delete Marker.
• Versioning can be suspended after enabling it.
• Lifecycle rules can remove old versions automatically.
```

---

# 2. S3 Cross-Region Replication (CRR)

### Concept

CRR automatically copies objects from a bucket in one AWS Region to a bucket in another AWS Region.

```text
Source Bucket
us-east-1
     |
     | Replication
     ↓
Destination Bucket
ap-south-1
```

### Requirements

```text
• Create source bucket
• Create destination bucket
• Buckets must be in different AWS Regions for CRR
• Enable Versioning on both buckets
• Configure replication rule
• Configure/allow required IAM permissions
```

### Steps

```text
1. Create Source Bucket
   Region → us-east-1

2. Create Destination Bucket
   Region → ap-south-1

3. Enable Versioning on Source Bucket

4. Enable Versioning on Destination Bucket

5. Open Source Bucket

6. Management
   → Replication Rules

7. Create Replication Rule

8. Enter Rule Name
   → cross-region-replication

9. Select scope
   → Apply to all objects
   OR
   → Limit using prefix/tags

10. Select Destination Bucket

11. Select/Create IAM Role

12. Save Replication Rule

13. Upload test.txt to Source Bucket

14. Open Destination Bucket

15. Verify replicated object
```

### Important

```text
Source Bucket (Region A)
        |
        | CRR
        ↓
Destination Bucket (Region B)
```

CRR is mainly useful for:

```text
• Disaster Recovery
• Geographic redundancy
• Compliance requirements
• Maintaining copies in another region
• Data protection
```

---

# 3. Versioning vs Replication

| Feature             | Versioning                    | Replication                |
| ------------------- | ----------------------------- | -------------------------- |
| Purpose             | Maintain object versions      | Copy objects               |
| Protection          | Accidental deletion/overwrite | Regional/bucket redundancy |
| Versioning required | Yes                           | Yes                        |
| Multiple versions   | Yes                           | Yes                        |
| Disaster Recovery   | Helpful                       | Very useful                |
| Extra storage cost  | Yes                           | Yes                        |

```text
Versioning = Multiple versions
Replication = Multiple copies
```

---

# 4. Benefits of Versioning & Replication

```text
• Protection against accidental deletion
• Protection against accidental overwrite
• Restore previous object versions
• Maintain backup copies
• Improve data durability strategy
• Disaster Recovery
• Geographic redundancy
• Compliance and business continuity
```

### Remember

```text
Versioning ≠ Backup by itself
Replication ≠ Complete backup strategy

For stronger protection, combine:
Versioning + Replication + Lifecycle + appropriate access controls
```

---

# 5. Amazon S3 Storage Classes

Storage classes allow you to choose storage based on **access frequency, retrieval requirements, resilience needs, and cost**.

| Storage Class                 | Best For                                     |
| ----------------------------- | -------------------------------------------- |
| S3 Standard                   | Frequently accessed data                     |
| S3 Intelligent-Tiering        | Unknown/changing access patterns             |
| S3 Standard-IA                | Infrequently accessed data                   |
| S3 One Zone-IA                | Infrequent, recreatable data                 |
| S3 Glacier Instant Retrieval  | Archive data needing millisecond retrieval   |
| S3 Glacier Flexible Retrieval | Archive/backup data                          |
| S3 Glacier Deep Archive       | Long-term archival                           |
| S3 Express One Zone           | Very high-performance, low-latency workloads |

### Easy Way to Remember

```text
Frequently Used
      ↓
S3 Standard

Unknown Access Pattern
      ↓
Intelligent-Tiering

Less Frequently Used
      ↓
Standard-IA

Archive
      ↓
Glacier

Long-Term Archive
      ↓
Deep Archive
```

---

# 6. Changing Object Storage Class

### Console Steps

```text
1. AWS Console
2. S3
3. Open Bucket
4. Select Object
5. Actions
6. Edit storage class
7. Select new Storage Class
8. Save changes
```

Example:

```text
Current:
photo.zip → S3 Standard

Change to:
photo.zip → S3 Standard-IA
```

### Points to Remember

```text
• Storage class is associated with objects.
• Different objects in the same bucket can use different storage classes.
• Some classes have minimum storage-duration charges.
• Some classes have retrieval charges.
• Choose classes based on access pattern, availability needs and cost.
```

---

# 7. S3 Lifecycle Management

### Concept

Lifecycle rules automatically manage objects based on their **age** and other filters.

Example:

```text
Upload Object
     ↓
S3 Standard
     ↓ 30 Days
Standard-IA
     ↓ 90 Days
Glacier
     ↓ 365 Days
Delete
```

Instead of manually moving or deleting old objects, S3 performs the configured lifecycle actions automatically.

---

# 8. Create Lifecycle Rule

```text
1. AWS Console
2. S3
3. Select Bucket
4. Management
5. Lifecycle Rules
6. Create Lifecycle Rule

7. Enter Rule Name
   → archive-old-files

8. Select scope
   → All objects
   OR
   → Prefix/tags

9. Select Lifecycle Actions

10. Configure transitions

Example:
   After 30 days → Standard-IA
   After 90 days → Glacier

11. Configure expiration if required

Example:
   After 365 days → Delete

12. Create Rule
```

---

# 9. Lifecycle Actions

Lifecycle rules can be used for tasks such as:

```text
• Transition current objects to another storage class
• Transition noncurrent object versions
• Expire current objects
• Permanently delete noncurrent versions
• Delete expired object delete markers
• Abort incomplete multipart uploads
```

---

# 10. Storage Classes + Lifecycle Example

```text
Day 0
S3 Standard
    |
    | 30 Days
    ↓
Standard-IA
    |
    | 90 Days
    ↓
Glacier Flexible Retrieval
    |
    | 365 Days
    ↓
Delete
```

This provides automatic storage optimization.

---

# 11. Benefits of Storage Classes & Lifecycle Policies

```text
• Reduce S3 storage costs
• Automatically archive old data
• Automatically delete unnecessary data
• Reduce manual administration
• Manage large amounts of data efficiently
• Match storage cost with access frequency
• Manage old object versions
• Improve long-term data management
```

---

# Points to Remember

```text
S3 VERSIONING
-------------
Versioning → Keep multiple versions
Version ID → Identifies each version
Delete Marker → Added when a versioned object is deleted

S3 REPLICATION
--------------
CRR → Cross-Region Replication
Versioning → Required for replication
IAM Role → Gives S3 replication permissions
CRR → Source and destination are in different regions

STORAGE CLASSES
---------------
Standard → Frequent access
Intelligent-Tiering → Changing/unknown access
Standard-IA → Infrequent access
One Zone-IA → Infrequent + single AZ
Glacier Instant → Archive + instant retrieval
Glacier Flexible → Archive
Deep Archive → Long-term archive
Express One Zone → High-performance workloads

LIFECYCLE
---------
Lifecycle → Automatic object management
Transition → Move to another storage class
Expiration → Remove objects
Noncurrent Version → Older version of versioned object

BEST PRACTICE
-------------
Versioning → Recovery
Replication → Redundancy / DR
Storage Classes → Cost optimization
Lifecycle → Automation
```

### One-Line Revision

```text
Versioning  = Keep old versions
CRR         = Copy data to another AWS Region
Storage Class = Choose how data is stored based on access/cost
Lifecycle   = Automatically transition or expire objects
```


Amazon Web Services official S3 documentation: [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/?utm_source=chatgpt.com)
