# Lien canva architecture
- https://www.canva.com/design/DAF8rxuZDIA/tP4KMKWc6OEHi1qUd_RH-g/edit
  
---

# Argumentation choix docker swarm

Bien que kubernetes offre une solution plus robuste et évolutive, il est idéal pour des environnements de prod complexes et à grande échelle.  
Pour un cas de projet scolaire et donc des environnements moins complexes, docker swarm est plus adapté à l'infrastructure que nous mettons en place.  
De plus, son utilisation est plus adaptée pour des débutants avec la technologie de conteneurs.  

---

# Documentation

Pour lancer le projet :
```bash
$ cd ansible
$ ansible-playbook -i hosts.ini playbooks/infrastructure.yml --ask-vault-pass -vvv
$ P@SSW0RD
```

Utilisation d'ansible vault :
```bash
ansible-vault encrypt ansible/roles/gitlab/defaults/main.yml
ansible-vault decrypt ansible/roles/gitlab/defaults/main.yml
ansible-vault edit ansible/roles/gitlab/defaults/main.yml
```

## Role docker_swarm

On commence par installer les prérequis pour le bon déploiement du cluster Docker Swarm.  
Puis on télécharge la clé GPG qui permet de garantir l'authenticité des téléchargements.  
On vient ensuite mettre en place le service Docker.  
On initialise Docker Swarm sur le noeud Manager puis on vient rejoindre les noeuds Workers pour les intégrer au cluster.  
On effectue une petite vérification avec la commande : sudo docker node ls

## Role docker_registry

On déploie un service Docker Registry dans notre cluster Docker Swarm.  
On monte ensuite le répertoire local "/opt/stockage" à l'emplacement "/var/lib/registry".  
Cela permet de stocker les données du registre Docker en dehors du conteneur, ce qui les rend persistantes.  
On met une clause sur notre hôte manager.  
On effectue une petite vérification avec la commande : sudo docker service ps registry  

## Role gitlab

Dans defaults, nous venons stocker les variables qui ne vont pas être amenées à changer, notament certaines variables sensibles.  
C'est pourquoi nous venons l'encrypter avec ansible-vault (voir commandes plus haut).  
Nous venons installer les dépendances pour gitlab.  
Nous mettons en place gitlab sur la vm Manager avec la configuration qu'il faut.  
Nous définissons une url pour l'appeller plus simplement : http://gitlab-clo.com/  
On vient ensuite créer un jeton d'accès personnel via gitlab-rails et l'attribuer au user 'lecler_a' avec les autorisations nécessaires (créé aupréalable).  
On s'assure de le créer uniquement si l'on ne le trouve pas dans token_stdout qui récupère la valeur renvoyée par l'api gitlab pour le token de nom 'token_aleclercq'.  
Une fois que l'utilisateur lecler_a a les droits suffisants, on vient créer 2 users admins pour les professeurs.  
On effectue une petite vérification avec la commande : sudo docker-ctl status

---

# A regarder

- [x] Handlers ansible
- [x] Conditionals ansible
- [x] Ansible vault
- [x] Magic facts
- [x] Tags

---

# Sources

- https://docs.gitlab.com/
- https://docs.ansible.com/ansible/latest/
