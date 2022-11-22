pipeline {
    agent {
        kubernetes {
            defaultContainer 'kaniko'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: docker-credentials
          items:
            - key: .dockerconfigjson
              path: config.json
"""
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS=credentials("jenkins_dockerhub")
	DOCKER_IMAGE_NAME="juanllorenzogomis/kaniko-jenkins"
	IMAGE_PUSH_DESTINATION="juanllorenzogomis/kaniko-jenkins"
    }

    stages {
        stage('Build with Kaniko') {
            steps {
                checkout scm
                container(name: 'kaniko', shell: '/busybox/sh') {
                    withEnv(['PATH+EXTRA=/busybox']) {
                        sh '''#!/busybox/sh
                            /kaniko/executor --context `pwd` --destination $IMAGE_PUSH_DESTINATION
                        '''
                    }
                }
            }
        }
    }
}
