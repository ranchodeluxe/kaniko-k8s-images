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
                                                                                                        
                /$$                                         
               | $$                                         
             /$$$$$$$$                                      
            |__  $$__/                                      
               | $$                                         
               |__/                                         
                                                         /$$
                                                        | $$
  /$$$$$$   /$$$$$$   /$$$$$$   /$$$$$$   /$$$$$$$  /$$$$$$$
 |____  $$ /$$__  $$ /$$__  $$ /$$__  $$ /$$_____/ /$$__  $$
  /$$$$$$$| $$  \__/| $$  \ $$| $$  \ $$| $$      | $$  | $$
 /$$__  $$| $$      | $$  | $$| $$  | $$| $$      | $$  | $$
|  $$$$$$$| $$      |  $$$$$$$|  $$$$$$/|  $$$$$$$|  $$$$$$$
 \_______/|__/       \____  $$ \______/  \_______/ \_______/
                     /$$  \ $$                              
                    |  $$$$$$/                              
                     \______/                                                                                                        
```

### Goal

Docker CI image-building pipeline in K8s via `argocd` and `kaniko`. 
Now we have our Docker images && caches as close to the k8s cluster as possible

### Workflow

* folks create PRs to add new images to `./images`
* Github Actions CI runs a `helm` templating pipeline on `main` which dumps k8s manifests to `./deploy/manifest.yaml`. 
It also plumbs through the Dockerfile sha256 to each resource to help identify changes
* ArgoCD is monitoring that branch and that folder for changes (either via polling, manually or eventually through webhooks)
* ArgoCD syncs the application resources, one of which is the `kind: Job` resource
* `kind: Job` is k8s resource that has a duty to spin up at most one `kind: Pod`. This is essentially the containerized jobs that leverages `kaniko` and builds images. 
`kaniko` also handles pushing the new image to Dockerhub (eventually to a private Docker repository on K8s). 
* Using `kind: Job` means that even if the same job/pod is around with the same sha256 naming pattern it will be rerun. This is not what we want
* We use a `lifecycle` > `postStart` hook to get the previous run information and decide whether to `exit` gracefully