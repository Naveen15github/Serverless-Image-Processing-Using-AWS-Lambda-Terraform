# Serverless-Image-Processing-Using-AWS-Lambda-Terraform

A **fully serverless, event-driven image processing system** built on AWS using **Lambda, S3, and Terraform**.
This project demonstrates how scalable, cost-efficient, and production-ready image workflows can be implemented without managing any servers.

---

## 🚀 Project Overview

This project implements an **automated image processing ** where images uploaded to an S3 bucket are automatically transformed into multiple optimized formats using AWS Lambda. The entire infrastructure is provisioned using **Terraform**, following Infrastructure as Code (IaC) best practices.

The system is designed to be:

* **Event-driven**
* **Scalable**
* **Cost-efficient**
* **Cloud-native**
* **Production-ready**

---

## 🧠 Architecture Overview

```
┌────────────────────┐
│  Image Upload (S3) │
│  Source Bucket     │
└─────────┬──────────┘
          │  S3:ObjectCreated Event
          ▼
┌────────────────────────┐
│  AWS Lambda Function   │
│  Image Processor       │
│  - Resize              │
│  - Compress            │
│  - Convert formats     │
└─────────┬──────────────┘
          │
          ▼
┌────────────────────────┐
│  Processed S3 Bucket   │
│  (Optimized Images)    │
└────────────────────────┘
```

---

## 🧩 Core Components

### 1. **Source S3 Bucket**

* Receives original image uploads
* Triggers Lambda automatically via `ObjectCreated` events
* Versioning enabled
* Private access only

### 2. **AWS Lambda (Image Processor)**

* Written in **Python**
* Uses **Pillow (PIL)** for image manipulation
* Automatically triggered on image upload
* Stateless and fully serverless

### 3. **Lambda Layer**

* Contains precompiled **Pillow 10.4.0**
* Built using Docker to match Amazon Linux runtime
* Ensures compatibility and avoids native dependency issues

### 4. **Processed S3 Bucket**

* Stores all transformed image variants
* Organized and optimized for retrieval

### 5. **Infrastructure as Code**

* Entire stack defined using **Terraform**
* Easy deployment, reproducibility, and teardown

---

## 📦 Image Variants Generated

For every uploaded image, the system automatically creates:

| Variant          | Description                    |
| ---------------- | ------------------------------ |
| Compressed JPEG  | 85% quality – balanced output  |
| Low Quality JPEG | 60% quality – smallest size    |
| WebP             | Modern, efficient image format |
| PNG              | Lossless version               |
| Thumbnail        | 200×200 px preview             |

### Example Output

```
photo.jpg
├── photo_compressed_ab12.jpg
├── photo_low_ab12.jpg
├── photo_webp_ab12.webp
├── photo_png_ab12.png
└── photo_thumbnail_ab12.jpg
```

---

## 🛠️ Deployment

### 1. Deploy Infrastructure

```bash
./scripts/deploy.sh
```

This script:

* Creates both S3 buckets
* Deploys Lambda and IAM roles
* Attaches triggers and permissions
* Outputs useful deployment values

---

## 📤 Upload an Image

```bash
aws s3 cp my-photo.jpg s3://<UPLOAD_BUCKET_NAME>/
```

Or directly from Terraform output:

```bash
aws s3 cp my-photo.jpg $(terraform output -raw upload_command_example)
```

---

## 📥 View Processed Images

```bash
aws s3 ls s3://<PROCESSED_BUCKET_NAME>/ --recursive
```

Download any processed file:

```bash
aws s3 cp s3://<PROCESSED_BUCKET_NAME>/photo_webp_ab12.webp ./
```

---

## ⚙️ Configuration

### Environment Variables

| Variable           | Description                      |
| ------------------ | -------------------------------- |
| `PROCESSED_BUCKET` | Destination bucket name          |
| `LOG_LEVEL`        | Logging verbosity (INFO / DEBUG) |

---

## 🔧 Customization

### Modify Image Quality

```python
COMPRESSION_LEVELS = {
    "compressed": 85,
    "low": 60,
    "webp": 85
}
```

### Change Thumbnail Size

```python
THUMBNAIL_SIZE = (200, 200)
```

### Add New Variants

```python
variants["custom"] = image.copy()
variants["custom"].save(buffer, format="JPEG", quality=75)
```

---

## 📊 Monitoring & Observability

### View Logs

```bash
aws logs tail /aws/lambda/<FUNCTION_NAME> --follow
```

### CloudWatch Metrics

* Invocation count
* Duration
* Error rate
* Memory usage

---

## 🔐 Security Features

* ✅ Private S3 buckets (no public access)
* ✅ Server-side encryption (AES-256)
* ✅ IAM least-privilege access
* ✅ Isolated Lambda execution role
* ✅ No hardcoded credentials
* ✅ Optional VPC support

---

## 💰 Cost Estimation (Approx.)

| Service              | Estimated Cost            |
| -------------------- | ------------------------- |
| S3 Storage           | ~$0.023 per GB            |
| Lambda Requests      | First 1M free             |
| Lambda Compute       | First 400,000 GB-sec free |
| Typical Monthly Cost | **$0.50 – $2.00**         |

---

## 🧹 Cleanup

```bash
./scripts/destroy.sh
```

Removes:

* Lambda functions
* IAM roles
* S3 buckets
* All associated resources

---

## 🧠 Key Learnings & Highlights

* Designed a **fully serverless, event-driven system**
* Implemented **real-world image processing logic**
* Solved native dependency issues using **Dockerized Lambda layers**
* Applied **IaC best practices** using Terraform
* Built a production-grade pipeline with minimal operational overhead

---

## 📌 Reference

This project is inspired by the implementation walkthrough:
**“Image Processing Serverless Project using AWS Lambda” – Tech Tutorials with Piyush**

The architecture and implementation were independently recreated, enhanced, and production-hardened.

