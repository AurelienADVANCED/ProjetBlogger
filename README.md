# 📌 Rapport de Vulnérabilité - Blogger

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

sudo netdiscover -r 192.168.1.0/24

Une machine avec l’IP `192.168.1.4` a été détectée.

![image](https://github.com/user-attachments/assets/b6b1c037-3b74-4804-8232-aacc448f5422)

---

## 🚀 2. Analyse des Ports et Services

### 🛠 Scan Nmap
Un scan approfondi a été réalisé avec `nmap` pour identifier les services exposés et leurs versions :

nmap -sS -sC -sV -A -O -vvv 192.168.1.4


**Résultats :**
| Port | Service | Version |
|------|---------|---------|
| 22   | SSH     | OpenSSH X.X |
| 80   | HTTP    | Apache/WordPress |

On constate que **deux ports sont ouverts** : 
- **Port 22 (SSH)** 
- **Port 80 (HTTP)**

Le **port 80** héberge un service web accessible via un navigateur.

---

## 🌐 3. Exploration du Site Web

### 🖥 Accès au Site Web
En entrant l'adresse `http://192.168.1.4` dans le navigateur, une page web s'affiche.

### 📂 Recherche de Contenus Cachés avec Dirb
Un scan avec `dirb` a été effectué pour découvrir des répertoires cachés :

dirb http://192.168.1.4

**Résultats :**
- **Répertoire découvert :** `/assets/fonts/blog`
- Ce répertoire contient une instance **WordPress**.

### 🛠 Ajout d'une Entrée dans le Fichier Hosts
Pour faciliter l’accès, un alias `blogger.thm` a été ajouté dans le fichier `/etc/hosts` :

echo "192.168.1.4 blogger.thm" | sudo tee -a /etc/hosts


---

## 🔥 4. Scan de Vulnérabilités WordPress

### 🏴 Utilisation de WPScan
Un scan approfondi a été réalisé sur WordPress pour détecter les vulnérabilités :

**Commande exécutée :**
wpscan --url http://192.168.1.4/assets/fonts/blog --plugins-detection mixed --enumerate ap --api-token=B4ZGWKhJHufoRPD6PAzqdQlraiSHRxinJF6zzaa6qtY

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

## ⚠️ 5. Exploitation Potentielle et Risques

### 🔑 Vulnérabilités Identifiées
1. **WordPress obsolète** : Potentiel XSS et SQL Injection.
2. **Plugins vulnérables** : Certains plugins non mis à jour contiennent des failles exploitables.
3. **Exposition du service SSH** : Potentiellement bruteforcable.

### 🚨 Risques et Conséquences
- **Prise de contrôle de l’instance WordPress**
- **Éventuel accès aux fichiers sensibles**
- **Possibilité d’escalade de privilèges via un plugin vulnérable**

---

## 🛡️ 6. Recommandations de Sécurité

1. **Mettre à jour WordPress et ses plugins** pour combler les failles connues.
2. **Restreindre l'accès au SSH** en limitant les connexions aux adresses IP de confiance.
3. **Désactiver les plugins inutilisés** et renforcer la configuration des permissions.
4. **Mettre en place un pare-feu (WAF)** pour filtrer les requêtes malveillantes.
5. **Auditer régulièrement la sécurité du serveur** pour détecter d’éventuelles nouvelles failles.

---

## 📌 Annexes

