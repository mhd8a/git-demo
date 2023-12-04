SonarQube:

SonarQube is designed to enhance code quality by analyzing and inspecting source code for potential bugs, vulnerabilities, and code smells. Its a powerful tool for developers and teams striving to produce high-quality, secure, and maintainable code.
Some of the key feature of SonarQube includes,
- Static Code Analysis : Identifying issues without executing the code.
- Code Smells Detection : Identifying common code quality issues that might not result in immediate errors but can lead to maintainability problems over time
- Security Vulnerability Scanning : Identifying potential security vulnerabilities, such as SQL injection, cross-site scripting (XSS),
- Coverage Reports : Indicating which parts of the codebase are covered by automated tests.
- Customizable Quality Profiles : Users can define their own coding rules and quality standards to align with their project's requirements.


Requesting Access to SonarQube
To get access to SonarQube the user must be added to the distributed list : 
- Utilize the IT Service Portal service [Security Groups / Distribution Lists](https://service-management.bosch.tech/sp?id=sc_cat_item&sys_id=ae0fa1bb1b87551078087403dd4bcbf2) (order for self) and select option "Request/Remove membership from a Security Group"
- Utilize the search box "Search for managed Groups" to search for the group to be managed. Search for: " RB_SDE_Sonarqube_Buildfarm_Admin_UP ".

![image](https://media.github.boschdevcloud.com/user/16004/files/418f2418-d306-4af8-b027-adfda772f5dc)

- To add a user, click the "Add Member" button.
- Then enter the display names or user IDs of the users to be added.

![image](https://media.github.boschdevcloud.com/user/16004/files/0e2ed222-594c-44b5-8fd7-dc4ea3bf8f3c)

- Click "Add" to add all users. Verify the list of "Current Members" to make sure that all users have been added.

SonarQube integration enables us to automatically analyze and improve the code quality of our GitHub repositories. By following these steps, we can set up SonarQube analysis and receive notifications via Microsoft Teams, enhancing our development process.

Prerequisites:
Organization secrets must be configured in GitHub repository:
- SONAR_HOST_URL: The URL of our SonarQube enterprise server.
- SONAR_TOKEN: An authentication token for SonarQube access.
- TEAMS_WEBHOOK_URL: The Microsoft Teams webhook URL for receiving notifications.

Step 1: Add "sonar-project.properties" File

Sample sonar-project.properties file:
----------------------
sonar.sources=.
sonar.inclusions=**/*
sonar.exclusions=
sonar.projectKey=com.bosch.buildfarm.bios-robotics.bosch_buildfarm
sonar.projectName=bios-robotics/bosch_buildfarm
sonar.cfamily.build-wrapper-output=build
sonar.c.file.suffixes=.disable_sonarcfamily_c
sonar.cpp.file.suffixes=.disable_sonarcfamily_cpp
sonar.cxx.file.suffixes=.hxx, .hpp, .hh, .h, .cxx, .cpp, .cc, .c

Step 2: Add "sonarqube_scan.yml" File
-	Create a file named .github/workflows/sonarqube_scan.yml in repository.
-	Populate the file with the following content:
on:
  push:
    branches:
      - main
      - master
      - develop
      - 'releases/**'
  pull_request:
    types: [opened, synchronize, reopened]
  schedule:
    - cron: '30 04 * * 1-5'  # Run every day at 10:00 AM (IST)

name: Main Workflow

jobs:
  sonarqube:
    runs-on: [self-hosted, Linux]

    steps:
    - name: Checkout Code
      uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: SonarQube Scan
      id: sonarqube_scan
      uses: sonarsource/sonarqube-scan-action@master
      env:
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

    - name: Send Teams Notification
      if: ${{ always() }}  # Notify regardless of the outcome
      env:
        TEAMS_WEBHOOK_URL: ${{ secrets.TEAMS_WEBHOOK_URL }}
      run: |
        if [[ "${{ job.status }}" == "success" ]]; then
          status_text="succeeded"
          status_color="#36a64f"
        fi

        if [[ "${{ job.status }}" == "failure" ]]; then
         status_text="failed"
         status_color="#d9534f"
        fi

        
        report_url=https://sonarqube.dev.bosch.com/projects /dashboard?id=com.bosch.buildfarm.bios-robotics.bosch_buildfarm ( replace id with the project key in sonar.properties file)
        payload="{
          \"@type\": \"MessageCard\",
          \"themeColor\": \"${status_color}\",
          \"title\": \"GitHub Actions Workflow\",
          \"text\": \"Workflow has ${status_text}: ${{ github.repository }}\",
          \"sections\": [
            {
              \"activityTitle\": \"SonarQube Analysis\",
              \"activitySubtitle\": \"${status_text}\",
              \"activityImage\": \"https://www.sonarqube.org/logos/index/favicon.png\",
              \"facts\": [
                {
                  \"name\": \"Repository\",
                  \"value\": \"${{ github.repository }}\"
                },
                {
                  \"name\": \"Report\",
                  \"value\": \"[SonarQube Analysis Report](${report_url})\"
                }
              ]
            }
          ]
        }"

curl -X POST -H "Content-Type: application/json" -d "$payload" $TEAMS_WEBHOOK_URL

Step 3: Automatic SonarQube Scan and Notifications
- With the files in place, any push to the branch (e.g., main) will trigger the SonarQube scan workflow.
- The workflow checks out the repository, sets up the necessary environment, and runs the SonarQube scan using the defined properties.
- Find the below screenshot for sample SonarQube scan:

![image](https://media.github.boschdevcloud.com/user/16004/files/f793e5b4-121c-4e90-8a03-c398872c1a86)

- After the scan, the workflow sends a notification to the specified Microsoft Teams webhook URL.

![image](https://media.github.boschdevcloud.com/user/16004/files/3fce50a8-60c2-47f4-962d-ba943b203dba)

