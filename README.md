# CST8917 - Lab 4: PhotoPipe (Event-Driven Image Processing)

## 🎥 Demo Video
https://youtu.be/l52hed8qc_I

---

##  Introduction

In this lab, I built an event-driven image processing system called **PhotoPipe** using Azure services. The goal was to automatically process images when they are uploaded to Azure Blob Storage using Event Grid and Azure Functions.

The system takes uploaded images, extracts metadata, stores results in another container, and logs all activity in Table Storage.

---


## Architecture Overview

1. User uploads an image using `client.html`
2. Image is stored in `image-uploads` container
3. Event Grid detects the upload (BlobCreated event)
4. Two functions are triggered:
   - `process-image` → processes image and saves metadata
   - `audit-log` → logs event in Table Storage
5. Processed data is stored in `image-results` container
6. Web app displays results and audit logs

---

## Setup Instructions

### Step 1: Clone the Repository
```bash
git clone https://github.com/surt0008/CST8917_Lab4.git
cd CST8917_Lab4
```
### Step 2: Create Virtual Environment

```bash
python -m venv .venv
source .venv/bin/activate
```
### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4: Configure Local Settings

Create a file named `local.settings.json`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "STORAGE_CONNECTION_STRING": "<your-storage-connection-string>"
  }
}
```

### Step 5: Run Locally

```bash
func start
```

Test endpoints using `test-function.http`.

### Step 6: Deploy to Azure

- Create a **Function App** (Python, Consumption Plan)  
- Deploy using **VS Code**  
- Add environment variable:

```
STORAGE_CONNECTION_STRING = <your-storage-connection-string>
```

- Enable **CORS** (`*` for lab)


### Step 7: Configure Azure Resources

- Create a **Storage Account**  
- Create containers:
  - `image-uploads` (public)
  - `image-results` (private)  
- Enable **Blob anonymous access**  
- Configure **CORS for Blob service**

### Step 8: Configure Event Grid

Create a **System Topic** and two subscriptions:

#### 1. process-image-sub

**Filters:**
- Subject begins with:
  ```
  /blobServices/default/containers/image-uploads
  ```

**Advanced Filters:**
- Subject ends with `.jpg`
- Subject ends with `.png`

#### 2. audit-log-sub

**Filter:**
- Subject begins with:
  ```
  /blobServices/default/containers/image-uploads
  ```


### Step 9: Configure Web Client

- Generate a **SAS token** from the storage account  
- Open `client.html` using **Live Server**  

Enter the following details:
- Storage account name  
- SAS token  
- Function App URL  


### Testing

- Upload `.jpg` → processed + logged  
- Upload `.png` → processed + logged  
- Upload `.txt` → only logged (no processing)  

### Results

Results are displayed in:

- **Results tab** → Processed images  
- **Audit Log tab** → All uploads

##  Security Note

Sensitive files like `local.settings.json`, storage keys, and SAS tokens are not included in the repository.

---

##  Conclusion

This lab helped me understand how event-driven architecture works in Azure. I learned how Event Grid connects services and how Azure Functions can automatically react to events.

It also showed how to build a real-world pipeline where uploads trigger processing and logging without manual intervention.
