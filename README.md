# TP-ANSIBLE-HAPROXY

## 1. Objectif

Ce projet ANSIBLE permet de déployer automatiquement une infrastructure web simple composée de :

* Un serveur **HAProxy** (`lb1`) qui fait le **load balancing**.
* Deux serveurs **web/app** (`web1` et `web2`) servant le même site web.
* Possibilité d’ajouter plus de serveurs web si nécessaire.

L’ensemble est configuré automatiquement grâce à **Ansible**, sans intervention manuelle sur les machines.

---

## 2. Prérequis

1. Une machine de contrôle (Linux/WSL) avec **Ansible 2.9+**.
2. Accès **SSH fonctionnel** vers toutes les machines (HAProxy + webservers).
3. Les serveurs doivent être sous **Ubuntu** ou une distribution Linux compatible.

---

## 3. Installation d’Ansible

Sur la machine de contrôle :

```bash
sudo apt update
sudo apt install ansible -y
ansible --version
```

* Testez la connexion SSH vers vos serveurs :

```bash
ansible all -i inventories/production/hosts -m ping
```

---

## 4. Configuration de l’inventaire

Le fichier **`inventories/production/hosts`** existe déjà.
Mettez vos machines dans les bons groupes et avec les bonnes informations :

```ini
[lb]
lb1 ansible_host=<IP_HAPROXY> ansible_user=ubuntu

[web]
web1 ansible_host=<IP_WEB1> ansible_user=ubuntu
web2 ansible_host=<IP_WEB2> ansible_user=ubuntu
```

* `<IP_HAPROXY>`, `<IP_WEB1>`, `<IP_WEB2>` → à remplacer par vos IP réelles.
* Chaque ligne contient : Nom, ansible_host et ansible_user.

---

## 5. Structure du projet

```
TP-ANSIBLE-HAPROXY/
├─ ansible.cfg                 # Configuration Ansible (inventaire, roles_path, host_key_checking)
├─ inventories/production/hosts
├─ playbooks/
│  ├─ common.yml
│  ├─ webserver.yml
│  └─ lb.yml
├─ roles/
│  ├─ common/
│  ├─ webserver/
│  └─ haproxy/
├─ group_vars/
└─ host_vars/
```

---

## 6. Lancement des playbooks

Depuis la racine du projet :

1. Installer les packages communs :

```bash
ansible-playbook playbooks/common.yml
```

2. Déployer les serveurs web :

```bash
ansible-playbook playbooks/webserver.yml
```

3. Déployer le load balancer HAProxy :

```bash
ansible-playbook playbooks/lb.yml
```


---

## 7. Vérification

* Accédez à l’IP du load balancer (`lb1`) via un navigateur ou avec curl :

```bash
curl http://<IP_HAPROXY>
```

* Les requêtes doivent être réparties entre `web1` et `web2`.
* Tester la tolérance à la panne : arrêter Nginx sur un serveur et vérifier que HAProxy continue de servir les autres.

---

## 8. Informations complementaires

* Playbooks idempotents : relancer plusieurs fois ne casse pas l’infrastructure.
* Configurations centralisées via **templates Jinja2**.
* Rôles structurés selon la hiérarchie standard Ansible.
* Projet versionné avec Git.

---

## 9. Dépôt GitHub

* Cloner le projet :

```bash
git clone git@github.com:RaphaelArz/TP-ANSIBLE-HAPROXY.git
cd TP-ANSIBLE-HAPROXY
```