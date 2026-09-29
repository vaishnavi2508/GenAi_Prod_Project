# Week 1 - Technical Questions & Answers

### Q1. What is Amazon S3 and what does S3 stand for?
**Answer:**
Amazon S3 stands for **Simple Storage Service**. It is an object storage service provided by AWS that offers industry-leading scalability, data availability, security, and performance. It allows users to store and retrieve any amount of data at any time from anywhere on the web.

---

### Q2. When you created your S3 bucket, you selected the region "us-east-1". What is a region in AWS and why does it matter?
**Answer:**
An **AWS Region** is a physical geographical location in the world where AWS clusters its data centers. Selecting the right region matters because:
1. **Latency:** Choosing a region physically closer to your end-users reduces latency and speeds up data access.
2. **Compliance & Legal Restrictions:** Certain laws require data to reside within specific geographical boundaries.
3. **Cost & Feature Availability:** AWS pricing and feature availability can vary slightly from region to region.

---

### Q3. Inside your S3 bucket, you created a folder called "data" before uploading the CSV. Why do we create folders inside S3 instead of directly uploading the file to the root of the bucket?
**Answer:**
Although Amazon S3 uses a flat object store architecture (where folders do not physically exist like a traditional file system), creating folder-like prefixes helps in:
1. **Organization:** Keeps files structured and manageable as data grows.
2. **Access Control (IAM Policies):** Allows granting specific permissions to entire prefixes/folders rather than individual files.
3. **Data Pipeline Management:** Enables automated scripts and tools to easily target specific directory paths for processing.

---

### Q4. What is the difference between a Bucket Name and a File Key in S3?
**Answer:**
* **Bucket Name:** The top-level globally unique container that holds your data in S3 (e.g., `ai-intern-data-vaishnavi`).
* **File Key:** The full relative path and filename that uniquely identifies an object within a bucket, including any folder prefix (e.g., `data/customer_support_tickets.csv`).

---

### Q5. After uploading the CSV to S3, what is the full S3 path of your file? Write it out.
**Answer:**
`s3://ai-intern-data-vaishnavi/data/customer_support_tickets.csv`

## Part 2: SageMaker & Technical Questions

Q1. What is Amazon SageMaker Studio and how is it different from writing Python code on your laptop?
Answer:
SageMaker Studio is a cloud-based coding space built by AWS specifically for data science and machine learning.

The main differences from coding on a laptop are:
- Power: It uses AWS cloud servers instead of your laptop's memory and processor, so it can handle heavy tasks easily.
- Easy S3 Access: It connects directly with S3 and other AWS tools without needing tricky network setups.
- ML Built-in: It comes with pre-built tools for training, tracking, and deploying models.

--------------------------------------------------

Q2. In Step 7 you attached a policy called "AmazonS3FullAccess" to the SageMaker IAM Role. What is an IAM Role and why did SageMaker need this permission to access S3?
Answer:
An IAM Role is like a digital ID card in AWS that gives a service temporary permission to do specific tasks.

SageMaker needed this permission because AWS blocks all connections between services by default for security. Attaching AmazonS3FullAccess to the role told AWS: "It's okay, let SageMaker read and write files in our S3 buckets."

--------------------------------------------------

Q3. In your notebook you used three libraries — pandas, boto3, and io. What does each one do? Explain each in one line.
Answer:
- pandas: Helps load, clean, and analyze data using tabular tables called DataFrames.
- boto3: The Python SDK that lets our code talk directly to AWS services like S3.
- io: Handles raw data streams in memory so pandas can read bytes directly from S3 as a file.

--------------------------------------------------

Q4. What is a Jupyter Notebook kernel? When you created your notebook you selected "Python 3 (Data Science)" — why did you choose that specific kernel and not just "Python 3"?
Answer:
A kernel is the engine running behind the notebook that actually executes your Python code.

I picked Python 3 (Data Science) because it comes with essential libraries like pandas, boto3, and numpy pre-installed. The plain "Python 3" kernel is blank, meaning I would have to waste time installing everything manually using pip.

--------------------------------------------------

Q5. In your code you used s3.get_object(Bucket=BUCKET, Key=KEY) to load the file. Explain what each part of this line does — what is get_object, what is Bucket, and what is Key?
Answer:
- get_object: The command that tells AWS to go fetch a file from S3.
- Bucket=BUCKET: The name of the specific S3 folder/bucket where the file is stored.
- Key=KEY: The exact path and filename of the file inside that bucket.

--------------------------------------------------

## Part 3: Scenario Based Questions

Q6. You opened SageMaker Studio and your notebook is showing a "Kernel is dead" error. What does this mean and what are the steps you would take to fix it?
Answer:
What it means: The background Python process crashed—usually because it ran out of RAM or CPU memory while processing data.

How to fix it:
1. Click Kernel -> Restart Kernel in the top menu.
2. If it crashes again, switch to a bigger SageMaker instance type with more RAM.
3. Check the code to make sure huge files aren't being loaded into memory all at once.

--------------------------------------------------

Q7. You ran the data loading cell and got the error "AccessDenied". Your bucket name and file key are both correct. What is the most likely cause and how do you fix it?
Answer:
Likely cause: The IAM Role attached to SageMaker doesn't have permissions to read from S3.

How to fix it:
1. Open the AWS IAM Console.
2. Find the SageMaker execution role.
3. Attach AmazonS3FullAccess (or a policy allowing s3:GetObject) to the role.

--------------------------------------------------

Q8. Your teammate says — "Why are we using SageMaker to write code? I can just use Google Colab for free." How do you explain to them why SageMaker is the better choice for this specific project?
Answer:
- Data Security: SageMaker stays inside our AWS account, keeping private data safe and compliant with company security rules.
- Direct AWS Connection: It talks to S3 directly and fast, without needing public download links.
- Production Ready: When the notebook is ready, SageMaker makes it easy to deploy the model directly into production.

--------------------------------------------------

Q9. After running df.head() you can see the data loaded correctly but df.shape shows only 500 rows instead of 8,469. What could have gone wrong and how do you investigate?
Answer:
What went wrong: The CSV file might have a corrupt or broken row around row 500 that stopped pandas from reading further, or someone accidentally added a limit like nrows=500 in the code.

How to investigate:
1. Check the code to make sure nrows=500 wasn't added by mistake.
2. Print the exact file size downloaded from S3 using Python to see if the whole file was fetched.
3. Inspect rows 500–505 in the raw text file to spot any bad characters or broken lines.

--------------------------------------------------

Q10. You closed SageMaker Studio and reopened it the next day. Your notebook is there but all the variables are gone — df is showing "NameError: name df is not defined". Why does this happen and what is the correct way to fix it?
Answer:
Why it happens: When you close SageMaker, the kernel turns off to save memory. The code on screen stays saved, but all temporary variables stored in memory get wiped clean.

How to fix it: Just re-run the code cells from top to bottom by clicking Run -> Run All Cells Above to reload all variables back into memory quickly.