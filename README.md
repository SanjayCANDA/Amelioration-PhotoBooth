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


  ## Prérequis pour l'utilisation et l'application de se projet

  ###  Matériel

|   Composant    |                 Spécification                      |
|----------------|----------------------------------------------------|
| **Vérin**      | Vérin électrique linéaire (1 DDL)                  |
| **GPU**        | NVIDIA avec CUDA (RTX 2060+ recommandé)            |
| **Webcam**     | Résolution 720p minimum                            |
| **Écran**      | 3840×1080 (dual monitor) recommandé                |
| **Imprimante** | HP Color LaserJet 5700 + papier A6 glacé 200g      |
| **RAM**        | 16 GB minimum (32 GB recommandé pour SDXL)         |


  ### Calibration du Vérin

Avant le premier lancement, il faut définir les "Setpoints" dans votre script :

Position Basse : X cm ou Y pas moteur (Contre-plongée).

Position Milieu : X′cm (Hauteur d'œil).

Position Haute : X′′cm (Plongée).

+ Alimentation : Bloc 12V/24V externe (les ports USB/GPIO ne suffisent pas à alimenter un vérin).


  ### Python 3.10 (OBLIGATOIRE pour WebUi)

Stable Diffusion WebUI nécessite **Python 3.10.x** (pas 3.11, 3.12 ou supérieur) [web:1][web:8].

#### Installer Python 3.10 :
Sur Ubuntu/Debian

```bash
sudo apt update
sudo apt install python3.10 python3.10-venv python3.10-dev
```

  ## STORY BORD



  ## TIMELINE D'UNE SESSION

t=0s      MENU 1 : Choix de la hauteur. Utilisateur lève 3 doigts (🖖).

t=4s      Détection stable -> Activation du vérin vers position HAUTE.
          Affichage : "Déplacement caméra en cours..."

t=4-10s   Caméra stabilisée en hauteur. Passage au MENU 2.
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


##  ARCHITECTURE SIMPLIFIEE

<pre>
[Signe Main]
            |
            v
        [Webcam] <-----------+ 
            |                |
         (frame)             | [Ajustement Physique]
            |                | (Position 1, 2 ou 3)
            v                |
     [photobooth.py] --------+----[Vérin Électrique]
            |          (Signal GPIO/USB)
            |
            | HTTP POST (Base64)
            v
      [Automatic1111]
            | Port 7860
            | SDXL + ControlNet
            | GPU: 9-12 GB VRAM
            v
     [photobooth.py]
            |
      +-----+-----+
      |           |
      v           v
   [Écran]   [Imprimante]
</pre>

================================================================================                    

 ## GUIDE DES SIGNES 

### Menu 1 : Réglage de la Hauteur (Vérin)

Position Basse :  ☝
<pre>
          # 1 doigt
        __
       /  |
      |   |
      |   |
      |   |
      |    \_______  
      |             \
      /  /   /   /   \ 
     |  |   |   |  |  \
     |  |   |   |  |  |
 ____|  |   |   |  |  |
/  __ \  \__/\__/\_/  /
 /  |__|             /
 |                  /
  \___             /
      \           |   
       |          |
</pre>

Position Milieu :  ✌  
<pre>
          # 2 doigts
        __       __ 
       /  |     /  /
      |   |    /  /
      |   |   /  /
      |   |  /  / 
      |    \/  /____  
      |              \
      |      /   /  / \ 
      |      |   |  |   \
      |      |   |  |   |
 ____ |      |   |  |   |
/  __ \      \_/ \_/   /
\/  |__|             /
 |                  /
  \___             /
      \           |   
       |          |
</pre>


Position Haute :  🖖
<pre>
              # 3 doigts
             _        __ 
           /  |     /  /
          |   |    /  /
          |   |   /  /
          |   |  /  / 
          |    \/  /____  
   _      |              \
 /  |     \      /   /  / \ 
|   |      |    |   |  |  |
|    \     |    |   |  |  |
 \    \___/     |   |  |  |
  \              \_/ \_/  /
    \                     /
     \                  /
      \__              /
          \           |   
           |          |
</pre>

#### Résumé des positions :
<pre>
POSITION BASSE          POSITION MILIEU           POSITION HAUTE
   (1 DOIGT)                (2 DOIGTS)               (3 DOIGTS)
      ☝️                       ✌️                       🖖
   [Vérin 10%]             [Vérin 50%]              [Vérin 90%]
       +                         +                        +
       |                         |                        | 
   .---'---.                     |                        |
                             .---'---.                    |                      
                                                      .---'---.
</pre>

### Menu 2 : Capture & Navigation

Prendre Photo :  ✌
<pre>
          # 2 doigts
        __       __ 
       /  |     /  /
      |   |    /  /
      |   |   /  /
      |   |  /  / 
      |    \/  /____  
      |              \
      /      /   /  / \ 
     |      |   |  |   \
     |      |   |  |   | 
 ____|      |   |  |   |
/  __ \      \_/ \_/   /
 /  |__|             /
 |                  /
  \___             /
      \           |   
       |          |
</pre>

Retour au menu :  ✊ 
<pre>
        # Poing fermé
        __  __ __ __
      /              \
     /    /   /   /  /\ 
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

Impression :  👍
<pre>
        # Pouce levé
                  __
                /   |
               /   /
              /   /
      ______-'    \______
                  \______\
                  \_______\
      ________     \______\
              \____ \____/                          
</pre>


  ### NOTE TECHNIQUE : Comptage de doigts

  L'algorithme de détection a été mis à jour pour analyser les Hand Landmarks (21 points). Un doigt est compté comme "levé" si l'ordonnée y de l'extrémité du doigt (Landmark 8, 12, 16, 20) est inférieure à celle de l'articulation précédente.

================================================================================

  ## APPLICATION PHOTOBOOTH (photobooth.py)

  ### MODULE 1:     GESTION DU VÉRIN

Le système utilise le comptage de doigts pour définir la hauteur physique avant la capture.
<pre>
| Position    |      Geste Signe	    |            Angle de vue       |
|-------------|-------------------------|-------------------------------|
|**BAS**      |    1 Doigt levé		    |    Contre-plongée (Low Angle) |
|**MILIEU**	  |    2 Doigts levés  	    |    Hauteur humaine (Eye Level)|
|**HAUT**	  |    3 Doigts levés	    |    Plongée (High Angle)       |
</pre>

  ### MODULE 2:    MACHINE A ETATS

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
      

  ### MODULE 3:     PREPARATION IMAGE
<pre>
Frame capturee (numpy array BGR 1280x720)
    |
    v
Redimensionnement --> Encodage Base64 --> Sauvegarde _input.png + Logo CPE
                                                |
                                                v
                                    HTTP POST Request (JSON)
</pre>


  ### MODULE 4:    POST-TRAITEMENT
<pre>
Decodage Base64 --> Application Logo CPE --> Sauvegarde
    (PNG)           (Overlay RGBA)           result_API_1111/
                                             timestamp_IA1.png
                                             timestamp_IA2.png
</pre>


  ### MODULE 5:    AFFICHAGE (OpenCV)

Ecran 3840x1080 (Dual Monitor)

Fenetre 1: "Webcam" (1440x810)
- Flux live 30 FPS
- Overlay gestes (cercles + barres progression)
- Messages etat systeme

Fenetre 2: "Image StableDiffusion" (1440x1620)
- Affiche derniere image IA
- Mise a jour apres generation


  ### MODULE 6:     IMPRESSION

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

###               GESTION DE L'ACTIONNEUR (VÉRIN LINEAIRE)

                       CONTROLE PHYSIQUE : VÉRIN 1 DDL


Interface: GPIO (Raspberry Pi) ou Serial (Arduino/USB)
Protocole: PWM ou High/Low Signal

LOGIQUE DE POSITIONNEMENT:
- Position 1 (BAS)    : Signal PWM 10% ou Relais 1 ON (Contre-plongée)
- Position 2 (MILIEU) : Signal PWM 50% ou Relais 2 ON (Angle Normal)
- Position 3 (HAUT)   : Signal PWM 90% ou Relais 1+2 ON (Plongée)

SECURITÉ:
- Fin de course (Limit Switches) intégrés au vérin.
- Timeout de mouvement : 8 secondes max.

###               STABLE DIFFUSION WEBUI (Automatic1111)

                STABLE DIFFUSION WEBUI (Automatic1111)
                    Port: 127.0.0.1:7860 - API REST


Endpoint: POST /sdapi/v1/img2img

Payload JSON:
{
  "init_images": ["base64_image"],
  "prompt": "comic book illustration, ligne claire style, clean lines...",
  "negative_prompt": "photorealistic, blurry, messy, low quality...",
  "denoising_strength": 0.62,
  "steps": 28,
  "cfg_scale": 7.5,
  "sampler_name": "DPM++ 2M Karras",
  "width": 1280, "height": 720,
  "batch_size": 1,
  "n_iter": 2,
  "controlnet_units": [{
    "model": "kohya_controllllite_xl_openpose_anime",
    "module": "openpose_full",
    "weight": 0.95,
    "guidance_start": 0.0,
    "guidance_end": 1.0
  }]
}

PIPELINE DE GENERATION (Post-Ajustement Vérin)
----------------------------------------------
1. Cadrage Physique  --> Défini par le vérin (Bas/Milieu/Haut)
2. ControlNet        --> Détecte le squelette selon l'angle choisi
3. SDXL UNet         --> Génère l'image 1280x720 (28 steps)
4. VAE Decoder       --> Conversion Latent vers Image finale

MODELES CHARGÉS EN VRAM GPU:
- sd_xl_base_1.0.safetensors (6.9 GB)
- kohya_controllllite_xl_openpose_anime (1.5 GB)
- VAE SDXL (335 MB)
TOTAL: ~9-12 GB VRAM

Response JSON:
{
  "images": [
    "iVBORw0KGgoAAAANS...",  // Image IA #1 (Vue selon position vérin)
    "9j/4AAQSkZJRgABA...",   // Image IA #2 (Vue selon position vérin)
    ...
  ]
}
  ### Remarque :
 Le fait d'avoir le vérin avant l'envoi à Stable Diffusion est crucial. Comme l'angle de vue change (Plongée vs Contre-plongée), le module OpenPose va détecter des coordonnées de squelette différentes. SDXL adaptera donc la perspective de l'illustration "Comic Book" pour qu'elle corresponde parfaitement à la posture physique de l'utilisateur.


  ### FLUX DE DONNEES

<pre>
Webcam --> photobooth.py --> Automatic1111 --> photobooth.py
   |            |                  |                  |
   |            |                  |                  |
Frame BGR   JSON+Base64      Generation IA      Decode+Logo
1280x720    POST /img2img    ~20-30 sec         Sauvegarde
                             9-12 GB VRAM
                                                Display + Print
</pre>


  ### COMMUNICATION INTER-PROCESSUS

<pre>
TERMINAL 1                          TERMINAL 2
bash launch_webui.sh                python photobooth.py

+------------------------+          +------------------------+
| Stable Diffusion WebUI |   HTTP   | Photo Booth Client     |
| Flask Server           | <------> | requests.post()        |
| Port 7860              |   REST   | Timeout: 180s          |
+------------------------+          +------------------------+
         |                                   |
         v                                   v
  PyTorch + CUDA                      OpenCV + MediaPipe
  GPU 0                               CPU threads

Process independant                 Process principal
Python 3.10 (venv WebUI)            Python 3.10 (venv photobooth)
Memoire: ~15 GB (modeles)           Memoire: ~500 MB
VRAM: 9-12 GB                       VRAM: 0 GB
</pre>


## DEPENDANCES CLES

<pre>
photobooth.py                    Automatic1111 WebUI
+-- opencv-python 4.11.0.86      +-- torch 2.5.1+cu121
+-- mediapipe 0.10.21            +-- diffusers 0.31.0
+-- numpy 1.26.2                 +-- transformers 4.30.2
+-- requests 2.32.5              +-- xformers 0.0.23.post1
+-- Python 3.10.x                +-- accelerate 0.21.0
                                 +-- safetensors 0.4.2
                                 +-- controlnet_aux 0.0.10
                                 +-- onnxruntime-gpu 1.17.1

Partagés (système):
+-- CUDA Toolkit 12.1
+-- cuDNN 9.1
+-- NVIDIA Driver 525+
</pre>

================================================================================

  ## Code & Logique logicielle

Le script photobooth.py est basé sur une Machine à États (Finite State Machine) qui synchronise la vision par ordinateur, l'IA générative et l'impression physique.

  ### Méthodes principales du projet
|         Méthode           |                                    Rôle                                             |
|---------------------------|-------------------------------------------------------------------------------------|
|**detect_gestures(frame)** |Utilise MediaPipe Hands pour extraire les coordonnées (Landmarks)                    |
|                           |et identifier les formes (Signe V ou Pouce).                                         |
|**check_stable_gesture()** |Gère la persistance temporelle. Empêche les faux positifs en vérifiant que le geste  |
|                           |est maintenu durant GESTURE_HOLD_DURATION.                                           |
|**call_api_images()**      |"Prépare le payload JSON (Prompt, ControlNet, Image Base64)                          |
|                           |et communique avec le serveur Stable Diffusion XL."                                  |
|**apply_logo_overlay()**   |Fusionne le template PNG transparent (Logo CPE)                                      |
|                           |sur les images capturées et générées.                                                |
|**countdown_flash_live()** |Gère l'animation visuelle (overlay bleu et texte 3-2-1)                              |
|                           |sur le flux webcam avant la capture.                                                 |
|**print_images()**         |Automatise l'envoi des fichiers vers le CUPS                                         |
|                           |pour l'impression physique.                                                          |

  ### Contrôle du Vérin (Actionneur)
Pour intégrer le vérin motorisé à l'architecture, il faut ajouter un module de communication (généralement Série/USB ou GPIO) et définir trois méthodes de contrôle spécifiques.

#### Méthodes à implémenter :

```Python
def move_actuator(position):
    """
    Envoie l'ordre physique au vérin via Serial ou GPIO.
    Positions : 1 (Bas), 2 (Milieu), 3 (Haut)
    """
    if position == 1:
        # Code pour rétracter le vérin (Contre-plongée)
        send_serial_command("MOVE_POS_1") 
    elif position == 2:
        # Code pour position médiane (Vue normale)
        send_serial_command("MOVE_POS_2")
    elif position == 3:
        # Code pour extension maximale (Plongée)
        send_serial_command("MOVE_POS_3")

def detect_finger_count(hand_landmarks):
    """
    Analyse les points MediaPipe pour compter précisément les doigts levés.
    Retourne un entier (1, 2 ou 3).
    """
    # Comparaison de l'extrémité des doigts (8, 12, 16) 
    # par rapport aux articulations (6, 10, 14)
    fingers = []
    # Logique de calcul y_tip < y_joint
    return sum(fingers)

def wait_for_actuator_stability():
    """
    Bloque le flux caméra ou affiche une icône de chargement 
    pendant que le vérin est en mouvement pour éviter le flou de bougé.
    """
    time.sleep(ACTUATOR_TRAVEL_TIME) # Ou lecture du capteur de fin de course
```

  Intégration dans la boucle principale (main)

Le code passera par un nouvel état initial SETUP_HEIGHT :
- Phase de Scan : La méthode detect_finger_count identifie si l'utilisateur lève 1, 2 ou 3 doigts.
- Phase d'Action : move_actuator est appelée pour déplacer la webcam.
- Phase de Transition : Une fois le vérin stable, le système bascule vers l'état waiting_victory pour lancer la session    photo.



## Problème éventuel du projet 

### Problèmes Mécaniques et Physiques

#### Problèmes de Sécurité (Hardware)

**Échauffement du Contrôleur** : Un vérin consomme beaucoup de courant lors du démarrage. Si le driver (L298N ou relais) n'est pas bien refroidi, il peut griller.

**Points de Pincement** : Le vérin crée une zone de cisaillement. Il faut s'assurer qu'aucun utilisateur (surtout des enfants) ne puisse mettre ses doigts dans le mécanisme pendant le réglage.

**Absence de Capteurs de Fin de Course** : Si le code envoie l'ordre de descendre alors que le vérin est déjà en bas, le moteur peut forcer, chauffer et réduire sa durée de vie.

#### Problèmes d'Interface et d'UX (Expérience Utilisateur)

**Latence de Réaction** : Un vérin est lent (souvent quelques centimètres par seconde). L'utilisateur pourrait croire que le système ne fonctionne pas s'il n'y a pas de retour visuel immédiat ("Mouvement en cours...") sur l'écran.

**Conflits de Gestes** : * Faire 2 doigts pour choisir la "Hauteur Milieu" déclenche-t-il immédiatement la photo ?

Il faut une séparation stricte entre le Menu Réglage et le Menu Capture pour éviter que la caméra ne bouge pendant que l'utilisateur pose.

**Sortie de Cadre** : En changeant de hauteur, l'utilisateur peut se retrouver hors-champ (trop haut ou trop bas). Le système doit alors l'inviter à reculer ou s'avancer.

### Problèmes Logiciels (Code)

**Blocage du Thread (Freezing)** : Si la fonction move_actuator() est "bloquante" (attend que le vérin finisse), l'affichage de la webcam va se figer. Il faut utiliser des threads séparés ou une approche asynchrone.

**Bruit dans le comptage de doigts** : MediaPipe peut hésiter entre 2 et 3 doigts si la main tremble. Cela pourrait faire "pomper" le vérin (monter/descendre sans arrêt). Il faut un système de verrouillage (une fois la position choisie, on ne peut plus en changer sans un geste de reset).



## Amélioration de l'IA et du Rendu (Software)

**Multi-ControlNet** : Actuellement, vous utilisez OpenPose. Ajouter Canny ou Depth en parallèle permettrait de conserver non seulement la pose de l'utilisateur, mais aussi la structure précise des objets qu'il tient (ex: un accessoire de photobooth) ou les détails de l'arrière-plan.

**Inpainting Automatique** : Utiliser l'IA pour corriger uniquement les visages ou les mains après la génération (souvent les points faibles de SDXL), afin de garantir un résultat esthétique parfait à chaque impression.

**Styles Dynamiques** : Permettre à l'utilisateur de choisir son univers via un geste (ex: 4 doigts pour un style "Cyberpunk", 5 doigts pour "Peinture à l'huile") au lieu de rester figé sur la "Ligne Claire".

### Évolution Mécanique et Hardware

**Asservissement par le Regard (Auto-Framing)** : Au lieu de positions fixes (Haut/Milieu/Bas), le vérin pourrait s'ajuster dynamiquement pour que le visage de l'utilisateur soit toujours parfaitement centré au milieu de l'image (via les coordonnées du nez détectées par MediaPipe).

**Ajout d'un 2ème Axe (Pan/Tilt)** : Rajouter un moteur de rotation horizontale pour que la caméra puisse suivre l'utilisateur s'il se déplace latéralement dans la pièce.

Éclairage Adaptatif (Ring Light Smart) : Connecter une bague LED dont l'intensité et la température de couleur (chaud/froid) changent en fonction du style IA choisi ou de la luminosité ambiante détectée.

### Interaction et Expérience Utilisateur (UX)

**Réalité Augmentée "Ghost"** : Afficher sur l'écran un contour transparent (pose de référence) que l'utilisateur doit essayer d'imiter pour obtenir la meilleure génération IA possible.

**Envoi Cloud / QR Code** : Au lieu de simplement imprimer, générer un QR Code unique sur l'écran à la fin du processus pour que l'utilisateur puisse télécharger sa photo directement sur son smartphone.

Audio-Réactivité : Ajouter des instructions vocales synthétisées ("Levez deux doigts pour monter la caméra") ou des sons déclenchés par les mouvements du vérin pour rendre la machine plus "vivante".

### Robustesse et Déploiement

**Mode "Kiosque" Sécurisé** : Verrouiller le système d'exploitation pour que l'utilisateur ne puisse pas fermer la fenêtre OpenCV ou accéder au bureau Windows/Linux.

**Gestion de File d'Attente** : Si beaucoup de gens utilisent le photobooth, implémenter un système qui traite les images en arrière-plan pendant que la personne suivante se place, afin d'optimiser le débit du GPU.



