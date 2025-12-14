pipeline {
  agent any

  environment {
    IMAGE = "shezanmalikcfc/employee-portal:latest"
    DOCKERHUB = credentials('dockerhub-creds')
    APP1_PRIVATE_IP = "10.10.2.73"
    SSH_KEY = "/var/lib/jenkins/.ssh/employee-portal.pem"
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/shezanmalikcfc/Employee-Portal-with-Automated-DevOps-Delivery-Pipeline.git', branch: 'main'
      }
    }

    stage('Build Image') {
      steps {
        sh "docker build -t ${IMAGE} ."
      }
    }

    stage('Login & Push') {
      steps {
        sh """
          echo "${DOCKERHUB_PSW}" | docker login -u "${DOCKERHUB_USR}" --password-stdin
          docker push ${IMAGE}
        """
      }
    }

    stage('Deploy to K3s') {
      steps {
        sh """
          ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ubuntu@${APP1_PRIVATE_IP} '
            sudo KUBECONFIG=/etc/rancher/k3s/k3s.yaml kubectl -n employee-portal set image deployment/employee-portal web=${IMAGE} &&
            sudo KUBECONFIG=/etc/rancher/k3s/k3s.yaml kubectl rollout status deployment/employee-portal -n employee-portal
          '
        """
      }
    }
  }
}

