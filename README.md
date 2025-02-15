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





   
