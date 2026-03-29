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



voici une parit du codage du robot (parti moteur)

/**
 * Contrôle d'un robot à deux moteurs via Arduino Mega
 * Module: Dual DC Motor Driver 4A V3 (sans broches ENA/ENB)
 * 
 * Commandes via Serial Monitor (9600 bauds) ou interface Raspberry Pi:
 * "A" - Avancer progressivement
 * "R" - Reculer progressivement
 * "S" - Arrêt progressif
 * "G" - Tourner à gauche
 * "D" - Tourner à droite
 * "V1" à "V4" - Définir vitesse utilisateur (25, 50, 125, 255)
 * 
 * Améliorations:
 * - Transitions fluides entre toutes les commandes
 * - Correction de la commande d'arrêt
 * - Optimisation des rotations
 * - Meilleure réactivité pour l'interface web
 */

// Définition des broches de contrôle moteur
const int IN1 = 7;  // Contrôle moteur gauche
const int IN2 = 6;  // Contrôle moteur gauche
const int IN3 = 5;  // Contrôle moteur droit
const int IN4 = 4;  // Contrôle moteur droit

// Constantes pour la vitesse et les temps
const unsigned long INTERVALLE_CHANGEMENT = 100;  // Temps entre ajustements (réduit pour plus de fluidité)
const int INCREMENT_VITESSE = 15;                 // Incrément de vitesse par étape (réduit pour plus de douceur)
const int VITESSE_ROTATION = 150;                 // Vitesse pour les rotations (augmentée pour plus d'efficacité)

// Variables pour la gestion des états et vitesses
int vitesseUtilisateur = 125;    // Vitesse par défaut
int vitesseActuelleGauche = 0;   // Vitesse actuelle moteur gauche
int vitesseActuelleDroite = 0;   // Vitesse actuelle moteur droit
int vitesseCibleGauche = 0;      // Vitesse cible moteur gauche
int vitesseCibleDroite = 0;      // Vitesse cible moteur droit
unsigned long dernierChangement = 0;  // Pour la gestion du temps
byte etatMoteur = 0;             // État actuel (0: arrêt, 1: avancer, 2: reculer, 3: gauche, 4: droite)

// Variables pour la simulation PWM
unsigned long dernierPWMGauche = 0;  // Dernier temps PWM moteur gauche
unsigned long dernierPWMDroite = 0;  // Dernier temps PWM moteur droit
const int CYCLE_PWM = 2000;         // Durée d'un cycle PWM en microsecondes (2ms = 500Hz)
bool etatPWMGauche = false;        // État actuel du cycle PWM moteur gauche
bool etatPWMDroite = false;        // État actuel du cycle PWM moteur droit

// Variables pour la lecture du port série
String commandeRecue = "";        // Stockage de la commande reçue
bool commandeTraitee = true;      // Indicateur de traitement de commande
boolean nouveauCommandeRecue = false;  // Indique si une nouvelle commande est disponible

void setup() {
  // Configuration des broches en sortie
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  // Arrêt des moteurs au démarrage
  arreterMoteurs();
  
  // Initialisation de la communication série
  Serial.begin(9600);
  Serial.println("Robot prêt à recevoir des commandes:");
  Serial.println("A: Avancer progressivement");
  Serial.println("R: Reculer progressivement");
  Serial.println("S: Arrêt progressif");
  Serial.println("G: Tourner à gauche");
  Serial.println("D: Tourner à droite");
  Serial.println("V1-V4: Régler vitesse utilisateur (25-255)");
}

void loop() {
  // Temps actuel pour diverses opérations de timing
  unsigned long tempsActuel = millis();
  unsigned long microActuel = micros();
  
  // 1. Lecture des commandes série
  if (Serial.available() > 0) {
    commandeRecue = Serial.readStringUntil('\n');
    commandeRecue.trim();
    nouveauCommandeRecue = true;
  }
  
  // 2. Traitement des commandes
  if (nouveauCommandeRecue) {
    traiterCommande(commandeRecue);
    nouveauCommandeRecue = false;
  }
  
  // 3. Gestion des transitions fluides de vitesse
  if (tempsActuel - dernierChangement >= INTERVALLE_CHANGEMENT) {
    dernierChangement = tempsActuel;
    ajusterVitesseProgressivement();
  }
  
  // 4. Application du PWM pour les deux moteurs
  simulerPWMGauche(microActuel);
  simulerPWMDroite(microActuel);
}

/**
 * Traite les commandes reçues du Serial Monitor ou Raspberry Pi
 */
