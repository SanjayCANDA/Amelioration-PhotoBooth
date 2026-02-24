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

  ## PHOTO BOOTH IA - ARCHITECTURE SYSTEME

                          COUCHE MATERIELLE
                          -----------------
        Webcam USB          GPU NVIDIA          Vérin + Contrôleur
        1280x720            CUDA/cuDNN          Positionnement 1 DDL
            |                   |                   |
            +-------------------+-------------------+
                                |
                    SYSTEME D'EXPLOITATION (Linux/Windows)
          Drivers: V4L2 (webcam), CUPS (imprimante), GPIO/Serial (Vérin)
                    

 ## GUIDE DES SIGNES 

### Menu 1 : Réglage de la Hauteur (Vérin)

Position Basse :  ☝  (1 doigt)

Position Milieu :  ✌  (2 doigts)

Position Haute :  🖖  (3 doigts)

### Menu 2 : Capture & Navigation

Prendre Photo :  ✌  (2 doigts maintenus)

Retour au menu :  ✊  (Poing fermé)

Impression :  👍  (Pouce levé)



  ## APPLICATION PHOTOBOOTH (photobooth.py)

  ### MODULE 1: GESTION DU VÉRIN

Le système utilise le comptage de doigts pour définir la hauteur physique avant la capture.

 Position	    |      Geste Signe	      |            Angle de vue
              |                         |
**BAS**.      |    1 Doigt levé		      |    Contre-plongée (Low Angle)
**MILIEU**	  |    2 Doigts levés  	    |    Hauteur humaine (Eye Level)
**HAUT**	    |    3 Doigts levés	      |    Plongée (High Angle)


  ### MODULE 2: MACHINE A ETATS

<pre>
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
</pre>
      

  ### MODULE 3: PREPARATION IMAGE
<pre>
Frame capturee (numpy array BGR 1280x720)
    |
    v
Redimensionnement --> Encodage Base64 --> Sauvegarde _input.png + Logo CPE
                                                |
                                                v
                                    HTTP POST Request (JSON)
</pre>


  ### MODULE 4: POST-TRAITEMENT
<pre>
Decodage Base64 --> Application Logo CPE --> Sauvegarde
    (PNG)           (Overlay RGBA)           result_API_1111/
                                             timestamp_IA1.png
                                             timestamp_IA2.png
</pre>


  ### MODULE 5: AFFICHAGE (OpenCV)

Ecran 3840x1080 (Dual Monitor)

Fenetre 1: "Webcam" (1440x810)
- Flux live 30 FPS
- Overlay gestes (cercles + barres progression)
- Messages etat systeme

Fenetre 2: "Image StableDiffusion" (1440x1620)
- Affiche derniere image IA
- Mise a jour apres generation


  ### MODULE 6: IMPRESSION

Files d'impression:
1. timestamp_input.png  <---- Photo originale + logo
2. timestamp_IA1.png    <---- Variation IA #1 + logo
3. timestamp_IA2.png    <---- Variation IA #2 + logo

Commande CUPS:
lp -d HP_Color_LaserJet_5700_USB
   -o media=A6
   -o InputSlot=Tray2
   -o mediaType=HP-Brochure-Glossy-200g
   -o orientation-requested=4
   -o print-quality=5
   image.png

CUPS Daemon --> USB --> HP LaserJet --> Photos imprimees

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

 ---------------------------------------  SI IMPRESSION  --------------------------------------------

t=47s    Envoi CUPS: input.png + IA1.png + IA2.png

t=50-60s  Impression physique (~8s par page A6)

t=60s     Etat: "waiting_victory" (pret nouvelle session)



 ## GUIDE DES SIGNES 

Menu 1 : Réglage de la Hauteur (Vérin)

Position Basse :  ☝  -> 1 doigt
<pre>
        __  __ __ __
      /              \
     /    /   /   /   \ 
    |    |   |   |  |  \
    |    |   |   |  |  |
    |    |   |   |  |  |
  __ \__/\__/\__/\_/  /
 /  |__|             /
 |  /               /
  \___             /
      \           |   
       |          |
</pre>

Position Milieu :  ✌  (2 doigts)

Position Haute :  🖖  (3 doigts)

Menu 2 : Capture & Navigation

Prendre Photo :  ✌  (2 doigts maintenus)

Retour au menu :  ✊  (Poing fermé)

<pre>
        __  __ __ __
      /              \
     /    /   /   /   \ 
    |    |   |   |  |  \
    |    |   |   |  |  |
    |    |   |   |  |  |
  __ \__/\__/\__/\_/  /
 /  |__|             /
 |  /               /
  \___             /
      \           |   
       |          |
</pre>

Impression :  👍  (Pouce levé)


  ### NOTE TECHNIQUE : Comptage de doigts

  L'algorithme de détection a été mis à jour pour analyser les Hand Landmarks (21 points). Un doigt est compté comme "levé" si l'ordonnée y de l'extrémité du doigt (Landmark 8, 12, 16, 20) est inférieure à celle de l'articulation précédente.


  ## Dépendances Additionnelles
RPI.GPIO ou pyserial (selon le contrôleur du vérin) ✅
MediaPipe Hand Landmarker (pour le comptage précis des doigts) ✅
