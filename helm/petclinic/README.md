# PetClinic Helm chart

This chart deploys the Docker image built by the Jenkins pipeline to Kubernetes.

The default image tag is 25, matching the image currently deployed in the local cluster. Jenkins overrides this with the current build number during automatic deployment. To deploy a different build manually, pass its build number:

helm upgrade --install petclinic ./helm/petclinic --set image.tag=26

Check the deployment:

kubectl get pods,services
kubectl port-forward service/petclinic-petclinic 8082:8080

Open http://localhost:8082. Jenkins already uses port 8080 on this computer.

For Jenkins to deploy automatically, configure a Jenkins **Secret file** credential with ID `petclinic-kubeconfig`. It must contain a kubeconfig for the intended cluster, and its API server address must be reachable from the Jenkins container. The Jenkins pipeline installs pinned `kubectl` and Helm clients, deploys the Docker image tagged with the current build number, and waits for the rollout.

PetClinic defaults to its in-memory H2 database. Data is reset when the pod restarts. Configure a persistent database and the corresponding Spring profile before using this chart for persistent or production workloads.

To remove the demo deployment:

helm uninstall petclinic
