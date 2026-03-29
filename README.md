# nuclear-intervention-robot
Concept d’un robot conçu pour intervenir dans des environnements nucléaires dangereux.

# Robot d’intervention pour environnement nucléaire

## Description du projet

Ce projet consiste à réfléchir à la conception d’un robot capable d’intervenir dans des environnements dangereux, notamment dans des installations nucléaires où la présence humaine peut être limitée ou risquée.

L’objectif est d’imaginer un système robotisé capable d’effectuer des missions d’inspection ou d’intervention à distance.

## Objectifs du projet

- Concevoir un robot capable d’évoluer dans un environnement dangereux
- Réfléchir à un système de contrôle à distance
- Étudier les capteurs nécessaires pour l’inspection

## Technologies et concepts étudiés

- Robotique
- Systèmes embarqués
- Capteurs
- Conception de systèmes mécaniques
- Téléopération

## Ce que j’ai appris

Ce projet m’a permis de réfléchir à la conception d’un système technique complexe et aux contraintes liées aux environnements dangereux.

Il m’a également permis de développer ma réflexion sur l’architecture d’un robot et les différents composants nécessaires.

## Améliorations possibles
![645941654_922971880137632_2470153405135315248_n](https://github.com/user-attachments/assets/1bc63ee5-ef6f-4265-80f1-dff6bd66f401)
![641727722_951423054021563_6846845541596897254_n](https://github.com/user-attachments/assets/ab4b0770-f223-4a58-bae2-f983dce84d5f)
![645744378_922431464042985_8595417993237053104_n](https://github.com/user-attachments/assets/00a5b5be-c424-4fc6-86de-9b10530d5d93)
![645804686_35042294372024325_3850835828676110251_n](https://github.com/user-attachments/assets/a54ba286-7e98-4eb9-b6ea-9e2383f0c27e)
![641808798_969987059025861_608805101617165722_n](https://github.com/user-attachments/assets/87eafcca-de82-4ce6-bda0-3f56d524d24a)
![643400562_951261387373388_4450486194735171023_n](https://github.com/user-attachments/assets/1cbd5893-4805-4fe2-ab03-4dc2097d1ee3)
![640707190_1976416359918868_6727495907514684835_n](https://github.com/user-attachments/assets/4a3b375b-4dfb-4541-bfde-80dbd2bc132e)
![639313393_926199683143857_4303821593250896039_n](https://github.com/user-attachments/assets/36e46c05-e43c-400e-8ff0-1c15fa69563e)
![640810263_4223016591348124_2530516800261994008_n](https://github.com/user-attachments/assets/92f421fc-d490-461e-acca-af6bfb25522e)

- Ajouter des capteurs spécifiques
- Améliorer la mobilité du robot
- Développer un système de contrôle plus avancé


![69038290-843A-45C6-AE2E-E391C0AD46A0](https://github.com/user-attachments/assets/60f8e26f-25fe-4b76-aeca-883388aa6b2f)
![IMG_3450](https://github.com/user-attachments/assets/44c03bd8-0ec0-4171-b982-b545633e3e3e)
![IMG_2544](https://github.com/user-attachments/assets/ff667758-92c2-4eb9-9875-84e9557742a3)
![Capture d’écran   2025-05-27 à 05 57 23](https://github.com/user-attachments/assets/d7f76a41-7c85-46c7-96a0-f75242fe0903)
![IMG_3436](https://github.com/user-attachments/assets/fba53f4e-cef5-4d46-b21d-d6ed91c7b376)
![IMG_3361](https://github.com/user-attachments/assets/fb23514e-6111-44b6-8409-0be1d7df1a4f)
![IMG_3360](https://github.com/user-attachments/assets/99313a54-498d-46fd-a779-3ea6128b39ce)



## ⚙️ Fonctionnement du programme

Ce programme permet de contrôler six moteurs avec une gestion progressive de la vitesse et une simulation PWM.

## 🧩 Exemple de code (gestion des commandes)

```cpp
const int IN1 = 7;
const int IN2 = 6;
const int IN3 = 5;
const int IN4 = 4;

const unsigned long INTERVALLE_CHANGEMENT = 100;
const int INCREMENT_VITESSE = 15;
const int VITESSE_ROTATION = 150;

int vitesseUtilisateur = 125;
int vitesseActuelleGauche = 0;
int vitesseActuelleDroite = 0;
int vitesseCibleGauche = 0;
int vitesseCibleDroite = 0;
unsigned long dernierChangement = 0;
byte etatMoteur = 0;

unsigned long dernierPWMGauche = 0;
unsigned long dernierPWMDroite = 0;
const int CYCLE_PWM = 2000;
bool etatPWMGauche = false;
bool etatPWMDroite = false;

String commandeRecue = "";
bool nouveauCommandeRecue = false;

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  arreterMoteurs();
  Serial.begin(9600);
}

void loop() {
  unsigned long tempsActuel = millis();
  unsigned long microActuel = micros();
  
  if (Serial.available() > 0) {
    commandeRecue = Serial.readStringUntil('\n');
    commandeRecue.trim();
    nouveauCommandeRecue = true;
  }
  
  if (nouveauCommandeRecue) {
    traiterCommande(commandeRecue);
    nouveauCommandeRecue = false;
  }
  
  if (tempsActuel - dernierChangement >= INTERVALLE_CHANGEMENT) {
    dernierChangement = tempsActuel;
    ajusterVitesseProgressivement();
  }
  
  simulerPWMGauche(microActuel);
  simulerPWMDroite(microActuel);
}

void traiterCommande(String commande) {
  if (commande == "A") {
    etatMoteur = 1;
    vitesseCibleGauche = vitesseUtilisateur;
    vitesseCibleDroite = vitesseUtilisateur;
  }
  else if (commande == "R") {
    etatMoteur = 2;
    vitesseCibleGauche = vitesseUtilisateur;
    vitesseCibleDroite = vitesseUtilisateur;
  }
  else if (commande == "S") {
    vitesseCibleGauche = 0;
    vitesseCibleDroite = 0;
  }
  else if (commande == "G") {
    etatMoteur = 3;
    vitesseCibleGauche = VITESSE_ROTATION;
    vitesseCibleDroite = VITESSE_ROTATION;
  }
  else if (commande == "D") {
    etatMoteur = 4;
    vitesseCibleGauche = VITESSE_ROTATION;
    vitesseCibleDroite = VITESSE_ROTATION;
  }
  else if (commande.startsWith("V")) {
    if (commande == "V1") vitesseUtilisateur = 25;
    else if (commande == "V2") vitesseUtilisateur = 50;
    else if (commande == "V3") vitesseUtilisateur = 125;
    else if (commande == "V4") vitesseUtilisateur = 255;
    
    if (etatMoteur == 1 || etatMoteur == 2) {
      vitesseCibleGauche = vitesseUtilisateur;
      vitesseCibleDroite = vitesseUtilisateur;
    }
  }
}

void ajusterVitesseProgressivement() {
  if (vitesseActuelleGauche < vitesseCibleGauche) {
    vitesseActuelleGauche = min(vitesseActuelleGauche + INCREMENT_VITESSE, vitesseCibleGauche);
  } else if (vitesseActuelleGauche > vitesseCibleGauche) {
    vitesseActuelleGauche = max(vitesseActuelleGauche - INCREMENT_VITESSE, vitesseCibleGauche);
  }
  
  if (vitesseActuelleDroite < vitesseCibleDroite) {
    vitesseActuelleDroite = min(vitesseActuelleDroite + INCREMENT_VITESSE, vitesseCibleDroite);
  } else if (vitesseActuelleDroite > vitesseCibleDroite) {
    vitesseActuelleDroite = max(vitesseActuelleDroite - INCREMENT_VITESSE, vitesseCibleDroite);
  }
  
  if (vitesseActuelleGauche == 0 && vitesseActuelleDroite == 0 && vitesseCibleGauche == 0 && vitesseCibleDroite == 0) {
    if (etatMoteur != 0) {
      etatMoteur = 0;
      arreterMoteurs();
    }
  }
}

void simulerPWMGauche(unsigned long microActuel) {
  if (vitesseActuelleGauche == 0) {
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    return;
  }
  
  int tempsActif = map(vitesseActuelleGauche, 0, 255, 0, CYCLE_PWM);
  unsigned long tempsEcoule = microActuel - dernierPWMGauche;
  
  if (!etatPWMGauche && tempsEcoule >= (CYCLE_PWM - tempsActif)) {
    activerMoteurGauche();
    etatPWMGauche = true;
    dernierPWMGauche = microActuel;
  } 
  else if (etatPWMGauche && tempsEcoule >= tempsActif) {
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    etatPWMGauche = false;
    dernierPWMGauche = microActuel;
  }
}

void simulerPWMDroite(unsigned long microActuel) {
  if (vitesseActuelleDroite == 0) {
    digitalWrite(IN3, LOW);
    digitalWrite(IN4, LOW);
    return;
  }
  
  int tempsActif = map(vitesseActuelleDroite, 0, 255, 0, CYCLE_PWM);
  unsigned long tempsEcoule = microActuel - dernierPWMDroite;
  
  if (!etatPWMDroite && tempsEcoule >= (CYCLE_PWM - tempsActif)) {
    activerMoteurDroite();
    etatPWMDroite = true;
    dernierPWMDroite = microActuel;
  } 
  else if (etatPWMDroite && tempsEcoule >= tempsActif) {
    digitalWrite(IN3, LOW);
    digitalWrite(IN4, LOW);
    etatPWMDroite = false;
    dernierPWMDroite = microActuel;
  }
}

void activerMoteurGauche() {
  switch (etatMoteur) {
    case 1: case 4: digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW); break;
    case 2: case 3: digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH); break;
    default: digitalWrite(IN1, LOW); digitalWrite(IN2, LOW); break;
  }
}

void activerMoteurDroite() {
  switch (etatMoteur) {
    case 1: case 3: digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW); break;
    case 2: case 4: digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH); break;
    default: digitalWrite(IN3, LOW); digitalWrite(IN4, LOW); break;
  }
}

void arreterMoteurs() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

```
