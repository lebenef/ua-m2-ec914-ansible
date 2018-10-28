# ua-m2-ec914-ansible

# Sudo

Lorsque l’on exporte une clé ssh afin de se connecter automatiquement à une machine distante, lors de l’utilisation des commande sudo il est nécessaire de saisir le mots de passe sudo. Il est possible d’ajouter dans le clé publique du client au serveur en temps que root pour permettre l’utilisation des commande sudo sans  mot de passe, Cependant cette technique n’est pas conventionnelle.


# Ansible

## Playbook

Un playbook est un script écris en YAML qui permet de configurer un serveur.
Il décrit les tâches que Ansible doit acomplir sur le seveur.

## Role

Un role est un playbook structurer pour répondre a une utilisation plus large et réutilisable.

# Configuration du serveur Ansible

## Création clé ssh

On crée une clé ssh afin de pouvoir se connecter sans mot de passe à notre serveur cible.
```ssh-keygen -t rsa``

Puis on copy la clé fraichement créée sur notre serveur cible.
```ssh-copy-id -i ~.ssh/key.pub user@host```

## Installation

Pour installer Ansible on utlise la commande.
```sudo apt-get install ansible```

## Configuration Hosts
Le fichier Hosts permet de renseigné tout les machines  qu' Ansible doit gérer, il n'est pas possible de déployer sur une machines si son nom ou son adresse ip n'est pas renseigné.
Il faut donc renseigner l'adresse ip de la machine que l'on va utiliser pour recevoir le déploiement
de Ansible.

```/etc/ansible/hosts```

Une fois ceci fait on teste que ansible communique bien avec les serveur cible.
```ansible all -m ping```

# Role 

## Playbook

Nous avons commencé par créer un playbook que l'on appellé script.yml.
Dans ce playbook nous avons renseigné les client grace a host, le nom d'utilisateur ssh grace a remote_user, le nom de l'utilisateur sur la machine distante avec become_user , la méthode utilisé avec become_method et le type connexion avec connection.
```
- hosts: all
  remote_user: username
  become: true
  become_user: root
  become_method: su
  connection: ssh 
```
Cette partie permet la connection avec les clients.

Nous avons ensuite ajouté les dépots grace a apt_repository qui nécéssite l'instation de python_apt sur client.
```
 - name: Add  Repo
   apt_repository:
       repo: deb address_repository version_repository
       state: present
```

Certain dépot nécéssite une clé de valition il donc parfois obligatoire de l'ajouter avant d'ajouter le depot.
```
- name : Add DotDeb Key
  apt_key: 
      url: address_key
```

Une fois ceci fait on déploie le script en utilisant la commande:
```
ansible-playbook script.yml -K
```
L'attribut -K permet de saisir de le mot de passe afin de pouvoir installer les dépot.

