# Deploy to AWS using GitHub Actions

This project demonstrates a GitHub Actions workflow for deploying a web application to an AWS EC2 instance. The workflow handles the setup of an Apache2 web server on the EC2 instance and deploys an `index.html` file to the server.

## Project Overview

The goal of this project is to automate the deployment process using GitHub Actions. The workflow performs the following tasks:
1. **Checkout Code**: Retrieves the latest code from the GitHub repository.
2. **Setup SSH**: Configures SSH access to the AWS EC2 instance using a private key.
3. **Install Apache2**: Installs the Apache2 web server on the EC2 instance.
4. **Deploy Application**: Copies the `index.html` file to the EC2 instance for serving by Apache2.

## Workflow Configuration

### `.github/workflows/deploy_to_aws.yml`

This file defines the GitHub Actions workflow used for deployment. Here is the content of the workflow file:

```yaml
name: Deploy_to_AWS

env:
  NAME_OF_PRIVATE_KEY: "elad_ca-central-1.pem"
  EC2_PUBLIC_IP: "15.222.12.15" 

on:
  push:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  deploy_to_aws:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: mkdir .ssh and copy private key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.MY_PRIVATE_KEY }}" > ~/.ssh/${{ env.NAME_OF_PRIVATE_KEY }}
          chmod 600 ~/.ssh/${{ env.NAME_OF_PRIVATE_KEY }}
          ls -la ~/.ssh
          pwd
        
      - name: Install SSH client
        run: sudo apt install openssh-client

      - name: Install Apache2 on EC2
        run: |
          sudo ssh -o StrictHostKeyChecking=no -i ~/.ssh/${{ env.NAME_OF_PRIVATE_KEY }} ubuntu@${{ env.EC2_PUBLIC_IP }} 'sudo apt update && sudo apt install -y apache2'

      - name: Copy index.html to EC2
        run: |
          sudo scp -o  StrictHostKeyChecking=no -i ~/.ssh/${{ env.NAME_OF_PRIVATE_KEY }} /home/runner/work/Git_deploy_Sadna_2aws/Git_deploy_Sadna_2aws/index.html ubuntu@${{ env.EC2_PUBLIC_IP }}:/home/ubuntu
```

### Key Steps Explained

1. **Checkout Repository**: This step uses the `actions/checkout` action to fetch the latest code from the repository.

2. **Setup SSH**: This step creates an SSH configuration directory, adds the private key to it, and sets the correct permissions. The private key is stored securely in GitHub Secrets.

3. **Install SSH Client**: Installs the OpenSSH client on the runner to enable SSH operations.

4. **Install Apache2 on EC2**: Connects to the EC2 instance via SSH and installs Apache2. This is done using the `ssh` command to execute remote commands on the EC2 instance.

5. **Copy `index.html` to EC2**: Uses `scp` to securely copy the `index.html` file from the GitHub Actions runner to the EC2 instance. The file is placed in the `/home/ubuntu` directory on the instance.

## Setup and Usage

1. **Create a GitHub Repository**: Make sure you have a GitHub repository set up with the required source code.

2. **Add SSH Private Key to GitHub Secrets**:
   - Go to your GitHub repository's settings.
   - Navigate to "Secrets and variables" > "Actions".
   - Add a new secret named `MY_PRIVATE_KEY` with the contents of your SSH private key.

3. **Configure the Workflow**:
   - Create a file named `.github/workflows/deploy_to_aws.yml` in your repository.
   - Copy the workflow configuration into this file.

4. **Push Changes to Repository**:
   - Commit and push your changes to the `main` branch.
   - The workflow will trigger automatically upon each push to the `main` branch.

5. **Manual Trigger** (Optional):
   - You can also manually trigger the workflow from the GitHub Actions tab in your repository.

## Conclusion

This GitHub Actions workflow automates the deployment process to an AWS EC2 instance, ensuring that the latest version of your application is always deployed. It simplifies the deployment pipeline and integrates seamlessly with your GitHub repository.

Feel free to explore the repository and adapt the workflow configuration to suit your deployment needs!
