# Install prerequisites

```bash
# install pipx
sudo apt install pipx

# install ansible
pipx install ansible-core

# install ansible requirements
cd ansible
ansible-galaxy collection install -r requirements.yaml

# install python requirements
pipx runpip ansible-core install -r requirements.txt

# install helm `diff` plugin
helm plugin install https://github.com/databus23/helm-diff --verify=false

# or, update `diff` later
helm plugin update diff
```


# Run playbook

The playbook is run in multiple steps, detailed in the following sections.

## Provision the k3s servers

This step installs k3s.

```bash
ansible-playbook playbook-k8s-install.yaml \
    --vault-id prod@prompt \
    --ask-become-pass \
    --limit k3s_servers
```

## Extract kubeconfig

Next, copy out the server cert, client cert and client key from `/etc/rancher/k3s/k3s.yaml` into `~/.kube/config`.

## Install system apps

This step installs the system apps into the k8s cluster, such as Longhorn for storage.

```bash
ansible-playbook playbook-k8s-install.yaml \
    --vault-id prod@prompt \
    --ask-become-pass
```

## Create storage volumes

After installing Longhorn above, the volumes for the user apps need to be manually created in the Longhorn UI:

| Volume name            | Size      | Mode | Where it's used
| ---------------------- | --------- | ---- | ---------------
| mediacms-media         | >= 200 Gi | RWX  | [mediacms-pvcs.yaml](ansible/roles/helm_mediacms/files/chart/templates/mediacms-pvcs.yaml)
| mediacms-postgres-data | >= 20 Gi  | RWO  | [postgresql.yaml](ansible/roles/helm_mediacms/files/chart/templates/postgresql.yaml)

## Install user apps

Finally, install the user apps into the k8s cluster:

```bash
ansible-playbook playbook-apps-install.yaml \
    --vault-id prod@prompt \
    --ask-become-pass
```
