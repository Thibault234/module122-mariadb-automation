# Rapport – Automatisation de l'installation et de la sécurisation de MariaDB

## 1. S'informer

### Description du mandat

Le responsable système souhaite automatiser l'installation et la configuration de MariaDB sur plusieurs serveurs Linux afin de gagner du temps et d'éviter les erreurs de configuration manuelles.

### Qu'est-ce que MariaDB ?

MariaDB est un système de gestion de bases de données relationnelles (SGBDR) open source. Il permet de stocker, organiser et gérer des données utilisées par des applications web ou métiers.

### Pourquoi automatiser l'installation ?

L'automatisation permet :

* de gagner du temps ;
* d'éviter les oublis ;
* de garantir une configuration identique sur plusieurs serveurs ;
* de réduire les erreurs humaines.

### Commandes Linux nécessaires

**apt update**
Met à jour la liste des paquets disponibles dans les dépôts Debian afin de disposer des versions les plus récentes des logiciels.

**apt install mariadb-server**
Installe le serveur de base de données MariaDB ainsi que les dépendances nécessaires à son fonctionnement.

**systemctl start mariadb**
Démarre immédiatement le service MariaDB sur le système.

**systemctl enable mariadb**
Configure MariaDB pour qu'il démarre automatiquement à chaque démarrage du serveur.

**mariadb**
Permet de se connecter au serveur MariaDB et d'exécuter des commandes SQL.

**chmod**
Modifie les permissions d'un fichier. Dans ce projet, il est utilisé pour rendre le script exécutable.

**mkdir**
Crée un ou plusieurs répertoires sur le système Linux.

**cat**
Affiche le contenu d'un fichier dans le terminal, notamment pour consulter le fichier log généré par le script.


### Risques à prendre en compte

* erreur dans les paramètres du script ;
* échec de l'installation de MariaDB ;
* mot de passe faible ;
* mauvaise attribution des droits ;
* absence de journalisation des erreurs.

---

## 2. Planifier

### Étapes du script

1. Vérifier la présence des paramètres.
2. Mettre à jour la liste des paquets.
3. Installer MariaDB.
4. Démarrer le service MariaDB.
5. Activer le service au démarrage.
6. Sécuriser MariaDB.
7. Créer la base de données.
8. Créer l'utilisateur MariaDB.
9. Attribuer les privilèges.
10. Recharger les privilèges.
11. Enregistrer les opérations dans un fichier log.
12. Vérifier le bon fonctionnement du service.

---

## 3. Décider

### Pourquoi utiliser des paramètres ?

Les paramètres permettent de réutiliser le script avec différentes bases de données et différents utilisateurs sans modifier le code.

### Pourquoi créer un fichier log ?

Le fichier log permet :

* de suivre les opérations réalisées ;
* d'identifier les erreurs ;
* de faciliter le dépannage.

### Pourquoi sécuriser MariaDB ?

La sécurisation protège le serveur contre les accès non autorisés en :

* définissant un mot de passe root ;
* supprimant les utilisateurs anonymes ;
* supprimant la base de test ;
* interdisant les connexions root distantes.

### Pourquoi publier sur GitHub ?

GitHub permet :

* de sauvegarder le projet ;
* de partager le code ;
* de conserver l'historique des modifications ;
* de faciliter la maintenance.

---

## 4. Concevoir

### Logique du script

Le script reçoit cinq paramètres :

* mot de passe root MariaDB ;
* nom de la base de données ;
* nom de l'utilisateur ;
* mot de passe utilisateur ;
* nom du fichier log.

Après vérification des paramètres, le script :

1. installe MariaDB ;
2. démarre le service ;
3. sécurise le serveur ;
4. crée la base de données ;
5. crée l'utilisateur ;
6. attribue les droits ;
7. écrit les informations dans le fichier log.

### Commandes importantes utilisées

```bash
apt update
apt install mariadb-server -y
systemctl start mariadb
systemctl enable mariadb
```

### Sécurisation MariaDB

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'motdepasse';
DELETE FROM mysql.user WHERE User='';
DROP DATABASE IF EXISTS test;
FLUSH PRIVILEGES;
```

### Création de la base et de l'utilisateur

```sql
CREATE DATABASE IF NOT EXISTS app_interne;
CREATE USER IF NOT EXISTS 'app_user'@'localhost';
GRANT ALL PRIVILEGES ON app_interne.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
```

---

## 5. Tester

### Test 1 : exécution sans paramètres

Résultat :

Le script affiche le message d'utilisation et s'arrête correctement.

### Test 2 : exécution avec paramètres valides

Résultat :

L'installation s'effectue automatiquement sans intervention de l'utilisateur.

### Test 3 : vérification de MariaDB

Commande :

```bash
mariadb --version
```

Résultat :

MariaDB est correctement installé.

### Test 4 : vérification du service

Commande :

```bash
systemctl status mariadb
```

Résultat :

Le service est actif (running).

### Test 5 : vérification de la base de données

Commande :

```bash
SHOW DATABASES;
```

Résultat :

La base de données app_interne est présente.

### Test 6 : vérification de l'utilisateur

Commande :

```sql
SELECT User, Host FROM mysql.user;
```

Résultat :

L'utilisateur app_user est présent.

### Test 7 : vérification du fichier log

Commande :

```bash
cat install_mariadb.log
```

Résultat :

Toutes les opérations et les messages sont enregistrés.

### Erreurs rencontrées

* L'utilisateur thibault ne possédait pas les droits sudo.
* Le script était vide lors de la première sauvegarde.
* Certaines commandes ont été exécutées depuis le mauvais répertoire.

### Corrections effectuées

* Connexion avec le compte root.
* Vérification du contenu du script.
* Exécution depuis le dossier du projet.

---

## 6. Évaluer

### Ce qui a bien fonctionné

* Installation automatique de MariaDB.
* Création de la base de données.
* Création de l'utilisateur.
* Génération du fichier log.

### Ce qui a été difficile

* Gestion des droits administrateur.
* Débogage du script lors des premiers tests.

### Ce que j'ai appris

* Automatiser une installation avec Bash.
* Utiliser MariaDB en ligne de commande.
* Gérer les services Linux avec systemctl.
* Utiliser un fichier log pour le suivi des opérations.

### Ce que je ferais différemment

* Ajouter davantage de vérifications d'erreurs.
* Améliorer les messages affichés à l'écran.
* Ajouter des fonctions Bash pour rendre le script plus modulaire.

## Conclusion

Le script répond aux exigences du cahier des charges. Il permet d'installer, sécuriser et configurer MariaDB automatiquement sur un serveur Debian ou Ubuntu sans intervention manuelle. Toutes les opérations sont enregistrées dans un fichier log et les tests réalisés confirment son bon fonctionnement.
