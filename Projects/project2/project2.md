# Project 2: AWS Event-Driven Programming

## Objective

Build an end-to-end pipeline: a **producer** program generates a **JSON bill file** with random fields, **uploads it to S3**; **S3 notifies SQS**; an **AWS Lambda** function (triggered by the queue) **reads the bill from S3** and **writes a record to DynamoDB**.

## Architecture

1. **Producer** (local script or small app): builds JSON → uploads object to your S3 bucket.  
2. **S3** sends an **ObjectCreated** notification to **SQS**.  
3. **Lambda** (SQS trigger): receives the S3 event, uses **bucket + key** from the message to **`GetObject`**, parses the JSON body, then **`PutItem`** into DynamoDB.

**Important:** The SQS message contains the **S3 event metadata**, not the file contents. Your Lambda **must** call S3 to download and parse the JSON bill.

---

## Requirements

### 1. Producer program

Implement a **producer** (Python 3.9+ recommended) that:

- Creates a JSON object with **exactly** these keys and value rules:

   | Field | Rule |
   |--------|------|
   | `accountNumber` | Random **integer**, inclusive range **10000–90000** |
   | `billNumber` | Random **integer**, inclusive range **50–500** |
   | `amountDue` | Random **number** in range **0–200** (inclusive). You may use integers or decimals (e.g. currency-style); document your choice. |

- Saves it to a **`.json` file** and **uploads** it to **your** S3 bucket using the AWS SDK from the same program.

- **Object key (required):** `import uuid`, call **`uuid.uuid4()`** once per upload, convert with **`str(...)`**, and upload with this key shape:

   `bills/<uuid>.json`  

   Example: `bills/550e8400-e29b-41d4-a716-446655440000.json`  

   The **same UUID string** (without `bills/` and without `.json`) is your DynamoDB partition key value (see below).

**Example payload shape** (values will vary):

```json
{
  "accountNumber": 45231,
  "billNumber": 187,
  "amountDue": 42.5
}
```

### 2. Event pipeline (AWS configuration)

- **S3 bucket**: Create a new bucket for this project.  
- **SQS queue**: **Standard** queue receiving S3 event notifications.  
- **S3 event notification**: On object creation (e.g. `s3:ObjectCreated:*` or at least `Put`), send messages to your queue.  
- **Permissions**:
  - **Queue policy**: Allow S3 to `sqs:SendMessage` to this queue.  
  - **Lambda execution role**: Use the IAM role **`LabRole`** for your function (this lab role provides the access you need for SQS, S3, and DynamoDB in the course environment).

### 3. Consumer: Lambda + SQS

The consumer **must** be an **AWS Lambda function** with an **SQS trigger** attached to your queue.

#### Environment variables (required)

Do **not** hard-code the S3 bucket name or the DynamoDB table name in your Lambda code. Read them from **Lambda environment variables** (for example **`S3_BUCKET`** and **`DYNAMODB_TABLE`**—document any names you choose in your submission). Configure values in the Lambda console to match your resources.

Use the bucket env var when calling **`GetObject`** (together with the **object key** from the S3 event).

For **each** SQS record:

1. Parse the **S3 event** JSON in the record body (`Records[]` from S3).  
2. For each S3 record (typically one), read **object key** from the event (and use your **`S3_BUCKET`** env var for the bucket when calling S3).  
3. Call **`GetObject`** on that object, read the body, and **parse** it as JSON.  
4. **Validate** that `accountNumber`, `billNumber`, and `amountDue` are present and types are sensible; log and skip or fail gracefully on bad data (no unhandled exceptions).  
5. **`PutItem`** into the table named by your **`DYNAMODB_TABLE`** env var with the required attributes (see below). The partition key value must be the **UUID** parsed from the S3 object key (`bills/<uuid>.json` → use `<uuid>`).

Use **try/except** around JSON parsing, S3 reads, and DynamoDB writes. Log errors to **CloudWatch** with context (e.g. message id, bucket, key). Process each SQS record safely so one bad message does not cause unhandled crashes.

### 4. DynamoDB table `bills`

Create **one** DynamoDB table. In the console you declare **how** the table is keyed; in Lambda you **fill in** those key attributes when you write an item. Use exactly:

| What you configure | Required choice |
|--------------------|-----------------|
| **Table name** | `bills` |
| **Partition key** (attribute name + type) | **`billId`**, type **String** |

There is **no sort key**—only a single partition key.

**What goes in `billId`?**  
The partition key is **not** random at write time: it must **match the UUID in the S3 object key**. The producer uploads to `bills/<uuid>.json`; the `<uuid>` part (for example `550e8400-e29b-41d4-a716-446655440000`) is the same string you must store in **`billId`**. So one object in S3 and one row in DynamoDB are tied together by that shared UUID. The JSON file body does not need to contain `billId`—Lambda derives it from the key.

Each item **must** include at least:

- **`billId`** (partition key; the UUID from the filename)  
- `accountNumber`, `billNumber`, `amountDue` (from the JSON file in S3)  
- `s3Bucket`, `s3Key` (full key from the S3 event, e.g. `bills/<uuid>.json`)  
- `processedAt`  (The time that message was processed in Lambda)

### 5. AWS resources summary

- S3 bucket + event notification → SQS  
- SQS queue  
- Lambda + **SQS trigger** (execution role **`LabRole`**) + **environment variables** for S3 bucket and DynamoDB table names (no hard-coded names in code)  
- DynamoDB table **`bills`** (partition key **`billId`** / String)

Runtime: **Python 3.9+** (consistent with Project 1).

---

## Example: S3 event in SQS body (illustrative)

Lambda receives SQS `Records`; each has a `body` string like:

```json
{
  "Records": [
    {
      "eventVersion": "2.1",
      "eventSource": "aws:s3",
      "awsRegion": "us-east-1",
      "eventTime": "2026-03-28T12:00:00.000Z",
      "eventName": "ObjectCreated:Put",
      "s3": {
        "bucket": { "name": "your-bucket" },
        "object": {
          "key": "bills/550e8400-e29b-41d4-a716-446655440000.json",
          "size": 64,
          "eTag": "\"abc123\""
        }
      }
    }
  ]
}
```

Use this to locate the object, then **fetch** and parse the uploaded JSON bill. Derive **`billId`** from the key: e.g. strip the `bills/` prefix and `.json` suffix to obtain the UUID string.

