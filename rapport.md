# Rapport – Automatisation de l'installation et de la sécurisation de MariaDB

# Introduction

Dans le cadre du module 122, j'ai réalisé un projet consistant à automatiser l'installation et la sécurisation de MariaDB sur un serveur Linux à l'aide d'un script Bash.

L'objectif de ce projet est de simplifier les tâches d'administration système en automatisant l'installation, la configuration et la création d'une base de données sans intervention manuelle. Toutes les opérations réalisées sont enregistrées dans un fichier log afin de permettre un suivi des actions effectuées.

Pour structurer ce travail, j'ai utilisé la méthode des 6 étapes.

# Table des matières

1. S'informer

2. Planifier

3. Décider

4. Concevoir

5. Tester

6. Évaluer

7. Conclusion

# 1. S'informer

Cette étape consiste à comprendre le mandat, recueillir les informations nécessaires et identifier les outils à utiliser.

## Description du mandat

Le responsable système souhaite automatiser l'installation et la configuration de MariaDB sur plusieurs serveurs Linux afin de gagner du temps et d'éviter les erreurs de configuration manuelles.

## Qu'est-ce que MariaDB ?

MariaDB est un système de gestion de bases de données relationnelles (SGBDR) open source. Il permet de stocker, organiser et gérer des données utilisées par des applications web ou métiers.

## Pourquoi automatiser l'installation ?

L'automatisation permet :

* de gagner du temps ;
* d'éviter les oublis ;
* de garantir une configuration identique sur plusieurs serveurs ;
* de réduire les erreurs humaines.

## Commandes Linux nécessaires

**apt update** : Met à jour la liste des paquets.

**apt install mariadb-server** : Installe MariaDB sur le serveur.

**systemctl start mariadb** : Démarre le service MariaDB.

**systemctl enable mariadb** : Active MariaDB au démarrage.

**mariadb** : Permet d'accéder à MariaDB.

**chmod** : Modifie les permissions d'un fichier.

**mkdir** : Crée un dossier.

**cat** : Affiche le contenu d'un fichier.

## Risques à prendre en compte

* erreur dans les paramètres du script ;
* échec de l'installation de MariaDB ;
* mot de passe faible ;
* mauvaise attribution des droits ;
* absence de journalisation des erreurs.

# 2. Planifier

Cette étape permet d'organiser les tâches à réaliser et de définir l'ordre des différentes actions.

## Étapes du script

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

# 3. Décider

Cette étape consiste à choisir les solutions techniques les plus adaptées aux besoins du projet.

## Pourquoi utiliser des paramètres ?

Les paramètres permettent de réutiliser le script avec différentes bases de données et différents utilisateurs sans modifier le code.

## Pourquoi créer un fichier log ?

Le fichier log permet :

* de suivre les opérations réalisées ;
* d'identifier les erreurs ;
* de faciliter le dépannage.

## Pourquoi sécuriser MariaDB ?

La sécurisation protège le serveur contre les accès non autorisés en :

* définissant un mot de passe root ;
* supprimant les utilisateurs anonymes ;
* supprimant la base de test ;
* interdisant les connexions root distantes.

## Pourquoi publier sur GitHub ?

GitHub permet :

* de sauvegarder le projet ;
* de partager le code ;
* de conserver l'historique des modifications ;
* de faciliter la maintenance.

# 4. Concevoir

Cette étape correspond à la réalisation concrète du projet et au développement du script.

## Logique du script

Le script reçoit cinq paramètres :

* mot de passe root MariaDB ;
* nom de la base de données ;
* nom de l'utilisateur ;
* mot de passe utilisateur ;
* nom du fichier log.

Après vérification des paramètres, le script :

* installe MariaDB ;
* démarre le service ;
* sécurise le serveur ;
* crée la base de données ;
* crée l'utilisateur ;
* attribue les droits ;
* écrit les informations dans le fichier log.

## Commandes importantes utilisées

```bash
apt update
apt install mariadb-server -y
systemctl start mariadb
systemctl enable mariadb
```

## Sécurisation MariaDB

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'motdepasse';
DELETE FROM mysql.user WHERE User='';
DROP DATABASE IF EXISTS test;
FLUSH PRIVILEGES;
```

## Création de la base et de l'utilisateur

```sql
CREATE DATABASE IF NOT EXISTS app_interne;
CREATE USER IF NOT EXISTS 'app_user'@'localhost';
GRANT ALL PRIVILEGES ON app_interne.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
```

# 5. Tester

Cette étape permet de vérifier que le script fonctionne correctement et respecte le cahier des charges.

## Test 1 : exécution sans paramètres

Résultat :

Le script affiche le message d'utilisation et s'arrête correctement.

## Test 2 : exécution avec paramètres valides

Résultat :

L'installation s'effectue automatiquement sans intervention de l'utilisateur.

## Test 3 : vérification de MariaDB

Commande :

```bash
mariadb --version
```

Résultat :

MariaDB est correctement installé.

## Test 4 : vérification du service

Commande :

```bash
systemctl status mariadb
```

Résultat :

Le service est actif (running).

## Test 5 : vérification de la base de données

Commande :

```sql
SHOW DATABASES;
```

Résultat :

La base de données app_interne est présente.

## Test 6 : vérification de l'utilisateur

Commande :

```sql
SELECT User, Host FROM mysql.user;
```

Résultat :

L'utilisateur app_user est présent.

## Test 7 : vérification du fichier log

Commande :

```bash
cat install_mariadb.log
```

Résultat :

Toutes les opérations et les messages sont enregistrés.

## Erreurs rencontrées

* L'utilisateur thibault ne possédait pas les droits sudo.
* Le script était vide lors de la première sauvegarde.
* Certaines commandes ont été exécutées depuis le mauvais répertoire.

## Corrections effectuées

* Connexion avec le compte root.
* Vérification du contenu du script.
* Exécution depuis le dossier du projet.

# 6. Évaluer

Cette étape consiste à analyser le résultat obtenu, identifier les points forts et les améliorations possibles.

## Ce qui a bien fonctionné

* Installation automatique de MariaDB.
* Création de la base de données.
* Création de l'utilisateur.
* Génération du fichier log.

## Ce qui a été difficile

* Gestion des droits administrateur.
* Débogage du script lors des premiers tests.

## Ce que j'ai appris

* Automatiser une installation avec Bash.
* Utiliser MariaDB en ligne de commande.
* Gérer les services Linux avec systemctl.
* Utiliser un fichier log pour le suivi des opérations.

## Ce que je ferais différemment

* Ajouter davantage de vérifications d'erreurs.
* Améliorer les messages affichés à l'écran.
* Ajouter des fonctions Bash pour rendre le script plus modulaire.

# Conclusion

Le script répond aux exigences du cahier des charges. Il permet d'installer, sécuriser et configurer MariaDB automatiquement sur un serveur Debian ou Ubuntu sans intervention manuelle. Toutes les opérations sont enregistrées dans un fichier log et les tests réalisés confirment son bon fonctionnement.
