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

    curl -X POST http://localhost:8090 \
    -H 'Content-Type: application/json' \
    -d '{"ref":"main","repository":{"url":"https://github.com/Heiko-E/wtecc-CICD_PracticeCode.git"}}'

Check pipeline status

    tkn pipelinerun ls

Get logs of last run

    tkn pipelinerun logs --last