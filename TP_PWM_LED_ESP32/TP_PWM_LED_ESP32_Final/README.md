# TP - Controle de la luminosite d'une LED par PWM / ESP32

**Cours :** 420-PC6-AG, Programmation d'objets connectes - Institut Grasset  
**Environnement :** Arduino IDE, paquet **esp32 by Espressif Systems 3.x**  
**Presentation / remise :** 11 novembre 2026

## Contenu du dossier

- `Arduino/Controle_LED_ESP32/Controle_LED_ESP32.ino` : source Arduino/C++ du serveur HTTP et du PWM.
- `Arduino/Controle_LED_ESP32/config.example.h` : modele de configuration Wi-Fi sans identifiants reels.
- `Documentation/Rapport_Technique_ESP32_PWM.pdf` : rapport technique (4 pages).
- `Documentation/Rapport_Technique_ESP32_PWM.docx` : version modifiable du rapport.
- `Documentation/Schema_branchement_ESP32_LED.png` : schema electrique.
- `Photos/` : quatre photos originales du montage, de l'interface et de l'IDE.

## Brancher

1. Deconnecter l'USB avant manipulation.
2. `GPIO18 (D18)` -> resistance `220 ohms` -> anode (+) de la LED.
3. Cathode (-) de la LED -> GND de l'ESP32.
4. Rien d'autre n'est necessaire (pas d'OLED ou de potentiometre pour ce TP).

## Demarrer

1. Installer Arduino IDE et `esp32 by Espressif Systems` version **3.x**.
2. Ouvrir `Controle_LED_ESP32.ino` dans Arduino IDE (carte : `ESP32 Dev Module`).
3. Copier `config.example.h` vers **`config.h`** dans le meme dossier et renseigner le Wi-Fi.
4. Connecter l'ESP32 USB, choisir son port COM et televerser.
5. Ouvrir le **Moniteur serie a 115200 bauds** et relever l'adresse IP affichee.
6. Depuis un appareil sur le meme reseau, ouvrir `http://ADRESSE_IP/` (port 80).

> L'adresse vue sur les photos (`172.20.10.3`) correspond a une session de test; elle peut changer.

## Fonctionnement et calcul

- `ledcAttach(18, 5000, 8)` : PWM sur D18, frequence 5 kHz, resolution 8 bits (0 a 255).
- Duty (%) -> valeur PWM : `(pourcentage * 255 + 50) / 100` en division entiere.
- Tension moyenne **estimee** : `(pourcentage / 100) * 3.3 V`.
- `GET /` : page HTML responsive et statut courant.
- `GET /pwm?duty=N` : reglage `N` entier de 0 a 100, sinon HTTP 400 et etat conserve.
- `GET /plus`, `GET /moins` : variation +/- 10 points, bornes 0..100.
- Autres chemins : HTTP 404.
- Si le Wi-Fi tombe, le logiciel tente une reconnexion sans remettre le PWM a zero.

## Tests de demonstration

| Action | Resultat attendu |
|:--|:--|
| `/pwm?duty=0` | LED eteinte ; PWM 0 |
| `/pwm?duty=25` | PWM 64 ; environ 0,83 V |
| `/pwm?duty=50` | PWM 128 ; environ 1,65 V |
| `/pwm?duty=75` | PWM 191 ; environ 2,48 V |
| `/pwm?duty=100` | PWM 255 ; environ 3,30 V |
| `/plus` ou `/moins` | +/- 10 % sans depasser 0..100 |
| `/pwm?duty=150` | HTTP 400 sans changement de luminosite |
| `/inconnu` | HTTP 404 |

## Avant remise

- **Ne pas remettre `config.h`** : il contient un mot de passe Wi-Fi. Seul le modele `config.example.h` est fourni.
- Ajouter une **capture de la page web sur telephone**, demandee par le professeur. Les photos fournies comprennent une capture sur ordinateur, mais pas la capture sur telephone de ce nouveau TP.
- Verifier la **valeur reelle de la resistance** (220 ohms demandes) avant la presentation.
- Completer le nom de l'equipe sur le rapport et ajouter les captures manquantes si necessaire.

Le dossier fourni reconstruit proprement le programme a partir du code mis au point pendant la conversation. Il ne constitue pas un export binaire des fichiers du PC/ESP32.
