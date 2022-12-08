# 部署Jenkins
deploy目录中的配置文件，用于将Jenkins部署于Kubernetes集群之上，它依赖于：
- Kubernetes集群上部署有基于nfs-csi的存储服务，且创建了名称为nfs-csi的StorageClass资源，并支持PV的动态置备；
- Kubernetes集群上部署有Ingress Nginx Controller；

### 部署命令
```bash
kubectl apply -f deploy/
kubectl apply -f ingress/
```

### Pipeline代码示例

#### 测试于Pod中运行slave

```
// Author: "MageEdu <mage@magedu.com>"
// Site: www.magedu.com
pipeline {
    agent {
        kubernetes {
            inheritFrom 'kube-agent'
        }
    }
    stages {
        stage('Testing...') {
            steps {
                sh 'java -version'
            }
        }
    }
}
```

#### 测试maven构建环境

需要事先定义出名为maven-3.8.6的Pod Template

```
pipeline {
    agent {
        kubernetes {
            inheritFrom 'maven-3.8'
        }
    }
    stages {
        stage('Build...') {
            steps {
                container('maven') {
                    sh 'mvn -version'
                }
            }
        }
    }
}
```

#### 测试docker in docker环境

需要事行定义出名为maven-and-docker的Pod Template；另外，docker in docker依赖于hostPath类型的存储卷将节点上的/var/run/docker.sock带入到Pod的dind容器中；

```
pipeline {
    agent {
        kubernetes {
            inheritFrom 'maven-and-docker'
        }
    }
    stages {
        stage('maven version') {
            steps {
                container('maven') {
                    sh 'mvn -version'
                }
            }
        }
        stage('docker info') {
            steps {
                container('dind') {
                    sh 'docker info'
                }
            }
        }
    }
}
```

### 一个具有实际功用的Pipeline示例

```json
pipeline {
    agent {
        kubernetes {
            inheritFrom 'maven-and-docker'
        }
    }   
    triggers {
        GenericTrigger(
            genericVariables: [
                [key: 'ref', value: '$.ref']
            ],
            token: 'fClZ0e/kTcqL2ARh7YqxW/3ndOCZA2SqfKnRTLat',
            causeString: 'Triggered on $ref',
            printContributedVariables: true,
            printPostContent: true
        )
    }   
    environment {
        codeRepo="http://gitlab.magedu.com/root/spring-boot-helloWorld.git"
        registry='hub.magedu.com'
        registryUrl='https://hub.magedu.com'
        registryCredential='harbor-user-credential'
        projectName='spring-boot-helloworld'
        imageUrl="${registry}/ikubernetes/${projectName}"
        imageTag="${BUILD_ID}"
    }
    stages {
        stage('Source') {
            steps {
                git branch: 'main', credentialsId: 'gitlab-root-credential', url: "${codeRepo}"
            }
        }
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }
        stage('Test') {
            steps {
                container('maven') {
                    sh 'mvn test'
                }
            }
        }
        stage("SonarQube Analysis") {
            steps {
                container('maven') {                
                    withSonarQubeEnv('SonarQube-Server') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        } 
        stage('Build Image') {
            steps {
                container('dind') {
                    script {
                        dockerImage = docker.build("${imageUrl}:${imageTag}")  
                    }
                }
            }
        }       
        stage('Push Image') {
            steps {
                container('dind') {
                    script {
                        docker.withRegistry(registryUrl, registryCredential) {
                            dockerImage.push()
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }
        stage('Update-manifests') {
        	steps {
        	    container('jnlp') {
        	        sh 'sed -i "s#__IMAGE__#${imageUrl}:${imageTag}#gi" deploy/all-in-one.yaml'
        	    }  
        	}
        }
        stage('Deploy') {
            steps {
                container('kubectl') {
                    withKubeConfig([credentialsId: 'k8s-hello-admin-token', serverUrl: 'https://kubernetes.default.svc']) {
                        sh '''
                            kubectl create namespace hello
                            kubectl apply -f deploy/all-in-one.yaml -n hello
                        '''
                    }
                }
            }
        }              
    }
    post{
        always{
            qyWechatNotification failNotify: true, webhookUrl: 'https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=c79e3215-d39f-44a7-a760-fa0ab23ca46b'
        }
    }   
}
```

