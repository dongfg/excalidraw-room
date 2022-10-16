pipeline {
  agent any
  stages {
    stage("检出代码") {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: env.GIT_BUILD_REF]],
          userRemoteConfigs: [[
            url: env.GIT_REPO_URL,
            credentialsId: env.CREDENTIALS_ID
        ]]])
      }
    }
    stage('构建镜像') {
      when {
        buildingTag()
      }
      steps {
        script {
          try {
            DOCKER_IMAGE_VERSION = "${env.TAG_NAME}"
            withCredentials([usernamePassword(credentialsId: '21d3d00f-32cd-4d8c-b6ef-2838adf3fb12', usernameVariable: 'REGISTRY_USER', passwordVariable: 'REGISTRY_PASS')]) {
              sh "echo ${REGISTRY_PASS} | docker login -u ${REGISTRY_USER} --password-stdin registry.cn-hangzhou.aliyuncs.com"
              sh "docker build -t registry.cn-hangzhou.aliyuncs.com/dongfg/excalidraw-room:${DOCKER_IMAGE_VERSION} ."
              sh "docker push registry.cn-hangzhou.aliyuncs.com/dongfg/excalidraw-room:${DOCKER_IMAGE_VERSION}"
              sh "docker tag registry.cn-hangzhou.aliyuncs.com/dongfg/excalidraw-room:${DOCKER_IMAGE_VERSION} registry.cn-hangzhou.aliyuncs.com/dongfg/excalidraw-room:latest"
              sh "docker push registry.cn-hangzhou.aliyuncs.com/dongfg/excalidraw-room:latest"
            }
          } catch(err) {
            echo err.getMessage()
          }
        }
      }
    }
  }
}