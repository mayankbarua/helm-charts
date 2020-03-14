/*
    This is an example pipeline that implement full CI/CD for a simple static web site packed in a Docker image.
    The pipeline is made up of 6 main steps
    1. Git clone and setup
    2. Build and local tests
    3. Publish Docker and Helm
    4. Deploy to dev and test
    5. Deploy to staging and test
    6. Optionally deploy to production and test
 */

/*
    Create the kubernetes namespace
 */
def createNamespace (name) {
    echo "Creating namespace ${name} if needed"

    sh "kubectl create namespace ${name} --dry-run -o yaml | kubectl apply -f -"
}

/*
    Helm install Backend application
*/
def helmInstallBackend (backendReleaseName) {
    echo "Installing backend application"

    script {
       // sh "helm repo add helm ${HELM_REPO}; helm repo update"
       sh "helm upgrade --install ${backendReleaseName} --namespace=api --set configmap.RDS_CONNECTION_STRING=${env.rdsString},configmap.S3_BUCKET_NAME=${env.S3BucketName},secret.awscred.aws_key=${env.awsKey},secret.awscred.secret_key=${env.awsSecret},secret.regcred.dockerconfigjson=${env.dockerString},redis.sentinel.enabled=${env.redisSentinel},redis.cluster.enabled=${env.redisCluster},redis.password=${env.redisPassword},secret.redis.password=${env.redisPassword},configmap.REDIS_SENTINEL_HOSTNAME=${backendReleaseName}-redis.api.svc.cluster.local --debug ./back-end/"
    }
}

/*
    Helm install Frontend application
*/
def helmInstallFrontend (frontendReleaseName, backendReleaseName) {
    echo "Installing frontend application"

    script {
       sh "sleep 50"
       echo "Finding backendip"
       backendIp = sh(returnStdout: true, script: "kubectl describe services backend-back-end --namespace=api | grep elb.amazonaws.com | grep LoadBalancer | awk '{print \$3}' | tr -d '\n'")
       echo "${backendIp}"
       sh "helm upgrade --install ${frontendReleaseName} --namespace=ui --set intiContainer.backendDependencyEndpoint=${backendReleaseName}-back-end.api.svc.cluster.local,configmap.backendIp='http://${backendIp}:3000',secret.regcred.dockerconfigjson=${env.dockerString} --debug ./front-end/"
    }
}


node {
     def backendIp
     def changedFiles
     def backendReleaseName = "backend"
     def frontendReleaseName = "frontend"
    stage('Clone repository') {
        /* Cloning the Repository to our Workspace */
                checkout scm
                sh 'git diff --name-only --diff-filter=ADMR @~..@ > output.txt'
                changedFiles = readFile 'output.txt'
                echo "Changed files - ${changedFiles}"
            }

    stage('Startup activities'){
     sh "kubectl cluster-info"

     // Init helm client
     sh "helm init"
    }

     stage('Deploy Backend'){
        if (changedFiles?.trim().contains("back-end"))
         {
            echo "Deploying backend"
            createNamespace('api')
            helmInstallBackend(backendReleaseName)
        }else{
            echo "Nothing to deploy in backend"
        }
      }

     stage('Deploy Frontend'){
        if (changedFiles?.trim().contains("front-end"))
        {
          echo "Deploying frontend"
          createNamespace('ui')
          helmInstallFrontend(frontendReleaseName, backendReleaseName)
        }
        else{
            echo "Nothing to deploy in frontend"
        }
     }
 }