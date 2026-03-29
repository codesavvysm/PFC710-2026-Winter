# AWS Lambda (Python)

A reference for building and deploying AWS Lambda functions in Python. No prior Lambda experience required.

---

## What Is AWS Lambda?

**AWS Lambda** is a **serverless** compute service: you upload code (e.g. Python), and AWS runs it when triggered—by a schedule, an HTTP request, or other events. You do not manage servers; you only write the function and configure when it runs.

- **Pay per use:** You are charged for the number of invocations and the execution time (per 100 ms).
- **Scalable:** AWS runs as many copies of your function as needed to handle the load.
- **Stateless:** Each invocation gets a clean environment. Use environment variables or external storage (e.g. S3) for configuration and data.

Lambda can run on demand (e.g. when you click “Test” in the console), on a schedule, or in response to events from other AWS services.

---

## Lambda Function Basics

### Handler (entry point)

Lambda runs a single function you define. For Python, you specify the **handler** as:

```text
filename.function_name
```

Example: if your file is `lambda_function.py` and your function is `lambda_handler`, the handler is:

```text
lambda_function.lambda_handler
```

### Handler signature (Python)

Your function must accept two arguments and can return a value (e.g. a dict that Lambda can convert to JSON):

```python
def lambda_handler(event, context):
    # event: data passed to the invocation (e.g. test event payload)
    # context: runtime info (request_id, remaining time, etc.)
    # Your logic here
    return {"statusCode": 200, "body": "Success"}
```

You can ignore `event` and `context` if the function is only triggered with an empty or fixed payload; use them when input is passed via the event.

### Runtime

Choose **Python 3.9** or **Python 3.10** (or newer). Set the runtime when creating the function in the AWS Console.

---

## Creating a Lambda Function in the AWS Console

1. In **AWS Console**, go to **Services → Lambda**.
2. Click **Create function**.
3. Choose **Author from scratch**.
4. **Function name:** e.g. `my-function`.
5. **Runtime:** Python 3.9 (or 3.10).
6. **Execution role:** Use an existing role that has the permissions your function needs, or create a new one. The role typically needs:
   - Permissions to write to **CloudWatch Logs** (so you can see logs).
   - Any other permissions your code uses (e.g. **S3** `PutObject` if you write to a bucket).
7. Click **Create function**.

You will then see the **Code** tab where you can paste or upload your code.

---

## Environment Variables

Environment variables are the right place for configuration: API keys, bucket names, limits, and other settings. They keep secrets and config out of your code.

### Setting environment variables in the console

1. Open your Lambda function.
2. Go to the **Configuration** tab.
3. In the left sidebar, click **Environment variables**.
4. Click **Edit** → **Add environment variable** for each setting (e.g. `API_KEY`, `BUCKET_NAME`).
5. **Save** the configuration.

**Security:** Do not put API keys or secrets in your code or in screenshots. Use environment variables and mask sensitive values in any documentation or screenshots.

### Reading environment variables in Python

Use the `os` module:

```python
import os

api_key = os.environ.get("API_KEY")
bucket_name = os.environ.get("BUCKET_NAME")
```

- `os.environ.get("KEY")` returns `None` if the variable is not set.
- `os.environ.get("KEY", "default")` returns `"default"` when the variable is missing.
- All values are strings. Convert where needed (e.g. `int(...)` for numeric config).
- Validate and handle missing or invalid values: raise clear errors or log and return an error response so the function fails in a controlled way.

---

## Scheduling Lambda with EventBridge

You can run a Lambda function on a schedule (e.g. every hour, daily) using **Amazon EventBridge** (formerly CloudWatch Events). EventBridge invokes your function at the specified times; your code does not need to change—it is simply triggered with an event payload (often empty or a fixed structure) each time the schedule fires.

### Why EventBridge?

- **Decoupled:** The schedule is configured outside your code. You can enable, disable, or change the schedule without redeploying the function.
- **Flexible:** Supports both simple intervals (`rate`) and cron-style schedules.
- **Visible:** In the Lambda console, the trigger appears under **Configuration → Triggers**, so you can confirm the function runs on a schedule.

### Adding a schedule trigger in the AWS Console