void traiterCommande(String commande) {
  if (commande == "A") {
    Serial.println("Commande: Avancer progressivement");
    etatMoteur = 1;
    configurationAvancer();
    vitesseCibleGauche = vitesseUtilisateur;
    vitesseCibleDroite = vitesseUtilisateur;
  }
  else if (commande == "R") {
    Serial.println("Commande: Reculer progressivement");
    etatMoteur = 2;
    configurationReculer();
    vitesseCibleGauche = vitesseUtilisateur;
    vitesseCibleDroite = vitesseUtilisateur;
  }
  else if (commande == "S") {
    Serial.println("Commande: Arrêt progressif");
    // On garde la configuration des moteurs mais on vise une vitesse nulle
    vitesseCibleGauche = 0;
    vitesseCibleDroite = 0;
  }
  else if (commande == "G") {
    Serial.println("Commande: Tourner à gauche");
    etatMoteur = 3;
    configurationGauche();
    vitesseCibleGauche = VITESSE_ROTATION;
    vitesseCibleDroite = VITESSE_ROTATION;
  }
  else if (commande == "D") {
    Serial.println("Commande: Tourner à droite");
    etatMoteur = 4;
    configurationDroite();
    vitesseCibleGauche = VITESSE_ROTATION;
    vitesseCibleDroite = VITESSE_ROTATION;
  }
  else if (commande == "V1") {
    vitesseUtilisateur = 25;
    Serial.println("Vitesse utilisateur réglée à 25");
    
    // Appliquer immédiatement la nouvelle vitesse si en mouvement
    if (etatMoteur == 1 || etatMoteur == 2) {
      vitesseCibleGauche = vitesseUtilisateur;
      vitesseCibleDroite = vitesseUtilisateur;
    }
  }
  else if (commande == "V2") {
    vitesseUtilisateur = 50;
    Serial.println("Vitesse utilisateur réglée à 50");
    
    // Appliquer immédiatement la nouvelle vitesse si en mouvement
    if (etatMoteur == 1 || etatMoteur == 2) {
      vitesseCibleGauche = vitesseUtilisateur;
      vitesseCibleDroite = vitesseUtilisateur;
    }
  }
  else if (commande == "V3") {
    vitesseUtilisateur = 125;
    Serial.println("Vitesse utilisateur réglée à 125");
    
    // Appliquer immédiatement la nouvelle vitesse si en mouvement
    if (etatMoteur == 1 || etatMoteur == 2) {
      vitesseCibleGauche = vitesseUtilisateur;
      vitesseCibleDroite = vitesseUtilisateur;
    }
  }
  else if (commande == "V4") {
    vitesseUtilisateur = 255;
    Serial.println("Vitesse utilisateur réglée à 255");
    
    // Appliquer immédiatement la nouvelle vitesse si en mouvement
    if (etatMoteur == 1 || etatMoteur == 2) {
      vitesseCibleGauche = vitesseUtilisateur;
      vitesseCibleDroite = vitesseUtilisateur;
    }
  }
}

/**
 * Ajuste progressivement la vitesse actuelle vers la vitesse cible
 */
void ajusterVitesseProgressivement() {
  bool changementGauche = false;
  bool changementDroite = false;
  
  // Ajuster la vitesse du moteur gauche
  if (vitesseActuelleGauche < vitesseCibleGauche) {
    vitesseActuelleGauche += INCREMENT_VITESSE;
    if (vitesseActuelleGauche > vitesseCibleGauche) {
      vitesseActuelleGauche = vitesseCibleGauche;
    }
    changementGauche = true;
  } 
  else if (vitesseActuelleGauche > vitesseCibleGauche) {
    vitesseActuelleGauche -= INCREMENT_VITESSE;
    if (vitesseActuelleGauche < vitesseCibleGauche) {
      vitesseActuelleGauche = vitesseCibleGauche;
    }
    changementGauche = true;
  }
  
  // Ajuster la vitesse du moteur droit
  if (vitesseActuelleDroite < vitesseCibleDroite) {
    vitesseActuelleDroite += INCREMENT_VITESSE;
    if (vitesseActuelleDroite > vitesseCibleDroite) {
      vitesseActuelleDroite = vitesseCibleDroite;
    }
    changementDroite = true;
  } 
  else if (vitesseActuelleDroite > vitesseCibleDroite) {
    vitesseActuelleDroite -= INCREMENT_VITESSE;
    if (vitesseActuelleDroite < vitesseCibleDroite) {
      vitesseActuelleDroite = vitesseCibleDroite;
    }
    changementDroite = true;
  }
  
  // Afficher les informations uniquement si un changement a eu lieu
  if (changementGauche) {
    Serial.print("Moteur gauche: ");
    Serial.println(vitesseActuelleGauche);
  }
  
  if (changementDroite) {
    Serial.print("Moteur droit: ");
    Serial.println(vitesseActuelleDroite);
  }
  
  // Si les deux moteurs sont à l'arrêt (vitesse = 0)
  if (vitesseActuelleGauche == 0 && vitesseActuelleDroite == 0 && 
      vitesseCibleGauche == 0 && vitesseCibleDroite == 0) {
    if (etatMoteur != 0) {
      etatMoteur = 0;  // État d'arrêt
      arreterMoteurs();
      Serial.println("Moteurs complètement arrêtés");
    }
  }
}

