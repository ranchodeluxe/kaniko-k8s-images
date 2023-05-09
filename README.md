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

### Goal:

Docker CI image-building pipeline in k8s via `argocd` and `kaniko`. Just because


### Workflow

* folks create PRs to add new images and Docker context to `./images`

* Github Actions CI runs a `helm` templating pipeline on `main` which dumps k8s manifests to `./deploy/manifest.yaml`. 
It also plumbs through the Dockerfile sha256 to each resource to help identify changes

* ArgoCD is monitoring that branch and that folder for changes (either via polling, manually or eventually through webhooks)

* ArgoCD syncs the application resources, one of which is the `kind: Pod` resource. 
This is essentially the containerized jobs that leverages `kaniko` and builds images. The 
`kaniko` job also handles pushing the new image to Dockerhub (eventually to a private Docker repository on k8s)
