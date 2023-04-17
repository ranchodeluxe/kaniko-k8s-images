## kaniko-k8s-images

```bash
 /$$                           /$$ /$$                                                                                               /$$
| $$                          |__/| $$                          /$$                                                                 | $$
| $$   /$$  /$$$$$$  /$$$$$$$  /$$| $$   /$$  /$$$$$$          | $$           /$$$$$$   /$$$$$$   /$$$$$$   /$$$$$$   /$$$$$$$  /$$$$$$$
| $$  /$$/ |____  $$| $$__  $$| $$| $$  /$$/ /$$__  $$       /$$$$$$$$       |____  $$ /$$__  $$ /$$__  $$ /$$__  $$ /$$_____/ /$$__  $$
| $$$$$$/   /$$$$$$$| $$  \ $$| $$| $$$$$$/ | $$  \ $$      |__  $$__/        /$$$$$$$| $$  \__/| $$  \ $$| $$  \ $$| $$      | $$  | $$
| $$_  $$  /$$__  $$| $$  | $$| $$| $$_  $$ | $$  | $$         | $$          /$$__  $$| $$      | $$  | $$| $$  | $$| $$      | $$  | $$
| $$ \  $$|  $$$$$$$| $$  | $$| $$| $$ \  $$|  $$$$$$/         |__/         |  $$$$$$$| $$      |  $$$$$$$|  $$$$$$/|  $$$$$$$|  $$$$$$$
|__/  \__/ \_______/|__/  |__/|__/|__/  \__/ \______/                        \_______/|__/       \____  $$ \______/  \_______/ \_______/
                                                                                                 /$$  \ $$                              
                                                                                                |  $$$$$$/                              
                                                                                                 \______/                                                                                 
```

Docker CI image-building pipeline in K8s via `argocd` and `kaniko`. 
Now we have our Docker images && caches as close to the k8s cluster as possible

### general workflow

* CI (Github Actions) runs a `helm` templating pipeline on `main` 
* `helm` writes a `./deploy/manifest.yaml` on the `main` plumbing through the Dockerfile sha256 to each resource to identify changes
* ArgoCD is monitoring that branch and that folder for changes (either manually, polling or eventually through webhooks)
* ArgoCD syncs the application and kicks of the `kind: Pod` definitions
* `kind: Pod` are essentially containerized jobs that leverage `kaniko` and build images. They also push to Dockerhub (eventually to a private Docker repository on K8s)