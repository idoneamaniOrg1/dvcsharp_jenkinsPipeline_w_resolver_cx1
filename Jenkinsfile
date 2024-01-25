pipeline {
    agent any
    environment {

    branchName = 'master'
    projectName = 'jenkins/dvcsharp_jenkinsPipeline_w_resolver_cx1'
    projectTags = 'jenkins_sca_resolver'
    repoUrl = 'https://github.com/idoneamaniOrg1/dvcsharp_jenkinsPipeline_w_resolver_cx1.git'
    projectGroup = 'myg' 
    scanTimeout = 40
    scaName = 'ScaResolver'
    workspace = "${WORKSPACE}"  
    scaResolverPath = "\\playground\\peopleCert\\ScaResolver"
    }


stages{
     stage('Greeting') {
        steps {
            echo "Job [${JOB_NAME}]  - in the Workspace of [${WORKSPACE}]!"
            script {
                if(isUnix()) {
                    sh "ls -lahR ${WORKSPACE} "
                    sh "cd ${WORKSPACE} "
                } else {                       
                    bat "cd ${WORKSPACE}"
                }
            }
        }
     }
    stage('Clone') {
        steps {
           dir ('sub')  {
                git(
                    url: repoUrl,
                    branch: branchName,
                    credentialsId: ''
                )
            }
        }
    }
    
    stage('CxONE Scan') {
        steps {
          staticScan(branchName: branchName, credentialsId: 'cx1_prod_clientCred', projectName: projectName)
        }
    }
}
}

void scaResolverInstallation(String scaDir) {
    def fileName = "ScaResolver-win64.zip"
    def filePath = "${scaDir}\\${fileName}"
    def scaResolverDir = "${scaDir}"
    def scaResolverPath = "${scaResolverDir}\\${scaName}\\scaResolverInstallation"

    script {
        // Delete the existing file
        bat "del /F /Q \"${filePath}\""

        // Download the new file
        bat "curl -o ${filePath} https://sca-downloads.s3.amazonaws.com/cli/latest/ScaResolver-win64.zip"

        
         // Unzip the downloaded file using 7-Zip for windows
       bat "\"C:\\Program Files\\7-Zip\\7z.exe\" x -y ${filePath} -o${scaResolverDir}"
    }
}
void staticScan(Map args) 
{
    scaResolverInstallation(workspace)
     

    dir('sub') {
        checkmarxASTScanner additionalOptions: "--debug --async --project-tags ${projectTags} --scan-types sast,sca --scan-timeout ${scanTimeout} --sca-resolver ${scaResolverPath} --sca-resolver-params \"--log-level Debug --nuget-parameters IgnoreDependencies\"",
        checkmarxInstallation: 'checkmarx_ast',
        baseAuthUrl: 'https://eu.iam.checkmarx.net',
        serverUrl: 'https://eu.ast.checkmarx.net',
        branchName: args.branchName,
        credentialsId: args.credentialsId,
        projectName: args.projectName,
        tenantName: 'cx_emea_se_ido_neamani',
        useAuthenticationUrl: true,
        useOwnAdditionalOptions: true,
        useOwnServerCredentials: true
}
}

