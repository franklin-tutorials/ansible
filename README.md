
# 🚀 Débuter avec Ansible sous Docker

Bienvenue dans ce **LAB** d’initiation à **Ansible** !  
Tu vas apprendre à **automatiser des tâches répétitives** sur des machines Linux dans un environnement **conteneurisé**, sans rien casser sur ta machine, et en consommant **le moins de ressources possibles**.

> 🐳 Grâce à **Docker**, chaque machine virtuelle est remplacée par un **conteneur léger**, rapide à lancer et à supprimer. C’est idéal pour s’exercer sur une configuration proche de la réalité, sans utiliser des machines virtuelles lourdes.

---

⚠️ **IMPORTANT** :  
**LES IMAGES DOCKER DE CE LAB SONT RÉSERVÉES À DES FINS DE TEST ET D’APPRENTISSAGE UNIQUEMENT !**    ➡️ **NE PAS LES UTILISER DANS UN  ENVIRONNEMENT DE PRODUCTION.**


---


## ⚙️ Et pourquoi Docker Compose ?

Docker Compose est un outil qui permet de :
- **définir plusieurs conteneurs dans un seul fichier (`docker-compose.yaml`)**
- **les lancer tous en une seule commande**

🎯 C’est parfait pour notre lab : un conteneur de contrôle de node Ansible + plusieurs conteneurs des nodes clients.

---

## ✅ Prérequis à installer

Avant de commencer, selon ton OS installes sur ta machine :

- [Docker Desktop](https://www.docker.com/get-started/)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Git](https://git-scm.com/downloads)


---

## 📁 Structure du lab

Le fichier `docker-compose.yaml` crée les conteneurs suivants :

| Nom | Rôle | Port SSH | Image utilisée |
|-----|------|----------|----------------|
| `ansible` | Conteneur de contrôle Ansible | 2222 | `ftutorials/ubuntu-ansible` |
| `client1` | Conteneur cible Ubuntu | 2223 | `ftutorials/ubuntu-ssh` |
| `client2` | Conteneur cible Ubuntu | 2224 | `ftutorials/ubuntu-ssh` |
| `client3` | Conteneur cible Rocky Linux | 2225 | `ftutorials/rocky-ssh` |
| `client3` | Conteneur cible Rocky Linux | 2226 | `ftutorials/rocky-ssh` |

---

## 🚀 Comment utiliser le LAB

### 1. Cloner le projet

```bash
git clone https://github.com/franklin-tutorials/ansible.git
```

N'hésites pas à utiliser la méthode de ton choix.

### 2. Lancer les conteneurs depuis ton terminal

Se positionner à l'emplacement du fichier `docker-compose.yaml` et lancer :

```bash
cd ansible 
```

```bash
docker-compose up -d  
```

### 3. Lister et voir l'état des conteneurs depuis ton terminal

```bash
docker-compose ps 
```

### 4. Tester la connexion SSH depuis ton terminal

```bash
ssh root@localhost -p 2222  # ansible
ssh root@localhost -p 2223  # client1
ssh root@localhost -p 2224  # client2
ssh root@localhost -p 2225  # client3
```
🔑 Mot de passe : P@ssw0rd

⚠️ **IMPORTANT** :  
**LES IMAGES DOCKER DE CE LAB SONT RÉSERVÉES À DES FINS DE TEST ET D’APPRENTISSAGE UNIQUEMENT !**    ➡️ **NE PAS LES UTILISER DANS UN  ENVIRONNEMENT DE PRODUCTION.**

Le dossier config en local est partagé dans le conteneur ansible à l’emplacement : /root/config.

C’est dans ce dossier en local que tu écriras ton inventaire, tes playbooks, tes rôles etc ... Et conservera tes fichiers sur ton PC en cas de suppression de ton conteneur ansible.

### 5. Arrêter les conteneurs depuis ton terminal

```bash
docker-compose stop 
```

### 6. Arrêter et supprimer les conteneurs depuis ton terminal

```bash
docker-compose down 
```


---

## 📚 Pour aller plus loin

- [Documentation Docker](https://www.docker.com/)
- [Documentation Ansible](https://docs.ansible.com/)


---










