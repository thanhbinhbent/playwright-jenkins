# Integrate Playwright with Jenkins for Automated Testing

This guide will help you integrate Playwright with Jenkins to automate your end-to-end testing.

## Prerequisites

- **Jenkins** installed and configured.

- **Node.js** and **npm** installed.

- **Playwright** installed in your project.

## Step-by-Step Guide

### Step 1: Install Playwright

If you haven't already, install Playwright in your project:

```sh
npm install @playwright/test
```

### Step 2: Create a Playwright Test Script

Create a basic test script in your project. For example, create a file `example.spec.js`:

    const { test, expect } = require('@playwright/test');

    test('basic test', async ({ page }) => {
      await page.goto('https://playwright.dev/');
      const title = await page.title();
      expect(title).toBe('Fast and reliable end-to-end testing for modern web apps | Playwright');
    });

### Step 3: Configure Jenkins

1.  **Install Node.js Plugin**:

    - Go to `Manage Jenkins` > `Manage Plugins` > `Available` and search for `NodeJS Plugin`.
    - Install it and restart Jenkins if necessary.

2.  **Add Node.js to Jenkins**:

    - Go to `Manage Jenkins` > `Global Tool Configuration`.
    - Scroll down to `NodeJS` and click `Add NodeJS`.
    - Provide a name (e.g., `NodeJS 14`), and select the version you want to install. Make sure to check `Install automatically`.

3.  **Create a Jenkins Pipeline Job**:

    - Go to `New Item`, enter a name for your job, select `Pipeline`, and click `OK`.
    - In the `Pipeline` section, select `Pipeline script` and add the following:

>     pipeline {
>         agent any
>         tools {
>             nodejs 'NodeJS 14' // Use the name you provided in the Global Tool Configuration
>         }
>         stages {
>             stage('Install dependencies') {
>                 steps {
>                     sh 'npm install'
>                 }
>             }
>             stage('Run Playwright Tests') {
>                 steps {
>                     sh 'npx playwright test'
>                 }
>             }
>         }
>         post {
>             always {
>                 archiveArtifacts artifacts: '**/test-results/*.*', allowEmptyArchive: true
>                 junit 'test-results/*.xml'
>             }
>         }
>     }

### Step 4: Configure Playwright for Jenkins

Playwright generates test results in the JUnit format by default, which Jenkins can process. Make sure your `playwright.config.js` includes the following to specify the output directory for test results:

    const config = {   reporter: [['junit', { outputFile:
    'test-results/results.xml' }]], };

    module.exports = config;`

### Step 5: Run Your Jenkins Job

1.  Save the configuration and click `Build Now`.
2.  Monitor the build process in the `Console Output`.
3.  After the build completes, check the `Test Result` to see the results of your Playwright tests.

## Troubleshooting

- **Node.js Not Found**: Ensure Node.js is installed and configured correctly in Jenkins.
- **Permission Issues**: Make sure Jenkins has the necessary permissions to access your project files and execute scripts.
- **Test Failures**: Check the `Console Output` and Playwright's test report for detailed error messages.
