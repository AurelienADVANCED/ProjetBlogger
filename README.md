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


