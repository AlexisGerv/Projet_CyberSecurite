# Projet_CyberSecurite
# Exploitation & Remédiation d'un Serveur Vulnérable

## 1. Contexte et Objectif
Ce projet a pour but de configurer un environnement de laboratoire sécurisé (Home Lab) afin d'identifier et d'exploiter des vulnérabilités connues sur un serveur cible. L'objectif final est d'obtenir un accès administrateur (Root) pour démontrer l'impact critique d'un service obsolète et mal configuré, puis de proposer des solutions de remédiation.

---

## 2. Environnement de Laboratoire
* **Hyperviseur :** VirtualBox (Réseau privé hôte / Host-Only Adapter pour une isolation totale du réseau).
* **Machine Attaquante :** Kali Linux.
* **Machine Cible :** Metasploitable 2 (IP : `192.168.56.104`).

---

## 3. Méthodologie

### Phase 1 : Reconnaissance et Énumération
Après avoir validé la connectivité réseau via la commande `ping`, un scan de ports ciblé a été lancé pour identifier les services exposés sur la cible. Une attention particulière a été portée au port 21 (FTP).

nmap -sV -p 21 192.168.56.104
Résultat : Le port 21 est ouvert et exécute le service vsftpd 2.3.4.

Phase 2 : Identification de la Vulnérabilité
La version vsftpd 2.3.4 est mondialement connue pour abriter une porte dérobée (backdoor) malveillante (CVE-2011-2523). Si un utilisateur tente de se connecter avec un identifiant contenant un smiley :), le service ouvre silencieusement un accès de type "shell" avec les privilèges maximums sur le port 6200, sans exiger de mot de passe.

Phase 3 : Exploitation
L'outil Metasploit Framework a été utilisé pour automatiser l'exploitation de cette faille.

Commandes exécutées dans msfconsole :

use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.56.104
exploit
Phase 4 : Post-Exploitation
L'exploit a réussi avec succès, générant un accès direct (dumb shell) sur la machine cible en arrière-plan. La commande whoami a confirmé l'obtention des pleins privilèges root.

(Insérez ici l'image de votre terminal Kali Linux montrant l'exploit réussi)
<img width="704" height="529" alt="image" src="https://github.com/user-attachments/assets/6640a6cf-1715-49d6-ad39-eb00b1008b22" />

Pour prouver la compromission totale du système, le fichier sensible contenant les empreintes cryptographiques des mots de passe a été lu via la commande cat /etc/shadow.


4. Recommandations (Remédiation)

Pour sécuriser ce serveur et empêcher de futures intrusions, les actions suivantes doivent être appliquées :

Mettre à jour le service : Remplacer immédiatement vsftpd 2.3.4 par une version récente et corrigée.

Changer de protocole : Privilégier des protocoles chiffrés comme SFTP (SSH File Transfer Protocol) au lieu du FTP classique qui fait transiter les données en clair sur le réseau.

Filtrage réseau : Configurer un pare-feu (Firewall) pour restreindre l'accès au service de transfert de fichiers aux seules adresses IP de confiance.
