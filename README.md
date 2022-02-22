# projet-avion
Description du projet pour le module 2 du cours Introduction aux objets connectés (243-413-SH)

## Présentation
Le but du projet est de simuler plusieurs parties d'un avion:
* Contrôle du moteur et d'un aileron
* Affichage des paramètres de vol
* Contrôle d'accès
* Sélection de la destination

### Schéma concept partie physique
![schéma bloc](/img/schema.jpeg)
Le schéma vous présente les différentes parties en haut niveau. Il ne s'agit pas d'un schéma électrique. C'est à vous de s'occuper de relier les différentes parties.

## Spécifications électriques
Ce projet va utiliser à peu près toutes les entrée/sorties (E/S) du Rapsberry Pi. C'est à vous de vous assurez de bien utiliser toutes les E/S de manière optimale. Tenez un tableau à jour avec les connexions entre le rPi et vos différents périphériques. Voici un exemple:

### Tableau de connexions pour le *header* du *Raspberry Pi*
| # *pin* | Nom E/S | Connecté à       |
|-------|---------|-------------------|
| 2     | GPIO 2  | Bouton *joystick* (SW) |

### Tableau de connexions pour le *joystick*
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour l'*ADC*
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le *LCD*
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le capteur *RFID*
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le clavier
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le contrôle moteur (L293D)
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le moteur *DC*
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour le servomoteur
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### Tableau de connexions pour l'interrupteur
| Label (*pin*) | Connecté à       |
|-------|-------------------|
| 2     | Bouton *joystick* (SW) |

### I2C
Le protocole I2C permet de communiquer avec plusieurs périphériques sur la même paire SDA/SCL. Pour ce projet là, l'écran LCD et l'ADC utilisent le protocole I2C. Vous pouvez donc les relier sur la même paire.

