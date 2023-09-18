# Argocd

Quick Argocd demo on Rancher Desktop

## Setup

1. Fork https://github.com/talavis/demo-argo
2. Check out the forks locally
3. Select the `demo` branch in `lunch-menu`: `git switch demo`
4. Enter the `demo-argo` folder
5. Create a demo branch: `git checkout -b demo`

## Prepare Argo

1. Create namespace `kubectl create namespace argocd`
2. Install Argo `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`
3. Update settings for argocd `kubectl apply -f argocd-param.yaml`
4. Redeploy Argo: `kubectl -n argocd rollout restart deployment argocd-server`
5. Install ingress: `kubectl apply -f ingress.yaml`
6. Get password for the admin account: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
7. Log in at `https://argocd.localdev.me`. Since the certificate is self-signed, there will be a warning about unsecure traffic.


## Create an application

1. Create a deployment key: `ssh-keygen -t ed25519`
2. Add the public key to Github: https://github.com/<your username>/demo-argo/settings/keys
3. Add a repository in Argo: https://argocd.localdev.me/settings/repos, provide the generated private key
4. Create an application in Argo, use the added repo and provide `demo-app` as path. Branch should be `demo`. Use `auto-create namespace` if it does not exist. Enable automatic syncing.
5. Wait while the application is starting
6. The app is available at https://menu.localdev.me/api/restaurant (backend) and https://menu.localdev.me/ (frontend). Still self-signed certs, so there will be a warning.


## Change image for the application

1. Edit `demo-app/deploy-frontend.yaml` to use the tag `demo-alt` for the image
2. Push to Github
3. Show changes in Argo
3. Reload the page in the web browser and show off the new color