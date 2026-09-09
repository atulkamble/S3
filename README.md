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

Amazon Web Services official S3 documentation: [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/?utm_source=chatgpt.com)
