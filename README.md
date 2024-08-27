#gcp

$env:GOOGLE_APPLICATION_CREDENTIALS='C:/gcp/sacred-alliance-433217-e3-54cc0f7cc68f.json'

# GKE

https://developer.hashicorp.com/terraform/tutorials/kubernetes/gke

terraform apply

kubernetes_cluster_host = "35.225.26.190"
kubernetes_cluster_name = "sacred-alliance-433217-e3-gke"
project_id = "sacred-alliance-433217-e3"
region = "us-central1"


gcloud components install kubectl

gcloud container clusters get-credentials $(terraform output -raw kubernetes_cluster_name) --region $(terraform output -raw region)

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

#tekton

kubectl apply --filename https:‌//storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
kubectl get pod -n tekton-pipelines --watch

#cli
choco install tektoncd-cli --confirm

#triggers
kubectl apply --filename https:‌//storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
kubectl apply --filename https:‌//storage.googleapis.com/tekton-releases/triggers/latest/interceptors.yaml
#dashboard
kubectl apply --filename https:‌//github.com/tektoncd/dashboard/releases/download/v0.38.0/release-full.yaml

kubectl get pods --namespace tekton-pipelines --watch

#To see tekton ui
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep service-controller-token | awk '{print $1}')
kubectl proxy
http://127.0.0.1:8001/api/v1/namespaces/tekton-pipelines/services/tekton-dashboard:http/proxy/#/about

#Tekton git serects

To clone a repository, Tekton needs a username and password of this repository. In this section, we will pass the Git username and password (using GitHub or GitLab) by creating a secret and mounting it to the service account.

Create a secret named git-secret.yaml and apply it as shown below:

apiVersion: v1
kind: Secret
metadata:
  name: basic-user-pass
  annotations:
    tekton.dev/git-0: <YOUR-GIT-REPO-URL> # Add your Git repository URL
type: kubernetes.io/basic-auth
stringData:
  username: <Enter unencrypted username>
  password: <Enter unencrypted password>

kubectl apply -f git-secret.yaml -n tekton-pipelines



Pass this secret name in the service account’s secrets section. Edit the service account through the CLI and add the secret name in the secret section as shown below:

kubectl edit sa <service account name> -n tekton-pipelines



Your service account looks as follows:

apiVersion: v1
kind: ServiceAccount
metadata:
  name: <Enter Service Account Name>
  namespace: tekton-pipelines
secrets:
  - name: basic-user-pass



This secret is now mounted to the service account. It can be retrieved from the service account when Tekton tries to communicate with the Git repository.
#vllm
https://medium.com/google-cloud/serving-open-source-llms-on-gke-using-vllm-framework-5e522b3679ee



