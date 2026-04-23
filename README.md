# LAB 7 : Analyse Dynamique Mobile avec MobSF

## 📌 Objectif du lab

L'analyse dynamique consiste à observer le comportement d'une application **pendant son exécution**, contrairement à l'analyse statique qui examine le code sans l'exécuter.

Ce lab utilise **MobSF (Mobile Security Framework)** pour analyser dynamiquement **DIVA (Damn Insecure and Vulnerable Android App)**, une application volontairement vulnérable conçue pour l'apprentissage.

**Objectifs** :
- Configurer un environnement d'analyse dynamique
- Utiliser MobSF pour intercepter logs, trafic réseau et instrumenter avec Frida
- Détecter des vulnérabilités comme le stockage non sécurisé, le hardcoding, etc.

---

## 🎯 Ce que nous avons appris

| Concept | Définition |
|---------|-------------|
| **Analyse dynamique** | Observation du comportement d'une application pendant son exécution |
| **MobSF** | Mobile Security Framework - analyse statique + dynamique |
| **DIVA** | Damn Insecure and Vulnerable Android App - app de test vulnérable |
| **Logcat Stream** | Logs Android en temps réel |
| **Frida Scripts** | Scripts d'instrumentation pour hooker des méthodes |
| **Insecure Data Storage** | Stockage de données sensibles en clair |
| **Hardcoding Issues** | Secrets (mots de passe, clés) codés en dur |

---

## 🛠️ Environnement de test

| Élément | Valeur |
|---------|--------|
| **VM** | Mobexler |
| **Émulateur** | Genymotion (API 28, rooté) |
| **Outil d'analyse** | MobSF v4.5.0 (Docker) |
| **Application testée** | DIVA (diva-beta.apk) |
| **Réseau** | Host-Only (192.168.137.x) |

---

## 🔧 Installation et configuration

### Étape 1 : Télécharger DIVA.apk

```bash

cd /home/mobexler/Downloads
wget http://www.payatu.com/wp-content/uploads/2016/01/diva-beta.tar.gz
tar -xzf diva-beta.tar.gz

```

### Étape 2 : Lancer MobSF (Docker)

```bash

sudo docker run -it -p 8000:8000 \
  -e MOBSF_ANALYZER_IDENTIFIER=192.168.137.101:5555 \
  opensecurity/mobile-security-framework-mobsf

```

### Étape 3 : Accéder à MobSF

URL : http://127.0.0.1:8000

Login : mobsf

Mot de passe : mobsf

### Étape 4 : Uploader et analyser DIVA

1. Cliquer sur "Upload & Analyze"
2. Sélectionner /home/mobexler/Downloads/diva-beta.apk
3. Attendre la fin de l'analyse statique
4. Cliquer sur "Start Dynamic Analysis"



## 📊 Fonctionnalités du Dynamic Analyzer MobSF

| Menu | Rôle | Utilité |
|------|------|---------|
| **Stop Screen** | Arrêter le mirroring de l'écran | Libère des ressources |
| **Remove Root CA** | Supprimer le certificat racine | Nettoyage après analyse HTTPS |
| **Unset HTTP(S) Proxy** | Désactiver le proxy | Retour à un réseau normal |
| **TLS/SSL Security Tester** | Tester la sécurité TLS/SSL | Vérifier les certificats |
| **Exported Activity Tester** | Tester les activités exportées | Détecter des activités accessibles |
| **Activity Tester** | Lancer les activités de l'app | Explorer les écrans internes |
| **Logcat Stream** | Logs Android en temps réel | Détecter fuites d'informations |
| **Frida Scripts** | Scripts d'instrumentation | Bypass root, SSL, monitoring |
| **Generate Report** | Exporter le rapport | PDF/JSON pour archivage |

---

## 🔍 Challenges analysés

### Challenge 2 : Hardcoding Issues

**Vulnérabilité** : Mot de passe codé en dur dans le code source.

**Méthode** : Analyse statique avec JADX.

**Résultat** : Mot de passe trouvé dans `HardcodeActivity`.

**Preuve** : Capture JADX montrant le mot de passe.