1. Open your Lambda function in the **AWS Console**.
2. Go to the **Configuration** tab.
3. In the left sidebar, click **Triggers** (or **Add trigger** if you have none).
4. Click **Add trigger**.
5. **Source:** Choose **EventBridge (CloudWatch Events)**.
6. **Rule:** Either create a new rule or select an existing one.
   - **Create a new rule:** Give it a name (e.g. `hourly-invocation`).
   - **Rule type:** Select **Schedule expression**.
   - **Schedule expression:** Use one of the formats below.
7. Leave **Enable trigger** checked.
8. Click **Add**.

Your Lambda will now be invoked automatically according to the schedule. You can see the trigger listed under **Configuration → Triggers**.

### Schedule expression formats

**Rate expression** – run at a fixed interval:

| Expression        | Meaning                    |
|-------------------|----------------------------|
| `rate(1 minute)`  | Every 1 minute             |
| `rate(5 minutes)` | Every 5 minutes            |
| `rate(1 hour)`    | Every 1 hour               |
| `rate(1 day)`     | Every 24 hours             |

- Use singular: `minute`, `hour`, `day` (not `minutes`, `hours`, `days`).
- Minimum interval is 1 minute.

**Cron expression** – run at specific times (UTC):

| Expression              | Meaning                          |
|-------------------------|----------------------------------|
| `cron(0 * * * ? *)`     | Every hour at minute 0           |
| `cron(0 12 * * ? *)`    | Every day at 12:00 UTC           |
| `cron(0 9 ? * MON-FRI *)` | Weekdays at 09:00 UTC         |

Format: `cron(minutes hours day-of-month month day-of-week year)`. Use `?` for “any” where both day-of-month and day-of-week are not needed.

### Verifying the trigger

- **Configuration → Triggers:** You should see **EventBridge (CloudWatch Events)** with your rule name and schedule expression.
- **Monitoring → Logs:** After the schedule fires, new invocations will appear in CloudWatch Logs; you can confirm the function ran at the expected time.

Your handler can ignore the `event` and `context` arguments if the schedule does not pass custom payload; the function will still run on every scheduled invocation.

---

## Packaging Your Lambda (Python + Dependencies)

Lambda runs your code in a fixed environment. The runtime includes **boto3** (AWS SDK), so you do not need to package it. It does **not** include third-party libraries like **requests**; if your code uses them, you must provide the library (and its dependencies) in a **zip** that Lambda can use.

**On macOS/Linux:**

1. Create a folder for the deployment package:

   ```bash
   mkdir lambda_package
   cd lambda_package
   ```

2. Install the library into this folder:

   ```bash
   pip install requests -t .
   ```

3. Copy your handler file into the same folder:

   ```bash
   cp /path/to/your/lambda_function.py .
   ```

4. Zip the **contents** of the folder (not the folder itself):

   ```bash
   zip -r ../lambda_deploy.zip .
   cd ..
   ```

5. Upload `lambda_deploy.zip` in the Lambda console: **Code** tab → **Upload from** → **.zip file**.

**On Windows (PowerShell or Command Prompt):**

1. Create a folder for the deployment package and go into it:

   ```powershell
   mkdir lambda_package
   cd lambda_package
   ```

2. Install the library into this folder:

   ```powershell
   pip install requests -t .
   ```

3. Copy your handler file into the same folder (adjust the path to your file):

   ```powershell
   copy C:\path\to\your\lambda_function.py .
   ```

4. Zip the **contents** of the folder (not the folder itself). In PowerShell, from inside `lambda_package`:

   ```powershell
   Compress-Archive -Path * -DestinationPath ..\lambda_deploy.zip
   cd ..
   ```

   Or in File Explorer: open `lambda_package`, select **all** files and folders inside it (Ctrl+A), right‑click → **Send to** → **Compressed (zipped) folder**, then name it `lambda_deploy.zip`. The zip must contain your `.py` file and the library folders at the **root** of the archive, not inside a `lambda_package` subfolder.

5. Upload `lambda_deploy.zip` in the Lambda console: **Code** tab → **Upload from** → **.zip file**.

**Important:** Your handler file (e.g. `lambda_function.py`) must be at the **root** of the zip (same level as the library folders). The handler remains `lambda_function.lambda_handler`.

### Zip structure (conceptual)

