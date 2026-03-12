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


# Set up microceph cluster

On the main microceph server:

```bash
# install the snap and bootstrap the cluster
# see: https://canonical-microceph.readthedocs-hosted.com/latest/tutorial/get-started/
sudo snap install microceph
sudo snap refresh --hold microceph
sudo microceph cluster bootstrap

# enable full disk encryption, add the disks
# see: https://canonical-microceph.readthedocs-hosted.com/latest/how-to/enable-fde/
sudo snap connect microceph:dm-crypt
sudo snap restart microceph.daemon
sudo microceph disk add /dev/{sda,sdb} --wipe --encrypt
```


# Run playbook

```bash
ansible-playbook playbook.yaml \
    --inventory hosts.yaml \
    --vault-id prod@prompt \
    --ask-become-pass \
    --limit carly
```
