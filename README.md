# Self-Hosting N8N on the Google Cloud with Auto-Updates
Guide on how to fully self-host n8n in a GCP project with up to no monthly costs (depending on the workflows you might pay networking costs, see: [GCP Network Pricing](https://cloud.google.com/vpc/network-pricing)) as well as auto-update the Docker image whenever the open-source GitHub repo of n8n has another release. The only two things you need to replicate this process 100% are a **credit/debit card** and a **domain**.

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
2. Click on VM Instances
![image](https://github.com/user-attachments/assets/c236c3d9-d07a-403e-b8dd-002b7979bdcd)
3. It might ask you to enable the Compute Engine API first (if you have just set up the account). Click **"Enable"** to proceed.
![image](https://github.com/user-attachments/assets/c5da1c83-f2cb-4c34-91c5-7e4cf8dbca0b)
4. Click on Create Instance
5. In the configuration, you are generally free to set it up as you like. However, to host this instance for free, you need to check Google's requirements for the free-tier cloud setup: [Google Cloud Free Tier](https://cloud.google.com/free/docs/free-cloud-features#compute).  

As of now, the free-tier configuration is limited to:  
- **1 non-preemptible e2-micro VM instance** per month in one of the following US regions:  
  - **Oregon:** `us-west1`  
  - **Iowa:** `us-central1`  
  - **South Carolina:** `us-east1`  
- **30 GB-months** of standard persistent disk  
- **1 GB of outbound data transfer** from North America to all region destinations (excluding China and Australia) per month

6. So, click on **E2** and select the **Preset**. ![image](https://github.com/user-attachments/assets/d550b1e6-01e7-4025-9a6f-2a8cf2f5aa00)
Select e2-micro
7. Click on **OS and Storage**, then select **Change**.
![image](https://github.com/user-attachments/assets/60433d75-23bf-42c1-97cd-61ed870cf1d4)
8. Change Size (GB) to 30 and the Boot disk type to Standard Persistent Disk as per the guidelines.
9. It will show you a monthly estimate, as running multiple instances 24/7 would incur these costs for what you selected. However, since you have only one project, you will stay within the free-tier limits, and the only potential costs will be for bandwidth, depending on your workflows.
10. Give the instance a name (it doesn’t really matter—just make sure it's written without spaces) and click **Create**. Wait a minite until the Status says that it has compelted the setup. If it takes longer, refresh the page.
![image](https://github.com/user-attachments/assets/06328659-e37a-4350-8790-5113790a1a4a)
11. Before setting up everything in the shell make sure to make the external IP static so that when an error occurs and you need to restart the instance it will still be pointing on your subdomain. 
- click on VPC Network, IP addresses.
![image](https://github.com/user-attachments/assets/f2fcb8bb-7ce2-473e-adb9-6fd66b48fd37)
- It will show **two IP addresses**: one **internal** and one **external**. For the **external** IP, click on the **three dots** and select **"Promote to static IP address."**. Name again doesn't matter.
![Screenshot 2025-02-15 114830](https://github.com/user-attachments/assets/e7177a19-c62a-42d8-98fd-8830492b6a00)

Lastly Select "Allow" on these 3 traffic sources. You can edit that later in the VM instance as well but we need it in order to reach our domain via our subdomain.
![image](https://github.com/user-attachments/assets/d6bf6370-64c9-4417-9dcd-ab78b8188059)


# Step 3: Setting up N8N

Now, go back to the **VM instance** we created and click on **"Connect SSH."**
![image](https://github.com/user-attachments/assets/038ee935-c057-4067-b5f0-e155eaf1855d)
Authorize with your **Google Account** (note that the connection may drop frequently). If that happens, just **reconnect and reauthorize**.  
Depending on where you left off, you might need to **reinstall** or **remove** a partially installed instance. This happened to me quite often. In such cases, just ask **ChatGPT** how to uninstall the instance, and then you can restart the setup.
Now enter the following commands after one another:


1. **Update the Package Index:**
   ```bash
   sudo apt update

2. **Install Docker:**
    ```bash
    sudo apt install docker.io
Click on Y

3.  **Start Docker:**
    ```bash
    sudo systemctl start docker

4. **Enable Docker to Start at Boot:**
    ```bash
    sudo systemctl enable docker



## Step 3.1: Setting up a Subdomain to point to your Google Cloud Instance

Now we want to add a subdomain of your domain which makes it easy to access your N8N instance from everywhere (dont worry you will need an account). 
With your domain provider go to Edit DNS settings (every domain provider has this) then you want to add a New Record. 

copy the external IP address which we made static before:
![Screenshot 2025-02-15 120154](https://github.com/user-attachments/assets/ea068f0c-8076-40ca-b86d-4f74b82a12fb)

For the **new record**, select **Type A** and name it whatever you like, but keep it **short and precise**. **Points to:** Paste the **external IP address**. **TTL:** Default is **14400**; you can leave it as is.
![image](https://github.com/user-attachments/assets/615b06e7-ab76-4dbc-8927-8dc355f4ba73)




## Step 3.2: Starting n8n in Docker
Run the following command to start n8n in Docker. Replace **your-domain.com** with your actual domain name. **Make sure you don’t copy and paste it with the "bash" part and the three backticks (` ``` `), or it won’t work.**


We are using a subdomain, it should look like this: 
(The subdomain is what we defined as the name—in my example, **myn8n**.)

    ```bash
    sudo docker run -d --restart unless-stopped -it \
    --name n8n \
    -p 5678:5678 \
    -e N8N_HOST="myn8n.your-domain.com" \
    -e WEBHOOK_TUNNEL_URL="https://myn8n.your-domain.com/" \
    -e WEBHOOK_URL="https://myn8n.your-domain.com/" \
    -v ~/.n8n:/root/.n8n \
    n8nio/n8n
    ```
It now downloads the latest **n8n** image. Since this is the first installation, it obviously can’t find **n8n:latest** in your directory, but that’s not a problem.
![image](https://github.com/user-attachments/assets/dd85386c-8807-43af-b25a-77ab298a659e)


## Step 3.3: Installing Nginx

We need **Nginx** as a **reverse proxy** to route traffic to n8n, handle **SSL encryption**, and allow access via a custom domain. Without it, n8n would only be reachable through its internal port (5678), which is not ideal for public access. An alternative is using a **Google Cloud Load Balancer**, but it’s more complex and can incur additional costs. Nginx is lightweight, free, and gives full control over traffic and security. It simplifies setup while ensuring a secure and accessible deployment.

1. **Install Nginx:**
    ```bash
    sudo apt install nginx

  Click Y

2. **Configuring Nginx**
    
Configure Nginx as a reverse proxy for the n8n web interface. This can be a bit tricky, so proceed carefully.

```bash
sudo mkdir -p /etc/nginx/sites-available /etc/nginx/sites-enabled
```
Now edit the config file. 
```bash
sudo nano /etc/nginx/sites-available/n8n.conf
```
Paste the following content (replace with your actual domain and subdomain):  

```nginx
server {
    listen 80;
    server_name myn8n.my-domain.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```
Save with **Ctrl + O**, **Enter**, then exit with **Ctrl + X**.  

```bash
sudo ln -s /etc/nginx/sites-available/n8n.conf /etc/nginx/sites-enabled/
```

```bash
sudo nginx -t
sudo systemctl restart nginx
```


## Step 3.4: Setting up SSL with Certbot

Certbot will obtain and install an SSL certificate from Let's Encrypt.

1. **Install Certbot and the Nginx Plugin:**
    ```bash
    sudo apt install certbot python3-certbot-nginx
Click Y

2. **Obtain an SSL Certificate:**

Here, we need to configure the firewall settings to allow HTTP and HTTPS traffic. If you haven't done this before, you can easily add firewall rules globally for the project or edit the VM instance settings.
    
Adjust the subdomain and domain.
```bash
sudo certbot --nginx -d myn8n.stardawnai.com
```
Enter an Email and select Y
Second one you can enter Y or N doesn't matter. It should work. If an error occurs it's propably due to the Firewall settings not being set up correctly. 

![Screenshot 2025-02-15 131657](https://github.com/user-attachments/assets/984cd5fa-a541-4066-b2ac-17424a10fc94)

When entering your domain (with the subdomain) in the browser, it should look like this:
![image](https://github.com/user-attachments/assets/84372133-c1d3-4df6-a5db-94871cb97943)

Bear in mind that this has nothing to do with any n8n accounts you might already have. You are setting it up from scratch, and it will only work on this VM instance.

# Step 3: Setting up Auto Updates for N8N

Since the official n8n repository regularly adds new features, it's important to stay updated without manually downloading, uploading workflows, or reconfiguring credentials. To automate this, we create a **cronjob** that checks for new **stable releases** (not pre-releases) in the n8n GitHub repository every **Sunday night**. If an update is available, it automatically updates the Docker image. Before updating, it saves your configurations in a new folder named **update_n8n**.

Bear in mind that the first part of your SSH prompt is your Google Account name, and the second part is the name of your VM instance.  
For example, if your VM instance is called **myvmn8n** and your Google Account is **johndoe@gmail.com**, your SSH prompt will show:  
```bash
johndoe@myvmn8n
```
![image](https://github.com/user-attachments/assets/9d6aeada-7047-4b45-8b0c-1b86548e795a)



### **Auto-Update Script for n8n**  

#### **1. Create or Edit the Update Script**  
```bash
nano /home/mygoogleaccount/update_n8n.sh
```
**Add the following content:**  
```bash
#!/bin/bash
# Backup current n8n directory
BACKUP_DATE=$(date +'%Y-%m-%d_%H-%M-%S')
cp -r /home/mygoogleaccount/.n8n /home/mygoogleaccount/.n8n-backup-$BACKUP_DATE

# Pull latest n8n version
sudo docker pull n8nio/n8n:latest

# Stop and remove old container
sudo docker stop n8n
sudo docker rm n8n

# Start new container with correct volume
sudo docker run -d --restart unless-stopped -it \
  --name n8n \
  -p 5678:5678 \
  -e N8N_HOST="myn8n.your-domain.com" \
  -e WEBHOOK_TUNNEL_URL="https://myn8n.your-domain.com/" \
  -e WEBHOOK_URL="https://myn8n.your-domain.com/" \
  -v /home/mygoogleaccount/.n8n:/home/node/.n8n \
  n8nio/n8n

# Clean up old Docker images
sudo docker image prune -af
```
Save with **Ctrl + O, Enter**, then exit with **Ctrl + X**.

---

#### **2️⃣ Make the Script Executable**  
```bash
chmod +x /home/mygoogleaccount/update_n8n.sh
```

---

#### **3. Set Up a Weekly Cronjob (Sunday at 3 AM)**  
Open the crontab:  
```bash
crontab -e
```
Select nano
![image](https://github.com/user-attachments/assets/bd9c300d-eacb-4b79-a1ee-471c85e301cd)


Add this line (you can remove the comments before):  
```bash
0 3 * * 0 /home/mygoogleaccount/update_n8n.sh >> /var/log/update_n8n.log 2>&1
```
Save and exit.

0 3 * * 0 means:  
- **0** → Minute (Runs at minute **0**, i.e., the start of the hour)  
- **3** → Hour (Runs at **3 AM**)  
- **`*`** → Day of the month (Runs **every day of the month**)  
- **`*`** → Month (Runs **every month**)  
- **`0`** → Day of the week (**0 = Sunday**, 1 = Monday, ..., 6 = Saturday)  

This schedule runs the script **every Sunday at 3:00 AM**.

---

#### **4. Test & Verify**  
- **Run the script manually:**  
  ```bash
  /home/mygoogleaccount/update_n8n.sh
  ```
  Since we just installed N8N it should not find a newer version so it should look like this:
  ![image](https://github.com/user-attachments/assets/5ff92c8d-11d2-4bc5-aebd-72c79e9d1c49)

  
  
- **Check backup directory:**  
  ```bash
  ls /home/mygoogleaccount/ | grep .n8n-backup
  ```
- **Verify Docker volume binding:**  
  ```bash
  docker inspect n8n | grep Mounts -A 10
  ```
  Expected output:  
  ```json
  "Source": "/home/mygoogleaccount/.n8n",
  "Destination": "/home/node/.n8n",
  ```

---

### **Summary**
 A **backup** of `.n8n` is created before each update.  
 The **container restarts** with the old data preserved.  
 The **cronjob automates updates** every Sunday at 3 AM.  

Now, please make sure that when setting up your private n8n account, you watch out for the correct path the next time you use it. It should be **`.../home`** so that it doesn’t ask you to sign up again, as there is no login page from the signup screen. It may seem trivial, but if you don’t notice it, you might think your data is lost.


Hope this helps you set up and automate your n8n instance! 🚀 For more on how to effectively set up workflows that truly help you or your business be more efficient, check out our YouTube channel: [StardawnAI](https://www.youtube.com/@StardawnAI). Thank you! 😊
