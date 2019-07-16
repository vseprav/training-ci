##  Jenkins Workshop (Part2)

### Build Triggers

1. Install "GitHub Pull Request Builder" Plugin

  * Manage Jenkins --> Manage Plugins --> Available --> GitHub Pull Request Builder


2. Configure plugin access to Git Repository

  * Manage Jenkins --> Configure System --> GitHub Pull Request Builder
  * Credential --> Add --> Secret text --> put `GitHub personal token` --> Add --> Test Credentials
  ```
  https://github.com/settings/tokens
  ```

3. Enable GitHub Pull Request Builder

  * Open Freestyle Job created in previous lesson -->
  Configure --> General --> set `GitHub project Url`

  * Freestyle Job --> Configure --> Build Triggers --> set GitHub Pull Request Builder


  In opened configuration tab set

  * Trigger phrase --> `build_training`
  * Crontab line --> `H/1 * * * *`
  * Build every pull request automatically without asking
  * Whitelist Target Branches --> `master`

4. Test build triggering

  * Create new git branch
  ```
  git checkout -b trigger
  ```
  * Edit README file or create a new one
  ```
  echo "Testing Build Trigger" > test_trigger.txt
  git add test_trigger.txt
  git commit -m "testing build trigger"
  git push
  ```
  * Create Pull Request from newly created branch to `master`
  * Wait a minute and check Jenkins Job
  * Trigger job from PR comment `build_training` and check in Jenkins UI


5. Install `ngrok` software to expose local Jenkins server to the internet and get public URL

  * Download from https://ngrok.com/download
  * Unzip binary file
    ```
    unzip ngrok-*.zip
    ```
  * Run command to expose Jenkins via http
    ```
    ./ngrok http 8080
    ```
  * Get public URL for building Webhooks integration, for example:
    ```
    http://53a7def7.ngrok.io
    ```


6. Configure web hooks for triggering Jenkins Job

  *  Open Jenkins Freestyle Job --> Configure --> Build Triggers --> GitHub Pull Request Builder --> set `Use github hooks for build triggering`

  * Open GitHub Repository Settings -->
  Webhooks --> Add webhook
  * Set Payload URL, example
  `http://b9a0f2dc.ngrok.io/ghprbhook/`
  * select individual events `Issue comments`, `Pull requests`
  * Add Webhook and test it with Pull Request comment or push event

### HomeWork. Create Pipeline Job

1. New Item --> Enter an item name ex. `Pipeline` --> Ok

  * Use environment variables in Pipeline for git url and credentialsId
  * Use parallel stages for running Tests,
  * Force your parallel stages to all be aborted when one of them fails
  * Use Post Condition Action
  * Delete workspace when build is done
  * Send notification if job failed (optional)
