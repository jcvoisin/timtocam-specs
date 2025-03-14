# Spécification d'interface TIMTOCAM : messages échangés entre le plot central (TIM) et le logiciel entraîneur (CAM) 
Spécification d'interface des communications entre le *Plot Central, appareil périphérique*, ci-après désigné par le terme "**TIM**" et  le *Logiciel Entraîneur, application Android/iOS ou simulateur PC, appareil central*, ci-après désigné par le terme "**CAM**".
Spécification rédigée dans le cadre du projet de BTS **ALM Fit Track** de la STS CIEL du LPO Queneau à Yvetot.

## 1. Historique de révision

| Version | Statut
| :- | :- | :- 
| 0.1 | proposition initiale (issue en partie du fichier https://chat.cybermatics.xyz/user_uploads/2/fa/vO1C3mFuj3QQxv6xTRZyMLRz/Bluetooth-Config.docx)<br>Seul le séquencement nominal (tous les joueurs effectuent l'ensemble de leurs exercices) est décrit.

## 2. Travaux en cours

- Mettre au point les cas dégradés/cas d'erreur
- Mettre au point le séquencement nominal, en particulier traiter les éléments identifier comme "optionnels"
- Définir la version de BLE utilisée, coté TIM comme coté CAM
- Définir, si nécessaire, les timings/delais minimum et/ou maximum dans les séquencements

## 3. Protocole, rôles

Le protocole utilisé pour la communication est le BLE (TODO : 
Spécification d'interface des communications entre le *Plot Central, appareil périphérique*, ci-après désigné par le terme "**TIM**" et  le *Logiciel Entraîneur, application Android/iOS ou simulateur PC, appareil central*, ci-après désigné par le terme "**CAM**".

## 4. Séquencement des communications

### 1. Cas nominal

Avant démarrage de la séquence, CAM et TIM sont actifs/sous-tension.

  1. Après après sur B1 de la carte Nucléo, TIM diffuse des annonces.
  2. CAM lance un cycle de découverte pour identifier un appareil périphérique nommé "Plot_Central" et son adresse BLE (optionnel ?)
  3. CAM se connecte à l'adresse BLE de TIM
  4. CAM liste les caractéristiques disponibles (optionnel ?)
  5. CAM s'abonne à la caractéristique nommée "TX"
  6. CAM écrit une commande début d'exercice CAM_START, contenant la description de l'exercice
  7. *Le premier exercice est effectué par le premier joueur*
  8. TIM envoie les résultats TIM_TIMINGS du premier joueur
  9. *Le nième exercice est effectué par le nième joueur*
  10. TIM envoie les résultats TIM_TIMINGS du nième joueur
   11. *Lorsque les résultats du dernier joueur sont envoyés, la séquence prend fin*

## 5. Message `CAM_START`, envoyé par le logiciel entraîneur vers le plot central
Chaque fois qu'une nouvelle série d'exercices doit démarrer, CAM écrit sur la caractéristique "TX" de TIM

| Statut/Commande | Nombre **n** de joueurs | Liste d'id de **m**  plots 
| :- | :- | :- 
| 8 bit | 8 bit | **m** × 8 bit 
| `0b0000-0001` | **n** 
| `0x01` | **n** 
| `CAM_EX_START` | entier non signé | entier non signés

Exemple de message pour 3 joueurs et la liste de plots 1, 3, 1, 2, 0 :
En hexadécimal : `01 03 01 03 01 02 00`
## 6. Message `TIM_TIMINGS`
Chaque fois qu'un joueur termine son exercice, CAM est **notifié** et lit sur la caractéristique "TX" un message listant les chronométrages du joueur suivant ce format : 

| Statut/Commande |  joueur n° **j** | Liste des chronos en millisecondes
| :- | :- | :- 
| 8 bit | 8 bit | **m** × 32 bit 
| `0b0001-0000` | **j** |
| `0x10` | **j** 
| `TIM_TIMINGS` | entier non signé | entiers non signé (ms)
