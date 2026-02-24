Projet : Caméra Robotisée à Commande Gestuelle (Vérin 1 DDL)
  Description du Projet
Ce projet consiste en la création d'un système de prise de vue intelligent capable de s'ajuster physiquement en hauteur grâce à un vérin électrique (1 degré de liberté). L'utilisateur interagit avec le système sans contact, uniquement par la reconnaissance de gestes via une caméra de traitement d'image.

Le système propose trois angles de vue prédéfinis (Plongée, Normal, Contre-plongée) pour offrir des perspectives artistiques variées sans manipulation manuelle de l'équipement.

  But Global
Offrir une interface de capture photo mains-libres, intuitive et automatisée, permettant de passer d'une configuration de menu à un retour vidéo en temps réel, tout en ajustant la position physique de la caméra selon le choix de l'utilisateur.

  Détails des Composants
1. Système de Vision (IA)

Moteur de détection : Utilisation de MediaPipe Hands (ou OpenCV) pour le suivi des points de repère de la main (Landmarks).

Logique de comptage : Analyse de la position des extrémités des doigts par rapport aux articulations pour déterminer le nombre de doigts levés.

Filtrage : Temporisation de 0.5s pour valider un geste et éviter les déclenchements accidentels.

2. Système Mécanique (Le Vérin)

Actionneur : Vérin électrique linéaire piloté par un contrôleur (type L298N ou relais).

Positions pré-réglées :

BAS : Extension minimale (Contre-plongée).

MILIEU : Extension 50% (Hauteur humaine / Normal).

HAUT : Extension maximale (Plongée).

  Interface Utilisateur & Navigation
Le système fonctionne sur une machine à états avec deux menus distincts :

Étape 1 : Menu de Configuration (Positionnement)

L'écran affiche les instructions de réglage. Le retour caméra n'est pas encore plein écran.

Geste	Signe	Action du Vérin	Résultat Photo
1 Doigt	☝️	Descente en position BAS	Contre-plongée
2 Doigts	✌️	Arrêt à MI-HAUTEUR	Vue Normale
3 Doigts	🖖	Montée en position HAUTE	Vue en Plongée
Une fois le choix détecté, le vérin s'active. Dès que la position est atteinte, le système passe automatiquement au Menu 2.

Étape 2 : Menu de Capture (Prise de vue)

L'écran affiche le retour vidéo direct (Live View) de la caméra positionnée.

Geste	Signe	Action
2 Doigts	✌️	Prendre la Photo (Déclenchement après décompte 3s)
Poing Fermé	✊	Retour au Menu 1 (Changement de position)
🖐️ Guide des Signes (Unicode)
Voici les commandes visuelles reconnues par l'algorithme :

Sélection Position Basse / 1 doigt :
 |
 ☝ 

Sélection Position Milieu & Déclencheur / 2 doigts :
|  |
 ✌  

Sélection Position Haute / 3 doigts :
|  |  |
  🖖   

Retour au menu précédent / Poing fermé :
 [ ] 
  ✊  
  
  Installation & Lancement (Exemple Python)
Prérequis :

Python 3.x

opencv-python

mediapipe

Bibliothèque de contrôle GPIO (selon votre matériel : RPi.GPIO ou Serial pour Arduino).

Lancement :

Bash
python main_camera_control.py
