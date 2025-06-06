# Automated CI/CD Deployment for Your Project

This guide will walk you through setting up an automated deployment process for your application using GitHub Actions. When you push changes to the `master` branch of your repository, this workflow will automatically deploy them to your server.

## How It Works

The process is managed by a GitHub Actions workflow defined in the `.github/workflows/deploy.yml` file. Here's a quick rundown of what it does:

1. **On Push to Master**: The workflow is triggered every time you push new code to the `master` branch.
2. **Checkout Code**: It checks out the latest version of your code in the GitHub Actions runner environment.
3. **SSH and Deploy**: It connects to your server using SSH (with a username and password) and runs a series of commands to:
   * Navigate to your project's directory.
   * Pull the latest code from your GitHub repository.
   * Install or update any necessary Python packages from your `requirements.txt` file.
   * Restart your application to apply the new changes. We use `screen` in this example for simplicity.

## Setup Instructions

Follow these steps to get your automated deployment up and running.

### 1. Prepare Your Server

Before setting up the GitHub Action, make sure your server is ready for deployments:

* **Git**: Ensure Git is installed and that your project is cloned onto the server from your GitHub repository.
* **Python and Virtual Environment**: Your application should be able to run in a Python virtual environment to keep dependencies isolated. Create one if you haven't already:

```bash
python3 -m venv venv
```

* **Application Runner (Optional but Recommended)**: It's a good practice to run your application in a way that it continues running even after you disconnect from the SSH session. In our example, we use `screen`, a simple terminal multiplexer. You can install it on Debian/Ubuntu with:

```bash
sudo apt-get update
sudo apt-get install screen
```

### 2. Add Repository Secrets

To allow GitHub Actions to securely connect to your server, you need to store your SSH credentials as **secrets** in your GitHub repository. **Do not hardcode your password or any sensitive information directly in the `.yml` file.**

1. In your GitHub repository, go to **Settings** > **Secrets and variables** > **Actions**.
2. Click on **New repository secret** for each of the following:
   * `SSH_HOST`: The IP address or domain name of your server.
     * Example: `123.45.67.89`
   * `SSH_USER`: The username you use to log in to your server.
     * Example: `root` or `ubuntu`
   * `SSH_PASSWORD`: The password for that user.

### 3. Create the Workflow File

1. In your repository, create a directory named `.github`, and inside it, create another directory named `workflows`.
2. Inside the `.github/workflows` directory, create a new file named `deploy.yml`.
3. Copy and paste the content of the simplified `deploy.yml` provided in the previous step into this new file.

### 4. Customize the Workflow

Open the `deploy.yml` file and make the following changes to fit your project:

* `cd /path/to/your/app`: Change `/path/to/your/app` to the actual path where your project is located on your server.
* `uvicorn app.main:app ...`: If you're not using FastAPI with Uvicorn, or if your main application file is named differently, update this command to how you start your application.

### 5. Push and Deploy!

Commit these changes and push them to your `master` branch.

```bash
git add .github/workflows/deploy.yml
git commit -m "Add CI/CD deployment workflow"
git push origin master
```

You can now go to the **Actions** tab in your GitHub repository to see your deployment workflow in action! From now on, every time you push to the `master` branch, your changes will be automatically deployed.