```text
lambda_deploy.zip
├── lambda_function.py    ← your code
├── requests/
│   └── ...
└── urllib3/
    └── ...
```

---

## IAM Permissions

The **execution role** attached to your Lambda function must have:

1. **CloudWatch Logs** – so Lambda can write logs (usually attached by default when you create a role from the Lambda console).
2. **Any other services your code uses** – e.g. S3 (`s3:PutObject`, `s3:GetObject`, `s3:GetBucketLocation` on the buckets you use).

If your code gets “Access Denied” when calling another service, add the required actions for that service to the role’s policy. You do not need to create a new role if you already have one (e.g. a lab or course role); ensure that role allows access to the resources your function uses.

---

## Using S3 from Lambda

- **Create a bucket** in the S3 console if you need one. Use the same region as your Lambda when possible.
- **Object key:** Choose a key format that fits your use case (e.g. `prefix/category/timestamp.json` or `data/2026-01-15.json`).
- Use the **boto3** client (already available in the runtime) to upload:

```python
import boto3
import json

s3 = boto3.client("s3")
bucket_name = os.environ.get("BUCKET_NAME")

# Example: store a JSON body under a key
key = "data/example.json"
s3.put_object(
    Bucket=bucket_name,
    Key=key,
    Body=json.dumps(your_data),
    ContentType="application/json"
)
```

Get `bucket_name` (and any key parts) from environment variables or the event. Only write to S3 when your logic has succeeded; do not store failed or invalid results if your requirements say otherwise.

---

## Logging and CloudWatch

- **print()** and the **logging** module both send output to **CloudWatch Logs** for your Lambda function.
- Using **logging** gives you levels and consistent format:

```python
import logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

# In your code:
logger.info("Processing item %s", item_id)
logger.exception("Request failed: %s", str(e))  # logs stack trace
```

- Catch exceptions, log them with `logger.exception()` or `logger.error()`, then return an error response or re-raise as appropriate. Avoid unhandled exceptions that exit the function without logging.

---

## Exception Handling

Lambda functions should handle errors so that:

- Failures are **logged** with clear messages.
- The function returns a **consistent response shape** (e.g. status and body) instead of crashing with an unhandled exception.

Typical categories to handle:

- **Connection errors** – network failures, unreachable hosts, wrong ports.
- **API or HTTP errors** – non-2xx status codes, timeouts, invalid responses.
- **Invalid or missing environment variables** – missing keys, bad format, wrong types.
- **S3 errors** – permission denied, bucket not found, network issues.
- **JSON or parsing errors** – invalid response format.

Use **try/except** blocks, log every handled exception, and return a structured response (e.g. `{"statusCode": 200, "body": "..."}` on success and `{"statusCode": 500, "body": "..."}` on failure) so you can test and debug from the console and CloudWatch.

---

## Local Testing (Before Uploading)

You can run the same handler on your machine to test success and failure paths:

1. Set environment variables (e.g. in a terminal):

   ```bash
   export API_KEY="your-key"
   export BUCKET_NAME="your-bucket"
   ```

2. Call your handler from a small test script:

   ```python
   from lambda_function import lambda_handler
   result = lambda_handler({}, None)
   print(result)
   ```

3. Test both success and failure cases (e.g. invalid input, missing env vars, or a failing external call) and confirm that exceptions are handled and logged, and that no data is written when it should not be.

---

## Deployment Checklist

- [ ] Lambda function created with the correct runtime (e.g. Python 3.9+) and handler (e.g. `lambda_function.lambda_handler`).
- [ ] If the function must run on a schedule: EventBridge trigger added (Configuration → Triggers) with the desired rate or cron expression (e.g. `rate(1 hour)`).
- [ ] Environment variables set in the Configuration tab and read in code with `os.environ.get(...)`; missing or invalid values are validated and handled.
- [ ] Deployment package: if you use third-party libraries (e.g. `requests`), they are included in the zip; boto3 is provided by the runtime.
- [ ] Execution role has permissions for CloudWatch Logs and any other services your code uses (e.g. S3).
- [ ] Exception handling in place for connection errors, API/S3 failures, bad env vars, and parsing errors; all logged; function returns a consistent response shape.
- [ ] Local tests cover both success and failure paths; CloudWatch logs confirm behavior after deployment.
