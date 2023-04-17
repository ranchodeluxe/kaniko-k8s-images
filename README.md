## kaniko-k8s-images

```bash
 /$$                           /$$ /$$                
| $$                          |__/| $$                
| $$   /$$  /$$$$$$  /$$$$$$$  /$$| $$   /$$  /$$$$$$ 
| $$  /$$/ |____  $$| $$__  $$| $$| $$  /$$/ /$$__  $$
| $$$$$$/   /$$$$$$$| $$  \ $$| $$| $$$$$$/ | $$  \ $$
| $$_  $$  /$$__  $$| $$  | $$| $$| $$_  $$ | $$  | $$
| $$ \  $$|  $$$$$$$| $$  | $$| $$| $$ \  $$|  $$$$$$/
|__/  \__/ \_______/|__/  |__/|__/|__/  \__/ \______/ 
                                                       
```

Docker CI image-building pipeline in K8s via `argocd` and `kaniko`. 
Now we have our Docker images && caches as close to the k8s cluster as possible

### general workflow

* CI (Github Actions) runs a `helm` templating pipeline on `main` 
* `helm` writes a `./deploy/manifest.yaml` on the `deploy` branch using sha256 for each Dockerfile to know if changes have happened
* ArgoCD is monitoring that branch and that folder for changes (either manually, polling or eventually through webhooks)
* ArgoCD syncs the application and kicks of the `kind: Pod` definitions
* `kind: Pod` are essentially jobs that leverage `kaniko` and build iamges and push to Dockerhub (eventually to a private Docker repository on K8s)