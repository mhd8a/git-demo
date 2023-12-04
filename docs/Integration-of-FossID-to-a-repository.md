FossID is a powerful and versatile open-source software identification and compliance tool designed for managing software assets, ensuring license compliance, and minimizing legal and security risks associated with using open-source software components. FossID stands for "Free and Open Source Software Identification.

FossID offers a range of features designed to streamline open-source software identification and compliance:

- Component Identification: FossID analyzes source code, binaries, and package metadata  employs advanced scanning techniques to identify open-source components present in our software codebase. 
- License Analysis: One of the primary tasks of FossID is to determine the licenses associated with each identified component. 
- Security Vulnerability Detection: FossID also checks for known security vulnerabilities in the identified open-source components.
- Reporting and Documentation: The tool generates comprehensive reports detailing the open-source components found in the software project, along with their associated licenses and any detected security vulnerabilities. 

Requesting access to FossID: 
- Place ITSP order for FossID access management in : [Fossid access management](https://service-management.bosch.tech/sp?id=search_itsp&spa=1&t=rsc&q=FossID%20access%20management)
- Select the below options and submit the request form.

![image](https://media.github.boschdevcloud.com/user/16004/files/cea0e215-1de5-4d0f-822f-f9f772800eb2)

Access for JMAAS: 
 Get Admin permission to this JMaaS , https://rb-jmaas.de.bosch.com/Buildfarm_prd_JMAAS/ by writing mail to [CIBEE.Pilot-Build-IaaS@de.bosch.com](mailto:CIBEE.Pilot-Build-IaaS@de.bosch.com)  team.

Integrating FossID in our Repository:

Step 1: Access the JMAAS Portal
Begin by accessing the JMAAS portal, https://rb-jmaas.de.bosch.com/Buildfarm_prd_JMAAS/ .This portal serves as a platform for managing software repositories, facilitating integration, and ensuring compliance with open-source licenses.

Step 2. Select the Appropriate Organization
Within the JMAAS portal, select the organization to which the repository we want to integrate FossID belongs. 

Step 3. Create a New Item
Once we have selected the organization, proceed to create a new item, which represents the repository we intend to integrate with FossID. Choose a meaningful name that accurately represents the repository.Utilize the "Copy From" option to reference an existing repository that has configurations we would like to replicate. 

Step 4. Update Project Name and Scan Code
After setting up the repository, update the project name to match the specific project associated with the repository. Assign a unique project scan code. This code serves as an identifier for the project within FossID.

Step 5. Add a pipeline script
Sample pipeline script:

 @Library('pipeline-fossid-autoscan@master') _
//@Library('pipeline-fossid-autoscan@feature/VECU-BUILDER') _

pipeline {
    agent any

    stages {
        stage('OSS Analysis') {
            steps {
                script {
                    autoScan {
                        my_server_url = "https://rb-fossid.de.bosch.com/CR"
                        my_api_credentials = "fossid_api"
                        my_project_code = "Bios_Robotics"
                        my_scan_code = "Bosch_Buildfarm"
                        my_git_repo_url = "https://efn1cob:@github.boschdevcloud.com/bios-robotics/bosch_buildfarm.git"
                        my_git_branch = "master"
                        my_report_type = "dynamic"
                        my_report_selection_type = "include_all_licenses"
                        my_report_selection_view = "all_files"

                        // Scan settings
                        my_delta_only = "false"
                        my_scan_limit = 10
                        my_scan_sensivity = 10
                        my_full_file_match_only = "false"
                        my_recursively_extract_archives = "true"
                        my_jar_file_extraction = "always"

                        // Auto identification
                        my_auto_identification_detect_declaration = "false"
                        my_auto_identification_detect_copyright = "false"

                        scan_status_check_interval_sleeptime = 10

                        // Jenkins break build
                        my_fail_build_on_pending_identification = "false"
                        my_send_email_on_pending_identification = "true"
                        my_email_recipients_on_pending_identification = ["sree.ganeshvembuviswanathan@in.bosch.com"]

                        my_send_email_on_scan_completion = "true"
                        my_email_recipients_on_scan_completion = ["sree.ganeshvembuviswanathan@in.bosch.com"]

                        buildResultPendingId = "SUCCESS"
                        stageResultPendingId = "SUCCESS"
                        fossid_debug = "true"
                    }
                }
            }
        }
    }
}

On successful execution of the pipeline in Jenkins, the scanned repositories look like below:

![image](https://media.github.boschdevcloud.com/user/16004/files/1bf7adae-b8d9-435f-bc7b-8fcaeca35b63)

Step 6. Initiate a New Scan in FossID
Following the completion of the integration steps, FossID will automatically initiate a new scan for the repository. 
The FossId scan can be viewed from https://rb-fossid.de.bosch.com/CR/index.html. Choose Go to projects option , select the project name to display all the scans in the project.

![image](https://media.github.boschdevcloud.com/user/16004/files/34583780-1192-4436-a909-e951418df338)

End
