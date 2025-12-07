# 🩺 Système Distribué de Télé-Radiologie
*Projet réalisé dans le cadre du module DAR — Développement d’Applications Réparties*

---

## 📌 Description du Projet
Ce projet consiste en une simulation d’une plateforme de **Télé-expertise médicale** utilisant une architecture distribuée de type **Grille de Calcul (Grid Computing)**.

L’objectif est de traiter des images médicales volumineuses (IRM / Radiographies) en **répartissant la charge de calcul** sur plusieurs nœuds (*Workers*).  
Le traitement appliqué est un **filtre de Sobel** permettant d’extraire les contours anatomiques (fractures, masses, tumeurs…).

Ce projet démontre l’utilisation combinée de plusieurs technologies distribuées : **RMI, JMS (ActiveMQ) et CORBA**.

---

## 🎯 Objectifs Académiques
Le projet met en pratique les compétences suivantes :

- **Java RMI** : communication synchrone + transfert d’objets complexes (matrices de pixels).  
- **JMS (ActiveMQ)** : communication asynchrone pour la visualisation en temps réel.  
- **CORBA** : supervision distante et interopérabilité multi-langages.  
- **Calcul parallèle** : découpage et traitement distribué de données.  

---

## 🏗️ Architecture Globale
Le système est composé de **quatre modules indépendants**, communiquant via différents middlewares :

### 1️⃣ Serveur Hospitalier (Master – RMI Server)
- Charge l’image médicale.  
- Découpe l’image en segments horizontaux (*chunks*).  
- Expose le service RMI **ComputeService**.  
- Intègre un module **CORBA** pour le monitoring.  

### 2️⃣ Workers (Nœuds de Calcul — RMI Clients)
- Récupèrent dynamiquement les tâches via RMI.  
- Appliquent le filtre de Sobel.  
- Renvoient au serveur un objet **ProcessedChunk**.  
- Plusieurs instances doivent être lancées pour simuler la grille.  

### 3️⃣ Dashboard Médecin (Client JMS)
- Interface Swing totalement indépendante du serveur.  
- S’abonne au Topic JMS *TeleRadiology*.  
- Affiche les segments calculés au fur et à mesure (*rendu progressif*).  

### 4️⃣ Client de Supervision (CORBA)
- Interroge l’état du serveur (CPU, tâches restantes, disponibilité...).  
- Utilise une interface IDL standardisée (interopérable C++/Python/Java).  

---

## 🧰 Prérequis Techniques
- **JDK 1.8** (recommandé pour compatibilité CORBA + RMI).  
- **Apache ActiveMQ** ≥ 5.16.  
- **Maven** (gestionnaire de dépendances).  

---

## 🚀 Installation & Exécution

### 1️⃣ Démarrer ActiveMQ
```bash
./activemq start
2️⃣ Compiler le projet

À la racine :

mvn clean install

3️⃣ Lancer les modules (dans cet ordre)
A. Serveur Hospitalier

Classe :

server.HospitalServer


Fonctions :

Initialise RMI

Charge l’image

Active le module CORBA

B. Dashboard Médecin (Visualisation)

Classe :

client.DoctorClient


Fonction :

Affiche les segments traités reçus via JMS

C. Client de Supervision CORBA (Optionnel)

Classe :

client.AdminConsole

D. Workers (3 minimum recommandés)

Classe :

worker.WorkerNode


✔️ Le dashboard affichera les segments dans un ordre non-séquentiel, démontrant le parallélisme réel.

⚙️ Justification des Choix Technologiques
💠 Pourquoi RMI ?

Communication simple et typée

Sérialisation native des objets Java

Idéal pour transporter des matrices de pixels + métadonnées

💠 Pourquoi JMS (ActiveMQ) ?

Découplage total entre calcul et visualisation

Résilience aux déconnexions

Permet plusieurs dashboards en parallèle

💠 Pourquoi CORBA ?

Démonstration d’interopérabilité (IDL standard)

Supervision possible depuis n’importe quel langage

Indépendance vis-à-vis de la JVM

🧠 Détails Algorithmiques

Le filtre de Sobel applique deux convolutions (GX & GY) sur les pixels.

🔎 Artefacts visibles :
Des lignes noires apparaissent entre les segments reconstruits — c’est normal.
Chaque Worker traite les segments indépendamment, les bords ne peuvent pas calculer le gradient complet.

📂 Structure du Projet
src/main/java/common   → Interfaces RMI + DTOs
src/main/java/server   → Serveur, RMI Registry, CORBA
src/main/java/worker   → Workers + algorithme Sobel
src/main/java/client   → Dashboard Swing + JMS + CORBA Client
src/main/resources     → Images de test
src/main/idl           → Interfaces IDL CORBA

👥 Auteurs

Hsan KHECHAREM

Mouez JEDIDI

Faculté des Sciences de Sfax
