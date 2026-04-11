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
```


# Run playbook

First, provision the k3s servers:

```bash
ansible-playbook playbook-k8s-install.yaml \
    --inventory hosts.yaml \
    --vault-id prod@prompt \
    --ask-become-pass \
    --limit k3s_servers
```

Next, copy out the server cert, client cert and client key from `/etc/rancher/k3s/k3s.yaml` into `~/.kube/config`.

Then, install the system apps into the k8s cluster:

```bash
ansible-playbook playbook-k8s-install.yaml \
    --inventory hosts.yaml \
    --vault-id prod@prompt \
    --ask-become-pass
```

Finally, install the user apps into the k8s cluster:

```bash
ansible-playbook playbook-apps-install.yaml \
    --inventory hosts.yaml \
    --vault-id prod@prompt \
    --ask-become-pass
```