### Alimentation
Vous aurez besoin de fournir du 3.3V ainsi que du 5V. Ce dernier est utilisé par les moteurs (moteur pour l'hélice et le servomoteur). **N'utilisez pas l'alimentation venant du rPi pour ce projet**. Utilisez plutôt le module d'alimentation venant avec votre kit. Il peut être alimenté par USB ou par une pile 9V. Le puissance requise pour alimenter le projet est trop grande pour le rPi.

## Spécifications logicielles
Le fonctionnement de l'avion est divisé en trois états:
* En attente (E1)
* Pré-vol (E2)
* Prêt à voler (E3)

![Diagramme d'état](/img/state.png)
Lorsque le programme est démarré, il rentre dans l'état E1. Une fois dans l'état E1, le programme peut rester dans cet état jusqu'à que la condition C2 soit vraie. La liste des conditions est présentée plus bas. Lorsque la condition C2 est vraie, le programme passe dans l'état E2. Dans l'état E2, il y a trois possibilités: rester dans E2, revenir dans E1 ou passer à l'état E3. Ces trois possibilités sont sont choisies selon les conditions C3, C4 et C5. Finalement, l'état E3 est atteint lorsque le programme est dans l'état E2 et que la condition C5 soit vraie. Dans cet état, il est possible de revenir à l'état E1 si la condition C7 est vraie.

Voici le tableau expliquant chacune des conditions

| Condition | Vrai si |
|--|--|
|C1|C2 fausse|
|C2|Code carte RFID autorisé à utiliser l'avion|
|C3|Touche # appuyée|
|C4|C3 et C5 fausses|
|C5|Interrupteur PWR en position "ON"|
|C6|C7 fausse|
|C7|Interrupteur PWR en position "OFF"|

### Conception d'un programme à partir d'un diagramme d'état
Lorsqu'un programme fonctionne selon le paradigme de diagramme d'état, il est divisé en deux partie: la section qui exécute les actions de l'état et la section vérifiant les conditions pour mettre à jour l'état

```python
def loop():
  currentstate = "E1"
  while (True):

    if currentstate == "E1":
      E1()
      # Validation des conditions pour la mise à jour de l'état
      if C2 is True:
        currentstate = "E2"

    elif currentstate == "E2":
      E2()
      # Validation des conditions pour la mise à jour de l'état
      if C3 is True:
        currentstate = "E1"
      elif C4 is True:
        currentstate = "E3"

    elif currentstate == "E3":
      E3()
      # Validation des conditions pour la mise à jour de l'état
      if C7 is True:
        currentstate = "E1"
```

### État 1 - En attente
Dans cet état, le système au complet est alimenté, mais il est impossible de contrôler les moteurs (*DC* et servo), le clavier ne répond pas et l'interrupteur ne fait rien. Le programme affiche le texte "Scannez carte" sur le LCD puis attend qu'une carte ou badge *RFID* soit passé près du capteur *RFID*. Lorsqu'une carte ou badge est détecté, le programme va faire une requête au serveur de contrôle pour savoir si cette carte à le droit d'utiliser l'avion. La requête GET doit avoir l'URI suivante: `/rfid/{rfidcardcode}`. Dans le cas que la carte soit autorisée, le programme peut passer à l'état 2. Sinon, le message d'erreur suivant apparaît pendant une seconde : "Carte non-autorisee". Après une seconde, le message "Scannez carte" revient et le programme retourne en attente. Voici la séquence à respecter:
![état 1](/img/etat1.png)
### État 2 - Pré-vol
Le pré-vol permet d'entrer le code de l'aéroport de destination, comme dans un véritable avion. Le code est composé de trois chiffres. Une fois le code entré via le clavier, vous devrez faire une requête à l'API que vous allez avoir développé dans votre cours en informatique. Il s'agit de faire une requête GET avec l'URI suivante: `/airports/{airportcode}`. Votre API devra retourner le nom de l'aéroport.

Voici le tableau contenant les aéroports à implémenter dans votre API:

| Code numérique | Nom de l'aéroport|
|----|----|
| 101 | YUL Montreal |
| 111 | ATL Atlanta |
| 222 | HND Tokyo |
| 764 | LHR London |
| 492 | CAN Baiyun |
| 174 | CDG Paris |
| 523 | AMS Amsterdam |

Une fois le code entré et le nom de l'aéroport obtenu, le LCD affiche qu'il est possible de démarrer l'avion en passant l'interrupteur PWR à la position ON. Une fois fait, le programme peut passer à l'état 3.
![état 2](/img/etat2.png)
### État 3 - Prêt à voler
Dans cet état, les commandes de l'avion deviennent disponibles et il est possible d'activer le moteur et servo moteur. Lorsque le *joystick* est utilisé, le moteur et le servo moteur réagissent selon les spéfications suivantes:
* L'axe X sert à actionner le servo-moteur
* L'axe Y sert à actionner le moteur
* Au repos, le servo est à l'angle 90°
* Au repos, le moteur est à 0%
* Il y a une relation linéaire entre la valeur du potentiomètre X et l'angle du servo moteur
  * 0-255 -> 0 à 180°
* Il y a une relation linéaire enter la valeur du potentiomètre Y et la puissance fournie au moteur
  * 0-255 -> -100% à 100%
* Lorsque vous appuyez sur le *joystick*, la vitesse et l'angle sont verrouillés. Ce qui veut dire que l'avion garde la vitesse et l'angle au moment où le *joystick* a été appuyé. Pour déverrouiller la vitesse et l'angle, il faut de nouveau appuyer sur le *joystick*.

Ensuite, toutes les 100 ms, le LCD doit afficher:
* La puissance en % fournie au moteur
* L'angle du servo-moteur
* La destination

La difficulté pour le respect du 100 ms est que votre boucle de l'état 3 doit s'exécuter plus rapidement que 100 ms pour donner l'impression que les réponses du joystick soit instanné.
Pour respecter la contraintes de 100 ms, utiliser les fonctions déjà disponibles en Python pour utiliser des timers. Voir [ce guide](https://realpython.com/python-timer/) pour plus de détails sur le respect du 100 ms.

## Tester l'utilisation d'un API

En attendant que vous développiez votre propre API, il est possible d'utiliser un API déjà prêt pour obtenir les codes d'aéroport. Voici la requête à utiliser pour obtenir les codes:

```
GET http://bosco.pw/airports/111
```
Il suffit de remplacer 111 dans l'exemple pour le code que vous souhaitez. Le nom de l'aéroport vous est retourné .

Pour pouvoir faire une requête à l'API en Python, vous pouvez utiliser le module *request*:

```python
import request

response = requests.get("http://bosco.pw/airports/111")

print(response.text)
```
## Pointage pour le projet
Pour le pointage voici la description des points

|Description|Points|
|---|---|
|Moteur|5 pts|
