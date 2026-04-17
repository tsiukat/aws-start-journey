
## 🍦🍦🍦 Static Website: Ice Cream Shop (AWS S3) 🍦🍦🍦
<img width="600" height="350" alt="Screenshot 2026-03-19 at 13 51 35" src="https://github.com/user-attachments/assets/95315ecd-1735-430c-80ee-e68720097dc4" />


We are a team of five building a modern, cloud-based web solution for a local ice cream shop.

Our goal is to solve operational challenges—such as order mix-ups and high booking demand—by moving the business to the AWS cloud.

Using **AWS S3**, **CloudFront**, and **Cognito**, we are developing a secure and scalable static website that:
- Improves customer experience  
- Simplifies order management  
- Provides a professional online presence



## 1. Start Sandbox

1. Click **Start Lab** to launch the lab.
2. Wait until **"Lab status: ready"** appears.
3. Close the lab panel.

 <img width="600" height="350" alt="image (2)" src="https://github.com/user-attachments/assets/b9b63ee4-3ce2-4734-8671-1077d521a84a" />

Management Console


## 2. Access AWS Management Console

1. Click **AWS** (top-left corner).
2. The AWS Management Console will open in a new tab and sign you in automatically.
3. If the tab doesn’t open:
   - Enable pop-ups in your browser
   - Try again
4. Arrange the AWS Console and these instructions side by side for easier setup.

 <img width="600" height="3500" alt="2" src="https://github.com/user-attachments/assets/134da7e9-9133-49cd-872a-29f0e45f5c9c" />



## 3. Create an S3 Bucket

1. Open the S3 console: https://console.aws.amazon.com/s3/
2. Click **Create bucket**.

<img width="600" height="7350" alt="3" src="https://github.com/user-attachments/assets/9c26c049-bc5d-4857-9fe9-4afa547bedba" />

3. Enter a bucket name: `static-website-icecream-shop`.
4. Select a **Region**:
   - Choose one close to you for lower latency and cost
   - The region determines your website endpoint
  
<img width="600" height="350" alt="4" src="https://github.com/user-attachments/assets/d91fc369-a5d3-45d7-a047-b6a9f522996f" />

5. Under **Block Public Access settings**:
   - Uncheck **Block all public access**
   - Check the acknowledgment box
6. Leave the remaining settings as default.
7. Click **Create bucket**.

<img width="600" height="350" alt="6" src="https://github.com/user-attachments/assets/53cafbfd-2e02-49a3-905a-825c359bfb7f" />



## 4. Enable Static Website Hosting

1. Open the S3 console: https://console.aws.amazon.com/s3/
2. Click **General purpose buckets**.
3. Select your bucket.
4. Go to the **Properties** tab.
 <img width="600" height="350" alt="7" src="https://github.com/user-attachments/assets/916b0101-bdbe-4b2e-966d-632334b91c33" />

5. Scroll to **Static website hosting** and click **Edit**.

<img width="600" height="350" alt="8" src="https://github.com/user-attachments/assets/215118ec-e27c-42d4-9658-8a638527a83a" />

6. Select **Use this bucket to host a website**.
7. Enable static website hosting.

<img width="600" height="350" alt="9" src="https://github.com/user-attachments/assets/73eed959-cb6d-43f9-bf3a-37dbdb51250d" />

8. Enter `index.html` as the **Index document** (case-sensitive).
9. Click **Save changes**.
10. Copy the **Website endpoint URL** to test your site.



## 5. Add Bucket Policy (Public Access)

1. Open the S3 console: https://console.aws.amazon.com/s3/
2. Select your bucket: `static-website-icecream-shop`.
3. Go to the **Permissions** tab.

<img width="600" height="350" alt="10" src="https://github.com/user-attachments/assets/0bb71b19-36fb-45e6-b5e4-28d7129e7680" />

4. Scroll to **Bucket policy** and click **Edit**.
5. Paste the policy below into the editor.
6. Ensure the bucket name is `static-website-icecream-shop`.
7. Click **Save changes**.

<img width="600" height="350" alt="11" src="https://github.com/user-attachments/assets/52a85835-15bb-4578-a153-f93abab7e52e" />


## 6. Configure Index Document

Upload your `index.html` file to your S3 bucket.

1. Create a file named `index.html` and save it locally.
2. Open the S3 console: https://console.aws.amazon.com/s3/
3. Select your bucket.
4. Upload the file:
   - Drag & drop `index.html` into the bucket, **or**
   - Click **Upload** and select the file
  
<img width="600" height="350" alt="12" src="https://github.com/user-attachments/assets/e187e30b-abf7-42c5-9444-5e0e7cf39740" />



## 7. Launch and Test the Website

1. Go to your S3 bucket.
2. Open the **Properties** tab.
3. Scroll to **Static website hosting**.
4. Copy the **Website endpoint URL**.
5. Open the URL in your browser.
6. Verify that your website loads correctly.

<img width="600" height="350" alt="13" src="https://github.com/user-attachments/assets/94136b4f-c867-4a90-b2d0-e5d1e015c004" />



## 8. Website Verification

- Opened the S3 **Website endpoint URL** in the browser
- 
<img width="600" height="350" alt="13" src="https://github.com/user-attachments/assets/94136b4f-c867-4a90-b2d0-e5d1e015c004" />


- **Home (Index Page):** confirmed the main page loads correctly
  
  <img width="600" height="350" alt="Screenshot 2026-03-19 at 13 51 35" src="https://github.com/user-attachments/assets/f77ac43a-f4e0-42ae-a1e9-ea0a753ef6d8" />


- **About the Shop:** verified business information is displayed
   
  <img width="600" height="350" alt="13" src="https://github.com/user-attachments/assets/d298af99-5542-473f-842a-75bc92b8ac39" />


- **Menu Highlights:** checked featured items are visible
  
<img width="600" height="350" alt="15" src="https://github.com/user-attachments/assets/e9134a1c-f8d5-40aa-8122-99dc86adcd48" />


- **Menu Variety:** ensured full menu options are displayed properly
  
<img width="600" height="350" alt="16" src="https://github.com/user-attachments/assets/6220e5df-1676-4e7f-bdf4-134782129bef" />


- **Customer Login:** tested login page access and functionality
  
<img width="600" height="350" alt="17" src="https://github.com/user-attachments/assets/e41a0f9a-3c46-4750-bfd0-ea9d80bfc94a" />


- **Book an Event:** verified booking section is accessible
  
<img width="600" height="350" alt="18" src="https://github.com/user-attachments/assets/29b738b2-aeed-45df-8aa6-0682ed0cf691" />

- **Contact Us:** confirmed contact details/form are working
  
<img width="600" height="350" alt="19" src="https://github.com/user-attachments/assets/c993238c-8419-443c-ae5f-d337834140fc" />
