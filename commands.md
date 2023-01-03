# Installing Tekton Pipelines on Kubernetes
Install Tekton on Kubernetes

    kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml

Install Triggers

    kubectl apply --filename https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml

Monitor Pods

    kubectl get pods --namespace tekton-pipelines --watch

# Clone git Repository

Add credentials

    gh auth login

Clone Repository

    gh repo clone https://github.com/Heiko-E/wtecc-CICD_PracticeCode

# Hands-on Lab: Build a Tekton Pipeline

## Apply tasks to kubernetes
    kubectl apply -f ./labs/01_base_pipeline/tasks.yaml
## Apply pipelines to kubernetes
    kubectl apply -f ./labs/01_base_pipeline/pipeline.yaml
## Run Pipelines
Run the hello-world pipeline

    tkn pipeline start hello-pipeline --showlog -p message="Hello Tekton!"

Run the cd-pipeline pipeline

    tkn pipeline start cd-pipeline --showlog -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" -p branch="main"

# Hands-on Lab: Adding GitHub Triggers

## Apply tasks and pipelines to kubernetes
    kubectl apply -f ./labs/02_add_git_trigger/tasks.yaml
    kubectl apply -f ./labs/02_add_git_trigger/pipeline.yaml

Check that tasks and pipelines are created

    tkn task ls
    tkn pipeline ls
## Add event listeners
    kubectl apply -f ./labs/02_add_git_trigger/eventlistener.yaml

Check that event listeners are created

    tkn eventlistener ls
## Add triggers
    kubectl apply -f ./labs/02_add_git_trigger/triggerbinding.yaml
    kubectl apply -f ./labs/02_add_git_trigger/triggertemplate.yaml

## Trigger the Event localy with curl
Open a terminal and for port forwarding

    kubectl port-forward service/el-cd-listener  8090:8080

Trigger the event

    curl -X POST http://localhost:8090 -H 'Content-Type: application/json' -d '{"ref":"main","repository":{"url":"https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git"}}'

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last

# Hands-on Lab: Use Tekton Continuous Delivery (CD) Catalog

