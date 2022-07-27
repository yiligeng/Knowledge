### Pipeline语法参考

#### 1.选择pipeline利用流水线语法


主要使用工具为流水线语法:

(1) git: Git拉取仓库代码 [参考网址](https://blog.51cto.com/dongweizhen/3607280)

(2) ssh: ssh推送 [参考网址](https://cloud.tencent.com/developer/article/1688143)](https://cloud.tencent.com/developer/article/1551377)


#### 2.具体例子

jenkins拉取代码-jenkins本地构建-推送到服务器-重启服务

```
pipeline {
    agent any
  
    parameters {
          string(name: 'domain', defaultValue: 'risk.i.deltaentropy.com', description: '')
  }
  
    stages {
        stage('拉取代码') {
            steps {
                echo '拉取代码'
                git credentialsId: '36fc673a-62ab-48d4-8549-0eeafed6d26f', url: 'http://gitlab.deltaentropy.com/standard/private_car/app.git'
            }
        } 
        stage('编译构建') {
            steps {
                echo '编译构建'
                sh "mvn -Dmaven.test.skip=true clean package -DprofileActive=test"
  
            }
        } 
        stage('项目部署') {
            steps {
                echo '项目部署'
                //备份 
                sshPublisher(publishers: [sshPublisherDesc(configName: 'private_car_test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cp /usr/local/apps/api/delta-api.jar  /usr/local/apps/api/delta-api_bak.jar', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                sshPublisher(publishers: [sshPublisherDesc(configName: 'private_car_test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker restart 6d9c0d79ee9c', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/usr/local/apps/api', remoteDirectorySDF: false, removePrefix: 'delta-api/target', sourceFiles: 'delta-api/target/delta-api.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
  
            }
   
        }
   
   

    }
    post{
        always {
            emailext body: '${DEFAULT_CONTENT}',
            subject: '${DEFAULT_SUBJECT}',
            to: 'gengyl@deltaentropy.com'
        }
         success{
  
              build job: '私家车模型_接口测试', parameters: [string(name:'domain', value: "${params.domain}")]
          
        }
    }
  
  

}
```


登录服务器拉取代码-重启服务

```
pipeline {
    agent any
    parameters {
          string(name: 'domain', defaultValue: 'risk.i.deltaentropy.com', description: '')
  }
    stages {
        stage('拉取代码') {
            steps {
                echo '拉取代码'
                sshPublisher(publishers: [sshPublisherDesc(configName: 'saas_backend_test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd /home/sevensun/projects/deltamotion_cloud && var=`git pull upstream dev` && echo $var', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/sevensun/projects/deltamotion_cloud', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
            }
        } 
        stage('项目重启') {
            steps {
                echo '项目重启'
                sshPublisher(publishers: [sshPublisherDesc(configName: 'saas_backend_test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker exec  003a6d700878  python manage.py migrate && docker exec   003a6d700878  kill -HUP 1', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/sevensun/projects/deltamotion_cloud/deltamotion', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }

        }

    }
    post{
        always {
            emailext body: '${DEFAULT_CONTENT}',
            subject: '${DEFAULT_SUBJECT}',
            to: 'gengyl@deltaentropy.com'
        }
        success{
            
              build job: '货车产品_接口测试', parameters: [string(name:'domain', value: "${params.domain}")]
                      
        }
    
    }

}
```



前端项目:

jenkins拉取代码-jenkins nodejs部署(需要到全局工具配置选择node,具体搜索nodejs插件)-推送到服务器-重启nginx服务

```
pipeline {
    agent any
    stages {
        stage('拉取代码') {
            steps {
                echo '拉取代码'
                git branch: 'develop', credentialsId: '36fc673a-62ab-48d4-8549-0eeafed6d26f', url: 'http://gitlab.deltaentropy.com/innovate/vibs/vibs-frontend.git'
            }
        } 
        stage('编译构建') {
            steps {
                echo '编译构建'
                nodejs(cacheLocationStrategy: workspace(), nodeJSInstallationName: 'nodejs16') {
                // some block
                sh 'npm install'
                sh 'npm run build:prod'
                }

            }
        } 
        stage('项目部署') {
            steps {
                echo '项目部署'
                sshPublisher(publishers: [sshPublisherDesc(configName: 'economic_project', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cp -r /usr/local/apps/vibs_html  /usr/local/apps/vibs_html_bak', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                sshPublisher(publishers: [sshPublisherDesc(configName: 'economic_project', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'rm -rf  /usr/local/apps/vibs_html ; mv /usr/local/apps/dist /usr/local/apps/vibs_html ; nginx -s reload', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/usr/local/apps', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'dist/**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }

        }

    }
    post{
        always {
            emailext body: '${DEFAULT_CONTENT}',
            subject: '${DEFAULT_SUBJECT}',
            to: 'qinquan@deltaentropy.com'
        }
    }

}
```


ansible playbook:

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
