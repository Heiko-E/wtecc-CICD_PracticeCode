# Hands-on Lab: Build a Tekton Pipeline
## Installing Tekton Pipelines on Kubernetes
Install Tekton on Kubernetes

    kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
Monitor Pods

    kubectl get pods --namespace tekton-pipelines --watch
## Apply tasks to kubernetes
    kubectl apply -f ./labs/01_base_pipeline/tasks.yaml
## Apply pipelines to kubernetes
    kubectl apply -f ./labs/01_base_pipeline/pipeline.yaml
## Run Pipelines
Run the hello-world pipeline

    tkn pipeline start hello-pipeline \
    --showlog  \
    -p message="Hello Tekton!"

Run the cd-pipeline pipeline

    tkn pipeline start cd-pipeline \
    --showlog \
    -p repo-url="https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git" \
    -p branch="main"

# Hands-on Lab: Adding GitHub Triggers

## Apply tasks and pipelines to kubernetes
    kubectl apply -f ./labs/02_add_git_trigger/tasks.yaml
    kubectl apply -f ./labs/02_add_git_trigger/pipeline.yaml

Check that tasks and pipelines are created

    tkn task ls
    tkn pipeline ls