**Remédiation** : Utiliser un stockage sécurisé (Keystore, serveur d'authentification).


<img width="731" height="372" alt="challenge2" src="https://github.com/user-attachments/assets/29dcd3e5-8828-4f15-8faf-c911dea83a42" />
<img width="463" height="706" alt="challenge2_1" src="https://github.com/user-attachments/assets/7d6bd8f4-ac53-427b-99f7-ef7dc41a8614" />


---

### Challenge 3 : Insecure Data Storage

**Vulnérabilité** : Stockage de données sensibles en clair.

**Méthode** : Analyse dynamique avec `adb shell`.

**Action** :
```bash
adb shell
cd /data/data/jakhar.aseem.diva/shared_prefs
cat jakhar.aseem.diva_preferences.xml
```
<img width="1391" height="609" alt="challenge3" src="https://github.com/user-attachments/assets/dcf04d14-d445-4cbc-bd07-4d0dc9d729eb" />





### Test 3 : TLS/SSL Security Tester

**Action** : Cliquer sur "TLS/SSL Security Tester" dans MobSF.

**Résultat** : Test des vulnérabilités TLS/SSL de l'application.

<img width="1588" height="793" alt="Test_TLS" src="https://github.com/user-attachments/assets/23b0ac4d-404c-4976-8ff0-2ac093176cb3" />





## 📸 Autres Captures d'écran

| # | Description |
|---|-------------|
| <img width="1600" height="715" alt="Etap4" src="https://github.com/user-attachments/assets/91b7fc79-9466-4d72-af13-d386d0811b49" />| MobSF - Interface d'accueil |
| <img width="1535" height="778" alt="Etap6" src="https://github.com/user-attachments/assets/53dfa9ea-f4f8-4873-babd-b9b8cccab5a5" />| Analyse statique - Score de sécurité |
| <img width="1595" height="656" alt="Etap6_2" src="https://github.com/user-attachments/assets/35e9d593-b9d2-4e09-91c3-b49d64ee929e" />| Dynamic Analyzer - Interface |


---

## 🧠 Problèmes rencontrés et solutions

| Problème | Solution |
|----------|----------|
| Logcat Stream ne capturait pas les logs DIVA | Utilisation de `adb logcat \| grep -i diva` |
| Screen streaming non fonctionnel | Contournement avec l'émulateur direct |
| RAM insuffisante (8 GB) | Réduction de la RAM allouée à Mobexler |


## ✅ Checklist

### Début de lab

| Vérification | Statut |
|--------------|--------|
| DIVA.apk téléchargé | ✅ |
| Genymotion démarré (API 28) | ✅ |
| Mobexler en Host-Only | ✅ |
| Ping avec émulateur OK | ✅ |
| MobSF Docker lancé | ✅ |

### Pendant le lab

| Vérification | Statut |
|--------------|--------|
| Analyse statique DIVA | ✅ |
| Lancement Dynamic Analysis | ✅ |
| Challenge 2 - Hardcoding | ✅ |
| Challenge 3 - Insecure Storage | ✅ |
| TLS/SSL Security Tester | ✅ |
| Frida API Monitoring | ✅ |

### Fin de lab

| Vérification | Statut |
|--------------|--------|
| Screenshots pris | ✅ |

---

## ⚠️ Remarque sur les limitations rencontrées

Lors de ce lab, nous avons rencontré plusieurs limitations techniques :

| Limitation | Impact |
|------------|--------|
| **RAM limitée (8 GB)** | Ralentissements et instabilité de l'environnement (Mobexler + Docker + Genymotion) |
| **Screen streaming non fonctionnel** | Impossible d'utiliser le mirroring d'écran de MobSF |
| **Logcat Stream partiel** | Certains logs de DIVA n'apparaissaient pas dans l'interface MobSF |
| **API de l'émulateur (API 28)** | MobSF est optimisé pour API 29-30, certaines fonctionnalités avancées ne sont pas disponibles |

**Conséquence** : Toutes les fonctionnalités de MobSF Dynamic Analyzer n'ont pas pu être testées. L'analyse a été complétée avec des outils complémentaires (`adb shell`, `adb logcat`).

**Pour un environnement de test optimal** :
- 16 GB RAM minimum
- Émulateur API 29 ou 30 (Android 10+)
- Docker avec ressources dédiées





## 👤 Auteur

**El Hachimi Abdelhamid**  
Date : 2026-04-23  
Cours : Sécurité des applications mobiles
