# 2627_ESE_PROJET_LABIB 
- Projet élaboré par : Jaouadi Roua
# 🤖 Autonomous Garbage Collector Robot

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: In Progress](https://img.shields.io/badge/Status-PCB%20Design%20Phase-orange.svg)](#-objectif-actuel--conception-du-pcb-sur-kicad)
[![KiCad](https://img.shields.io/badge/EDA-KiCad%20v8.0-blue.svg)](https://www.kicad.org/)
[![MCU: STM32G4](https://img.shields.io/badge/MCU-STM32G431RBT6-red.svg)](https://www.st.com/)

Projet d'ingénierie embarquée visant à concevoir de zéro un robot mobile autonome capable de repérer, trier et collecter des déchets (canettes métalliques rouges et vertes) sur une maquette bordée.

---

## 📌 Présentation du Projet

Le robot évolue sur une maquette fermée par des bordures de sécurité, comportant 4 zones distinctes :
1. **Zone Canettes Rouges :** Zone initiale où sont disposées les canettes rouges.
2. **Zone Canettes Vertes :** Zone initiale où sont disposées les canettes vertes.
3. **Zone de Dépôt Rouge :** Zone de stockage des canettes rouges collectées.
4. **Zone de Dépôt Verte :** Zone de stockage des canettes vertes collectées.

---

## 🎯 Objectifs & Missions (Cahier des Charges)

Le développement suit une approche modulaire validée par niveaux progressifs :

- **Niveau 0 (Sécurité & Déplacement) :** 
  - Déplacement autonome avec propulsion différentielle.
  - Sécurité mécanique garantie par la bordure périphérique de la table.
- **Niveau 1 (Détection & Tri) :**
  - Repérage et approche des canettes par différence de profilométrie laser.
  - Identification de la couleur (Rouge vs Verte) via le capteur optique.
- **Niveau 2 (Collecte & Environnement Multi-Robot) :**
  - Transport et dépôt dans la zone dédiée selon la couleur.
  - Navigation réactive et évitement des obstacles fixe/robots sur le terrain.
- **Niveau 3 (Autonomie & Localisation) :**
  - Insensibilité aux perturbations extérieures.
  - Auto-localisation spatiale sur la maquette ($X, Y, \theta$) combinant odométrie et centrale inertielle.

---

## 🏗️ Architecture Système Retenue

### 1. Bloc Traitement & Contrôle Central (Intégré sur PCB)
* **Microcontrôleur (IC) :** **STM32G431RBT6** (ARM Cortex-M4 @ 170 MHz, LQFP-64).
* **Rôle :** Exécution de la Machine à États (FSM), calcul de l'odométrie, gestion dynamique des adresses I2C (`XSHUT`), asservissement des moteurs et décodage des encodeurs quadrature.

### 2. Bloc Perception & Environnement (4x ToF Fixes + APDS + MPU)
* **Télémétrie Laser Multi-ToF (4x VL53L0X fixes) :** 
  - **Avant-Centre Haut :** Détection d'obstacles hauts ou d'autres robots.
  - **Avant-Centre Bas :** Détection dédiée des canettes au sol (permet la différenciation obstacle vs canette par corrélation verticale).
  - **Arrière-Droit & Arrière-Gauche :** Surveillance des angles morts et évitement lors des manœuvres de recul.
* **Capteur Couleur & Proximité (APDS-9960) :** Connecté via en-tête au fond de la pince pour la reconnaissance RGBC de la canette.
* **Centrale Inertielle (MPU6050) :** Gyroscope/Accéléromètre I2C pour la correction de dérive et la détection de chocs inter-robots.

### 3. Bloc Actionneurs & Manipulation
* **Propulsion :** 2x Moteurs DC **CH-N20-3** (avec encodeurs intégrés), commandés par le driver **DRV8833PWPR** (TSSOP-16) directement implanté sur le PCB.
* **Préhension :** Pince mono-servo motorisée (1x **MG90S**) pour le serrage/relâchement des canettes.

### 4. Bloc Gestion d'Énergie 1S (Li-Ion & Power Path)
* **Source d'Énergie :** 1x Accumulateur Li-Ion 1S ($3.7\text{V}$ nominal).
* **Gestionnaire de Charge (IC) :** **Texas Instruments BQ25896RTWR** (WQFN-24) :
  - Management de charge rapide via USB Type-C.
  - Gestion automatique du chemin de puissance (*NVDC Power-Path*).
* **Étage de Régulation PCB :**
  - Convertisseur **Boost Step-Up 5V** pour la ligne de puissance des moteurs et du servomoteur.
  - Régulateur **LDO 3.3V** pour l'alimentation du STM32G431RBT6 et de la chaîne de capteurs I2C.
---
## 🚀 Objectif Actuel : Conception du PCB sous KiCad v10

Nous sommes actuellement dans la phase de **saisie schématique et de routage du PCB sur-mesure** intégrant l'ensemble des puces CMS (`STM32G431RBT6`, `BQ25896RTWR`, `DRV8833PWPR`). 