Check [Tekton Hub](http://hub.tekton.dev/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMCD0215ENSkillsNetwork35576036-2022-01-01) for existing Tasks

## Apply tasks and pipelines to kubernetes

Create Tasks:

    kubectl apply -f ./labs/03_use_tekton_catalog/tasks.yaml
    tkn hub install task git-clone

Note: If the above git-clone command returns an error for the git-clone task due to Tekton Version mismatch, please run the below command to fix this.

    kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-clone/0.9/git-clone.yaml

Check if the tasks are installed succesfully

    tkn task ls

Create Workspace:

    kubectl apply -f ./labs/03_use_tekton_catalog/pvc.yaml

Create Pipeline:

    kubectl apply -f ./labs/03_use_tekton_catalog/pipeline.yaml

## Test the pipeline

Run the pipeline

    tkn pipeline start cd-pipeline -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" -p branch="main" -w name=pipeline-workspace,claimName=pipelinerun-pvc --showlog

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last

# Integrating Unit Test Automation

## Apply tasks and pipelines to kubernetes
Create Tasks:

    tkn hub install task git-clone
    tkn hub install task flake8
    kubectl apply -f ./labs/04_unit_test_automation/tasks.yaml

Note: If the above git-clone or flake8 command returns an error for the git-clone task due to Tekton Version mismatch, please run the below command to fix this.

    kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-clone/0.9/git-clone.yaml
    kubectl apply -f https://api.hub.tekton.dev/v1/resource/tekton/task/flake8/0.1/raw

Check if the tasks are installed succesfully

    tkn task ls

Create Workspace:

    kubectl apply -f ./labs/04_unit_test_automation/pvc.yaml

Create Pipeline:

    kubectl apply -f ./labs/04_unit_test_automation/pipeline.yaml

## Test the pipeline

Run the pipeline

    tkn pipeline start cd-pipeline -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" -p branch="main" -w name=pipeline-workspace,claimName=pipelinerun-pvc --showlog

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last

# Hands-on Lab: Building an Image

## Apply tasks and pipelines to kubernetes
Create Tasks:

    tkn hub install task git-clone
    tkn hub install task flake8
    kubectl apply -f ./labs/05_build_an_image/tasks.yaml

Note: If the above git-clone or flake8 command returns an error for the git-clone task due to Tekton Version mismatch, please run the below command to fix this.

    kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-clone/0.9/git-clone.yaml
    kubectl apply -f https://api.hub.tekton.dev/v1/resource/tekton/task/flake8/0.1/raw

Check if the tasks are installed succesfully

    tkn task ls

Check if buildah is in the cluster tasks

    tkn clustertask ls

Create buildah cluster task

    kubectl apply -f https://api.hub.tekton.dev/v1/resource/tekton/task/buildah/0.5/raw
    tkn clustertask create --from=buildah

Create Workspace:

    kubectl apply -f ./labs/05_build_an_image/pvc.yaml

Create Pipeline:

    kubectl apply -f ./labs/05_build_an_image/pipeline.yaml

## Test the pipeline

Run the pipeline

    tkn pipeline start cd-pipeline -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" -p branch=main -p build-image=image-registry.openshift-image-registry.svc:5000/$SN_ICR_NAMESPACE/tekton-lab:latest -w name=pipeline-workspace,claimName=pipelinerun-pvc --showlog

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last

# Hands-on Lab: Deploy to Kubernetes/OpenShift

## Apply tasks and pipelines to kubernetes
Create Tasks:

    tkn hub install task git-clone
    tkn hub install task flake8
    kubectl apply -f ./labs/06_deploy_to_kubernetes/tasks.yaml

Note: If the above git-clone or flake8 command returns an error for the git-clone task due to Tekton Version mismatch, please run the below command to fix this.

    kubectl apply -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-clone/0.9/git-clone.yaml
    kubectl apply -f https://api.hub.tekton.dev/v1/resource/tekton/task/flake8/0.1/raw

Check if the tasks are installed succesfully

    tkn task ls

Check if openshift-client is in the cluster tasks

    tkn clustertask ls

Create openshift-client cluster task

    kubectl apply -f https://api.hub.tekton.dev/v1/resource/tekton/task/openshift-client/0.2/raw
    tkn clustertask create --from=openshift-client

Create Workspace:

    kubectl apply -f ./labs/06_deploy_to_kubernetes/pvc.yaml

Create Pipeline:

    kubectl apply -f ./labs/06_deploy_to_kubernetes/pipeline.yaml

## Test the pipeline

Run the pipeline

    tkn pipeline start cd-pipeline -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" -p branch=main -p app-name=hitcounter -p build-image=image-registry.openshift-image-registry.svc:5000/$SN_ICR_NAMESPACE/tekton-lab:latest -w name=pipeline-workspace,claimName=pipelinerun-pvc --showlog

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last

Check if the deployment is running

    kubectl get all -l app=hitcounter

# SonarQube

## Set up SonarQube in a Docker container on port 9000.

    docker network create mynet
    docker run --name postgres  -e POSTGRES_USER=root -e POSTGRES_PASSWORD=Test12345  -p 5432:5432 --network mynet -d postgres
    docker run -d --name sonarqube -p 9000:9000 -e sonar.jdbc.url=jdbc:postgresql://postgres/postgres -e sonar.jdbc.username=root -e sonar.jdbc.password=Test12345 --network mynet sonarqube

Check if installation was successfull.

    docker ps

## Log in
    
    http://localhost:9000/
    username: admin
    password: admin

Create a project manually for a local repository. Then generate an access token and get the command to run the scan. This should loo like:

    sonar-scanner -Dsonar.projectKey=temp -Dsonar.sources=. -Dsonar.host.url=https://heikoegerter-9000.theiadocker-3-labs-prod-theiak8s-4-tor01.proxy.cognitiveclass.ai -Dsonar.login=<TOKEN>

## Get SonarQube scanner

    docker pull sonarsource/sonar-scanner-cli
    alias sonar-scanner='docker run --rm -v "$(pwd):/usr/src" sonarsource/sonar-scanner-cli'

Now use the copied run command

# Dynamic application security testing (DAST)

Get a OWASP ZAP container:

    docker pull owasp/zap2docker-stable

Run the scan

    docker run -t owasp/zap2docker-stable zap-baseline.py -t {TARGET_URL}

# Static application security testing (SAST)

Instal bandit

    pip install bandit

Run bandit in bash

    bandit -r web_app_example.py -o ./results.txt