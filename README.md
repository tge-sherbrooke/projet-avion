# projet-avion
Description du projet pour le module 2 du cours Introduction aux objets connectés (243-413-SH)

## Schéma
![schéma bloc](/img/schema.jpeg)
Le schéma vous présente les différentes parties en haut niveau. Il ne s'agit pas d'un schéma électrique. C'est à vous de s'occuper de relier les différentes parties.

## Spécifications électriques
Ce projet va utiliser à peu près toutes les entrée/sorties (E/S) du Rapsberry Pi. C'est à vous de vous assurez de bien utiliser toutes les E/S de manière optimale. Tenez un tableau à jour avec les connexions entre le rPi et vos différents périphériques. Voici un exemple:

| # pin | Nom E/S | Connecté à       |
|-------|---------|-------------------|
| 2     | GPIO 2  | Bouton *joystick* |

### I2C
Le protocole I2C permet de communiquer avec plusieurs périphériques sur la même paire SDA/SCL. Pour ce projet là, l'écran LCD et l'ADC utilisent le protocole I2C. Vous pouvez donc les relier sur la même paire.

### Alimentation
Vous aurez besoin de fournir du 3.3V ainsi que du 5V. Ce dernier est utilisé par les moteurs (moteur pour l'hélice et le servomoteur). **N'utilisez pas l'alimentation venant du rPi pour ce projet**. Utilisez plutôt le module d'alimentation venant avec votre kit. Il peut être alimenté par USB ou par une pile 9V. Le puissance requise pour alimenter le projet est trop grande pour le rPi.

## Spécifications logicielles
À venir

## Pointage pour le projet
À venir
