Setting up email notifications in Jenkins CI/CD helps notify teams about build status, errors, or successful deployments. Here's how you can set up email notifications in Jenkins:

### 1. **Install the Email Extension Plugin**
First, ensure that the **Email Extension Plugin** is installed.

#### Steps:
- Go to **Jenkins Dashboard**.
- Navigate to **Manage Jenkins** > **Manage Plugins**.
- Under the **Available** tab, search for `Email Extension Plugin` and install it. 
  (If already installed, ensure it's updated.)

### 2. **Configure SMTP Server for Jenkins**
Once the plugin is installed, configure the SMTP server Jenkins will use to send email notifications.

#### Steps:
- Go to **Manage Jenkins** > **Configure System**.
- Scroll down to the **Extended E-mail Notification** section.
- Fill in the required fields:
  - **SMTP server**: The email server you are using, e.g., `smtp.gmail.com` for Gmail.
  - **Default user e-mail suffix**: For example, `@yourcompany.com`.
  - **Use SSL**: Enable if required by your email provider.
  - **SMTP Port**: The port to use for the SMTP server (e.g., 465 for SSL, 587 for TLS).
  - **Authentication**: If your SMTP server requires authentication, check this box and provide the **Username** and **Password** (e.g., email address and its password or app-specific password).

#### Example:
- SMTP server: `smtp.gmail.com`
- SMTP port: `465` (SSL)
- Authentication: Username = `youremail@gmail.com`, Password = `yourpassword` or app password.

> **Note:** If you use a Gmail account, you might need to enable **less secure apps** or generate an **app password** for more security.

### 3. **Configure E-mail Notification in Jenkins Jobs**
Once the SMTP configuration is done, configure email notifications in your Jenkins job.

#### Steps:
- Open the Jenkins **job** (freestyle or pipeline) you want to configure email notifications for.
- Click on **Configure**.
- Under **Build Triggers**, you can choose when to trigger the email notification (e.g., on failure, success, unstable, etc.). Typically, email notifications are set under the **Post-build Actions** section.
  
  For a **Freestyle Job**:
  - Scroll down to **Post-build Actions**.
  - Click on **Add post-build action** and select **Editable Email Notification**.
  - In the **Recipients** field, add the email addresses that should receive notifications. You can use:
    - **$BUILD_USER** to send notifications to the user who triggered the build.
    - **user@example.com** to send to specific users.
  - Configure the **Triggers** (e.g., send email on `Success`, `Failure`, `Unstable`, or `Always`).
  - You can set up custom content for the email using the **Subject** and **Content** fields. Jenkins provides variables like `$BUILD_STATUS`, `$BUILD_URL`, and others to include dynamic content.

  Example:
  - **Subject**: `Build ${BUILD_STATUS}: ${JOB_NAME} #${BUILD_NUMBER}`
  - **Content**: `Build result: ${BUILD_STATUS}\nSee details at ${BUILD_URL}`

  You can also configure a **pre-send script** if you want more advanced handling, such as modifying the email based on certain conditions.

#### For a **Pipeline Job**:
To configure email notifications in a pipeline job, you can use the `emailext` step in your Jenkinsfile.

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
    post {
        success {
            emailext(
                subject: "Build Success: ${JOB_NAME} #${BUILD_NUMBER}",
                body: "Good news! The build was successful. You can see the results here: ${BUILD_URL}",
                to: 'user@example.com'
            )
        }
        failure {
            emailext(
                subject: "Build Failed: ${JOB_NAME} #${BUILD_NUMBER}",
                body: "Oops! The build failed. Check the logs at ${BUILD_URL}",
                to: 'user@example.com'
            )
        }
        unstable {
            emailext(
                subject: "Build Unstable: ${JOB_NAME} #${BUILD_NUMBER}",
                body: "The build is unstable. Check the details at ${BUILD_URL}",
                to: 'user@example.com'
            )
        }
    }
}
```

### 4. **Test the Email Configuration**
To ensure the email configuration works, you can trigger a build and verify if the emails are sent based on the defined conditions.

- You can also test the email configuration from **Manage Jenkins** > **Configure System** by clicking **Test Configuration** under the **Extended E-mail Notification** section.

### Additional Tips:
- **Email Notifications for Build Users**: If you want Jenkins to notify the user who triggered the build, you can use the `${BUILD_USER}` variable. This is particularly useful for tracking who is responsible for a build and keeping them updated.
- **SMTP Providers**: You can use other email services like SendGrid, AWS SES, or others for better scalability and performance if needed.
- **Email Content Customization**: You can customize the subject, body, and recipients based on the build status. You can even use HTML formatting in the email body for better presentation.

Now, your Jenkins jobs should be able to send email notifications to the relevant recipients based on build outcomes!
