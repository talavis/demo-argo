# Argocd

Quick Argocd demo on Rancher Desktop

## Setup

1. Fork https://github.com/talavis/demo-argo and https://github.com/talavis/lunch-menu
2. Check out the forks locally (e.g. `git@github.com:talavis/lunch-menu.git`)
3. Select the `demo` branch in `lunch-menu`: `git switch demo`
4. Create a branch based on the demo branch: `git checkout -b demo-local`
5. Make a change in the file `lunch-menu/backend/build_trigger.txt`.
6. Push the change to Github to trigger a build of container images that will be published on your account (`git push --set-upstream origin demo`)
7. Create a `demo` branch in the `demo-argo` repo: `git checkout -b demo`
8. Update the image in the `demo-app/deployment/deploy_*` files to use your images instead. Commit.
9. Push the updated branch to github (`git push --set-upstream origin demo`)

## Prepare Argo

1. Create namespace `kubectl create namespace argocd`
2. Install Argo `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`
3. Update settings for argocd `kubectl apply -f argocd-param.yaml`
4. Redeploy Argo: `kubectl -n argocd rollout restart deployment argocd-server`
5. Install ingress: `kubectl apply -f ingress.yaml`
6. Get password for the admin account: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
7. Log in at `https://argocd.localdev.me`. Since the certificate is self-signed, there will be a warning about unsecure traffic.


## Create an application

1. Create a deployment key: `ssh-keygen -t ed25519 -f deploy_key`
2. Add the public key to Github: https://github.com/<your username>/demo-argo/settings/keys
3. Add a repository in Argo (https://argocd.localdev.me/settings/repos), provide the ssh link to the repo and the generated private key
4. Create an application in Argo, use the added repo and provide `demo-app/deployment` as path. Branch should be `demo`. Use `auto-create namespace` if the namespace does not exist. Enable automatic syncing.
5. Wait while the application is starting
6. The app is available at https://menu.localdev.me/api/restaurant (backend) and https://menu.localdev.me/ (frontend). Still self-signed certs, so there will be a warning.


## Create a new image

1. Create another branch in the `lunch-menu` repo: `git checkout -b demo-local-alt`
2. Edit the primary color for the frontend in the file `frontend/src/css/quasar.variables.scss`, e.g. `#355E3B` for a green color
3. Commit and push the new branch to Github
4. New images with the tag `:demo-local-alt` will be built automatically


## Deploy the new image

1. Update the image reference in the `demo-app/deployment/deploy_*` in the `demo-argo` repo to use the new image (`demo-local-alt`)
2. Commit and push to Github
3. Wait for (or manually trigger) a sync in Argo to start using the updated deployments
