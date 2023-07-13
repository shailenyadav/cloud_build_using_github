# cloud_build_using_github
CI/CD model using Cloud Build to retrieve code from GitHub and push it to a Compute Engine instance


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
To set up a CI/CD model using Cloud Build to retrieve code from GitHub and push it to a Compute Engine instance, you can follow these steps:

Step 1: Set up Cloud Build configuration file
Create a file named `cloudbuild.yaml` in the root of your project directory and paste the following code:

```yaml
steps:
  - name: 'gcr.io/cloud-builders/gcloud'
    entrypoint: 'bash'
    args:
      - '-c'
      - |
        gcloud compute ssh <vm_name> \
        --zone=<zone> \
        --project=$PROJECT_ID \
        --command='sudo apt update' \
        --command='sudo apt upgrade -y' \
        --command='sudo apt install php -y' \
        --command='sudo mkdir testfolder'
    env:
      - 'LOGGING_OPTION=CLOUD_LOGGING_ONLY'
logsBucket: onramp-sample-bucket
```

This configuration specifies the steps to be executed by Cloud Build, which include connecting to the Compute Engine instance and running commands to update packages, install PHP, and create a directory.

Step 2: Set up Cloud Build trigger
To automatically trigger the CI/CD process whenever changes are pushed to the GitHub repository, you need to set up a Cloud Build trigger.

1. Go to the Cloud Build section in the Cloud Console.
2. Click on "Triggers" in the left sidebar.
3. Click on "Create Trigger."
4. Choose your repository source (GitHub) and authenticate with your GitHub account.
5. Select the repository and branch you want to monitor for changes.
6. In the "Build configuration" section, select "Cloud Build configuration file (yaml or json)" and enter the path to your `cloudbuild.yaml` file (usually `/cloudbuild.yaml`).
7. Click on "Create Trigger" to save the trigger configuration.

Step 3: Set up Compute Engine instance
Ensure that you have a Compute Engine instance named `<vm_name>` in the `<zone>` zone. You don't need to set up SSH keys to establish a connection between Cloud Build and the Compute Engine instance, **cloud build does that part for you**

Step 4: Grant necessary permissions to Cloud Build service account
Cloud Build requires appropriate permissions to access and modify the Compute Engine instance.

1. Go to the IAM & Admin section in the Cloud Console.
2. Find the service account associated with your Cloud Build service.
3. Assign the `Compute Admin` role or a custom role with sufficient permissions to the service account.
   - Required permissions: `compute.instances.update`, `compute.instances.setMetadata`

Step 5: Test the CI/CD process
Make changes to your GitHub repository and push them to trigger the Cloud Build process.

1. Push your code changes to the branch configured in the Cloud Build trigger.
2. Go to the Cloud Build section in the Cloud Console.
3. Click on the triggered build to view the build details and logs.
4. Monitor the build process and check for any errors.
5. Once the build completes successfully, verify that the changes are reflected in the Compute Engine instance.

That's it! You have now set up a CI/CD model using Cloud Build to retrieve code from GitHub and push it to a Compute Engine instance.
