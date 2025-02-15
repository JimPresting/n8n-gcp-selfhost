# Self-Hosting N8N on the Google Cloud with Auto-Updates
Guide on how to fully self-host n8n in a GCP project with up to no monthly costs (depending on the workflows you might pay networking costs, see: [GCP Network Pricing](https://cloud.google.com/vpc/network-pricing)) as well as auto-update the Docker image whenever the open-source GitHub repo of n8n has another release.

# Step 1: Setting up the GCP

1. Go to cloud.google.com and click on Console
2. Click on Try for free
![Screenshotgcptrial](https://github.com/user-attachments/assets/a35cdfad-fd96-440e-ac0d-2b650d094da2)
4. You need to add a billig method to verify your identity.
5. After you added your payment method and verified your identity you should click on Activate full account: 
![youreinfreetrial](https://github.com/user-attachments/assets/cbc28977-2088-4b31-a3f4-74264ba54d02)
--> This allows you to use the account after the 90-day trial period expires.
--> Bear in mind that after the trial period ends or if the credits are used up, you will be charged on the provided payment method. However, you can limit this risk by setting up budget alerts. Check [Google Cloud Budgets](https://cloud.google.com/billing/docs/how-to/budgets).

6. **Adding a New Project:** If you are starting with a new Google Cloud account, it may take some time before you can create a new project. Alternatively, you can click on the settings of your existing **"My First Project"** and rename it for better association with your project.
![image](https://github.com/user-attachments/assets/5ee0947d-3879-4d6b-8489-ebcf41aa2c18)
7. Whichever way you are using it, when selecting your project in the top left, it should say, "You've activated your full account."


# Step 2: Setting up the Cloud VM Instance

Now we set up the instance where N8N will run.

1. Make sure your project is selected
2. Click on VM Instances ![image](https://github.com/user-attachments/assets/ae8c5668-1b99-4bc8-b9bf-0bcd2716c759)
3. It might ask you to enable the Compute Engine API first (if you have just set up the account). Click **"Enable"** to proceed. ![image](https://github.com/user-attachments/assets/c5da1c83-f2cb-4c34-91c5-7e4cf8dbca0b)
4. Click on Create Instance
5. 5. In the configuration, you are generally free to set it up as you like. However, to host this instance for free, you need to check Google's requirements for the free-tier cloud setup: [Google Cloud Free Tier](https://cloud.google.com/free/docs/free-cloud-features#compute).  

As of now, the free-tier configuration is limited to:  
- **1 non-preemptible e2-micro VM instance** per month in one of the following US regions:  
  - **Oregon:** `us-west1`  
  - **Iowa:** `us-central1`  
  - **South Carolina:** `us-east1`  
- **30 GB-months** of standard persistent disk  
- **1 GB of outbound data transfer** from North America to all region destinations (excluding China and Australia) per month

6. So, click on **E2** and select the **Preset**. ![image](https://github.com/user-attachments/assets/d550b1e6-01e7-4025-9a6f-2a8cf2f5aa00)
Select e2-micro
7. Click on **OS and Storage**, then select **Change**. ![image](https://github.com/user-attachments/assets/60433d75-23bf-42c1-97cd-61ed870cf1d4)
8. Change Size (GB) to 30 and the Boot disk type to Standard Persistent Disk as per the guidelines.
9. It will show you a monthly estimate, as running multiple instances 24/7 would incur these costs for what you selected. However, since you have only one project, you will stay within the free-tier limits, and the only potential costs will be for bandwidth, depending on your workflows.
10. Give the instance a name (it doesn’t really matter—just make sure it's written without spaces) and click **Create**. Wait a minite until the Status says that it has compelted the setup. If it takes longer, refresh the page. ![image](https://github.com/user-attachments/assets/06328659-e37a-4350-8790-5113790a1a4a)


# Step 3: Setting up Docker






   
