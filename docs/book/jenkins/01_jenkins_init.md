```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'git pull'
                // Get some code from a GitHub repository
                git credentialsId: '880bb432-d92e-4450-beb4-8bcd31aab94a', url: 'https://github.com/7jiongsh/extention_v2.git'

                sh "cp -rf /playbook/tpl/prod/application.yml extension-api/src/main/resources/application.yml"
                sh "cat extension-api/src/main/resources/application.yml"
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit 'extension-api/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'extension-api/target/*.jar'
                    echo 'ansible playbook'
                    sh "ansible-playbook /playbook/pb-prod.yml"
                    sh "python3 /playbook/tools/yanbao_automated_test/main.py prod"
                }
            }
        }
    }
}
```

### jenkins docker创建服务器

####地址
https://www.jenkins.io/doc/book/installing/docker/
--rm 可以不用选

####流程图
<table><tr><td>
<img src="../../images/5_jenkins_flow.png" />
</td></tr></table>