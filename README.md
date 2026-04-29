# Burp Suite DAST with Jenkins (Demo)

> ⚠️ **Note:** This is a demo configuration file intended for reference purposes. For more customization, go to [[https://github.com/realawaisakbar/JenkinsxBurpSuiteDAST/tree/main#%EF%B8%8F-further-customization]]

## 🔐 Prerequisites

Before using this `Jenkinsfile`, make sure the following configurations are properly in place:

- Store your **`burp-api-key`** securely in **Jenkins Credentials**.
- Configure the following required environment variables or parameters:
  - `BURP_ENTERPRISE_SERVER_URL`
  - `BURP_CORRELATION_ID`
  - `BURP_FOLDER_ID`

## 📚 Documentation & Guides

For a detailed step-by-step guide, refer to the following resources:

### 🔗 Main Article
- [Integrating Burp Suite DAST with Jenkins for Automated Security in CI/CD Pipelines](https://medium.com/@awaisakbarofficial/integrating-burp-suite-dast-with-jenkins-for-automated-security-in-ci-cd-pipelines-092569578090)

### 🔐 Authenticated Scans Guide
- [How to Run Authenticated Scans in Burp Suite DAST using Recorded Login Sequences](https://medium.com/@awaisakbarofficial/burp-suite-dast-tutorial-how-to-run-authenticated-scans-using-recorded-login-sequences-5c68f8474860)

## ⚙️ Further Customization

According to the official documentation, it is recommended to use a `burp_config.yml` file. The setup would look like this:

#### 1. `Jenkinsfile`

```
pipeline {
  agent any

  parameters {
    string(
      name: 'BURP_START_URL',
      defaultValue: 'https://ginandjuice.shop/',
      description: 'Target URL for Burp DAST scan'
    )
  }

  stages {
    stage("Burp DAST Scan") {
      steps {
        withCredentials([string(credentialsId: 'burp-api-key', variable: 'BURP_ENTERPRISE_API_KEY')]) {
          sh '''
            docker run --rm --pull=always \
              -u $(id -u) \
              -v ${WORKSPACE}:${WORKSPACE}:rw \
              -w ${WORKSPACE} \
              -e BURP_ENTERPRISE_SERVER_URL=https://yoursite.portswigger.cloud \
              -e BURP_ENTERPRISE_API_KEY=$BURP_ENTERPRISE_API_KEY \
              -e BURP_START_URL=${BURP_START_URL} \
              -e BURP_CONFIG_FILE_PATH=${WORKSPACE}/burp_config.yml \
              public.ecr.aws/portswigger/enterprise-scan-container:latest
          '''
        }
      }
    }
  }

  post {
    always {
      junit testResults: 'burp_junit_report.xml',
            allowEmptyResults: true

      cleanWs()
    }
  }
}
```

#### 2. `burp_config.yml`

```
site:
  correlationId: Jenkins-Scan
  folderId: 43
```

---

Feel free to customize this setup according to your project requirements. A detailed guide on customization can be found here:
- **`Jenkinsfile:`** https://portswigger.net/burp/documentation/dast/user-guide/ci-cd/ci-driven-scans/example-integrations/integrate-jenkins
- **`burp_config.yml:`** https://github.com/PortSwigger/ci-cd-platform-scanning-examples/blob/main/README.md
