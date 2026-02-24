  # Photo Booth IA avec Ajustement Dynamique - MDM 2026
Photo booth intelligent avec contrôle motorisé de la hauteur par gestes, génération d'images par IA utilisant Stable Diffusion XL et détection de gestes en temps réel.

  ## Description
Ce projet crée un photobooth interactif et automatisé qui :

  - Ajuste la hauteur physique de la caméra via un vérin électrique (3 positions : Bas, Milieu, Haut).

  - Capture des photos via webcam avec détection de gestes précise (comptage de doigts).

  - Génère des images stylisées "Comic Book Ligne Claire" via SDXL + ControlNet.

  - Imprime les résultats au format A6 glacé.

  ### Style graphique
- **Bande dessinée européenne** (ligne claire)
- Traits nets et épurés, aplats de couleurs avec dégradés
- Scènes futuristes avec interfaces holographiques cyan/orange
- Arrière-plans technologiques complexes

  ## Prérequis matériel

|   Composant    |                 Spécification                      |
|----------------|----------------------------------------------------|
| **Vérin**      | Vérin électrique linéaire (1 DDL)                  |
| **GPU**        | NVIDIA avec CUDA (RTX 2060+ recommandé)            |
| **Webcam**     | Résolution 720p minimum                            |
| **Écran**      | 3840×1080 (dual monitor) recommandé                |
| **Imprimante** | HP Color LaserJet 5700 + papier A6 glacé 200g      |
| **RAM**        | 16 GB minimum (32 GB recommandé pour SDXL)         |



                          COUCHE MATERIELLE
                          -----------------
        Webcam USB          GPU NVIDIA          Vérin + Contrôleur
        1280x720            CUDA/cuDNN          Positionnement 1 DDL
            |                   |                   |
            +-------------------+-------------------+
                                |
                    SYSTEME D'EXPLOITATION (Linux/Windows)
          Drivers: V4L2 (webcam), CUPS (imprimante), GPIO/Serial (Vérin)
                    

  ## APPLICATION PHOTOBOOTH (photobooth.py)

  ### MODULE 1: GESTION DU VÉRIN (NOUVEAU)

Le système utilise le comptage de doigts pour définir la hauteur physique avant la capture.

 Position	    |      Geste Signe	      |            Angle de vue
              |                         |
**BAS**.      |    1 Doigt levé		      |    Contre-plongée (Low Angle)
**MILIEU**	  |    2 Doigts levés  	    |    Hauteur humaine (Eye Level)
**HAUT**	    |    3 Doigts levés	      |    Plongée (High Angle)


  ### MODULE 2: MACHINE A ETATS (Mise à jour)

Plaintext
[STATE: SETUP_HEIGHT] --(1, 2 ou 3 doigts)--> [ACTION: MOVE_ACTUATOR]
       ^                                              |
       |                                              v
[STATE: WAITING_CAPTURE] <------------------ [POSITION_REACHED]
       |
       +--(2 doigts 2s)--> [COUNTDOWN] --(Capture)--> [PROCESSING_IA]
       |
       +--(poing fermé 2s)--> [RETOUR SETUP_HEIGHT]

[STATE: READY_PRINT]
       |
       +--(pouce 2s)--> [PRINTING]
       +--(V-sign 2s)--> [RESET TO SETUP_HEIGHT]


  ## TIMELINE D'UNE SESSION

t=0s      MENU 1 : Choix de la hauteur. Utilisateur lève 3 doigts (🖖).

t=1s      Détection stable -> Activation du vérin vers position HAUTE.
          Affichage : "Déplacement caméra en cours..."

t=5-10s   Caméra stabilisée en hauteur. Passage au MENU 2.
          Affichage : Retour vidéo Live (Vue Plongée).

t=10s     Utilisateur fait signe V (✌️) pendant 2s.

t=12s     Validation --> Countdown "3-2-1" -> Capture Photo.

t=15s     Envoi vers Stable Diffusion XL.

t=45s     Réception Image IA.
          Options : Pouce (👍) pour imprimer ou Poing (✊) pour changer de hauteur.

                            [SI IMPRESSION]

t=47s    Envoi CUPS: input.png + IA1.png + IA2.png

t=50-60s  Impression physique (~8s par page A6)

t=60s     Etat: "waiting_victory" (pret nouvelle session)



 ## GUIDE DES SIGNES 

Menu 1 : Réglage de la Hauteur (Vérin)

Position Basse :  ☝  (1 doigt)

Position Milieu :  ✌  (2 doigts)

Position Haute :  🖖  (3 doigts)

Menu 2 : Capture & Navigation

Prendre Photo :  ✌  (2 doigts maintenus)

Retour au menu :  ✊  (Poing fermé)

Impression :  👍  (Pouce levé)


  ### NOTE TECHNIQUE : Comptage de doigts

  L'algorithme de détection a été mis à jour pour analyser les Hand Landmarks (21 points). Un doigt est compté comme "levé" si l'ordonnée y de l'extrémité du doigt (Landmark 8, 12, 16, 20) est inférieure à celle de l'articulation précédente.

  ## Dépendances Additionnelles
RPI.GPIO ou pyserial (selon le contrôleur du vérin) ✅

MediaPipe Hand Landmarker (pour le comptage précis des doigts) ✅
