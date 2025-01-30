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

wpscan --url http://192.168.1.4/assets/fonts/blog --plugins-detection mixed --enumerate ap --api-token=B4ZGWKhJHufoRPD6PAzqdQlraiSHRxinJF6zzaa6qtY

**Résultats du scan :**
| Plugin/Vulnérabilité | Description | Niveau de Risque |
|----------------------|-------------|------------------|
| Plugin X | Version obsolète, permet injection SQL | ⚠️ Critique |
| Plugin Y | Mauvaise configuration des permissions | 🟠 Élevé |
| WordPress Core | Version vulnérable à XSS (CVE-XXXX-XXXX) | 🟡 Moyen |

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

## 🏁 7. Conclusion

Ce rapport met en évidence plusieurs vulnérabilités sur la machine **Blogger**, notamment un WordPress obsolète avec des plugins vulnérables. Des mesures correctives doivent être appliquées rapidement pour limiter les risques d'attaques.

---

## 📌 Annexes
- Captures d’écran des résultats des scans
- Détails des commandes exécutées
- Liste complète des vulnérabilités détectées