/**
 * Simulation PWM pour le moteur gauche
 */
void simulerPWMGauche(unsigned long microActuel) {
  if (vitesseActuelleGauche == 0) {
    // Moteur à l'arrêt, on désactive les sorties
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    return;
  }
  
  // Calcul du temps d'activation pour ce cycle
  int tempsActifGauche = map(vitesseActuelleGauche, 0, 255, 0, CYCLE_PWM);
  
  // Gestion du cycle PWM
  unsigned long tempsEcouleGauche = microActuel - dernierPWMGauche;
  
  if (!etatPWMGauche && tempsEcouleGauche >= (CYCLE_PWM - tempsActifGauche)) {
    // Activer le moteur gauche (phase ON du PWM)
    activerMoteurGauche();
    etatPWMGauche = true;
    dernierPWMGauche = microActuel;
  } 
  else if (etatPWMGauche && tempsEcouleGauche >= tempsActifGauche) {
    // Désactiver le moteur gauche (phase OFF du PWM)
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    etatPWMGauche = false;
    dernierPWMGauche = microActuel;
  }
}

/**
 * Simulation PWM pour le moteur droit
 */
void simulerPWMDroite(unsigned long microActuel) {
  if (vitesseActuelleDroite == 0) {
    // Moteur à l'arrêt, on désactive les sorties
    digitalWrite(IN3, LOW);
    digitalWrite(IN4, LOW);
    return;
  }
  
  // Calcul du temps d'activation pour ce cycle
  int tempsActifDroite = map(vitesseActuelleDroite, 0, 255, 0, CYCLE_PWM);
  
  // Gestion du cycle PWM
  unsigned long tempsEcouleDroite = microActuel - dernierPWMDroite;
  
  if (!etatPWMDroite && tempsEcouleDroite >= (CYCLE_PWM - tempsActifDroite)) {
    // Activer le moteur droit (phase ON du PWM)
    activerMoteurDroite();
    etatPWMDroite = true;
    dernierPWMDroite = microActuel;
  } 
  else if (etatPWMDroite && tempsEcouleDroite >= tempsActifDroite) {
    // Désactiver le moteur droit (phase OFF du PWM)
    digitalWrite(IN3, LOW);
    digitalWrite(IN4, LOW);
    etatPWMDroite = false;
    dernierPWMDroite = microActuel;
  }
}

/**
 * Active le moteur gauche selon l'état actuel
 */
void activerMoteurGauche() {
  switch (etatMoteur) {
    case 1: // Avancer
      digitalWrite(IN1, HIGH);
      digitalWrite(IN2, LOW);
      break;
    case 2: // Reculer
      digitalWrite(IN1, LOW);
      digitalWrite(IN2, HIGH);
      break;
    case 3: // Tourner à gauche (moteur gauche recule)
      digitalWrite(IN1, LOW);
      digitalWrite(IN2, HIGH);
      break;
    case 4: // Tourner à droite (moteur gauche avance)
      digitalWrite(IN1, HIGH);
      digitalWrite(IN2, LOW);
      break;
    default:
      digitalWrite(IN1, LOW);
      digitalWrite(IN2, LOW);
      break;
  }
}

/**
 * Active le moteur droit selon l'état actuel
 */
void activerMoteurDroite() {
  switch (etatMoteur) {
    case 1: // Avancer
      digitalWrite(IN3, HIGH);
      digitalWrite(IN4, LOW);
      break;
    case 2: // Reculer
      digitalWrite(IN3, LOW);
      digitalWrite(IN4, HIGH);
      break;
    case 3: // Tourner à gauche (moteur droit avance)
      digitalWrite(IN3, HIGH);
      digitalWrite(IN4, LOW);
      break;
    case 4: // Tourner à droite (moteur droit recule)
      digitalWrite(IN3, LOW);
      digitalWrite(IN4, HIGH);
      break;
    default:
      digitalWrite(IN3, LOW);
      digitalWrite(IN4, LOW);
      break;
  }
}

/**
 * Configuration des broches pour avancer
 */
void configurationAvancer() {
  // La configuration est maintenue même pendant les transitions
  Serial.println("Configuration: Avancer");
}

/**
 * Configuration des broches pour reculer
 */
void configurationReculer() {
  // La configuration est maintenue même pendant les transitions
  Serial.println("Configuration: Reculer");
}

/**
 * Configuration pour tourner à gauche
 */
void configurationGauche() {
  // La configuration est maintenue même pendant les transitions
  Serial.println("Configuration: Tourner à gauche");
}

/**
 * Configuration pour tourner à droite
 */
void configurationDroite() {
  // La configuration est maintenue même pendant les transitions
  Serial.println("Configuration: Tourner à droite");
}

/**
 * Arrêter complètement les moteurs
 */
void arreterMoteurs() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
  Serial.println("Moteurs arrêtés");
}
