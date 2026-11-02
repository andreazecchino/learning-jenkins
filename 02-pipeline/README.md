# Create a Pipeline

1. Login and select **New Item** from the Jenkins Dashboard. <p align="left"><img src="assets/img/new-item.png" /></p>

2. Select **Pipeline**, choose the name for your project then click **OK** at the end of the page to open the configuration page. <p align="left"><img src="assets/img/pipeline-project.png" /></p>

3. In **General** section, apply a max number of builds to keep, prevent concurrent builds and deny pipeline resume it the controller restarts. <p align="left"><img src="assets/img/general.png" /></p>

4. From the **Definition** field in **Pipeline** section, choose the **Pipeline script from SCM** option. Choose a Git repository and a branch where to pull the latest code for your builds. For this learning path, I chose a small Python project that I worked on. <p align="left"><img src="assets/img/pipeline.png" /></p>
This is the [Jenkinsfile](Jenkinsfile) used.

5. Click **Save** to open the project view page. On this page, click **Build Now** to start the process. Under **Builds** it's possible to access the details for this particular run. <p align="left"><img src="assets/img/build-now.png" /></p>

6. Click **Console Output** to see the output of the process. <p align="left"><img src="assets/img/console-output.png" /></p>
