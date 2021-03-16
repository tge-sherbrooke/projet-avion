# projet-avion
Description du projet pour le module 2 du cours Introduction aux objets connectés (243-413-SH)

## Schéma
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
Dans cet état, le système au complet est alimenté, mais il est impossible de contrôler les moteurs (*DC* et servo), le clavier ne répond pas et l'interrupteur ne fait rien. Le programme affiche le texte "Scannez carte" sur le LCD puis attend qu'une carte ou badge *RFID* soit passé près du capteur *RFID*. Lorsqu'une carte ou badge est détecté, le programme va faire une requête au serveur de contrôle pour savoir si cette carte à le droit d'utiliser l'avion. Dans le cas que la carte soit autorisée, le programme peut passer à l'état 2. Sinon, le message d'erreur suivant apparaît pendant une seconde : "Carte non-autorisee". Après une seconde, le message "Scannez carte" revient et le programme retourne en attente. Voici la séquence à respecter:
![état 1](/img/etat1.png)
### État 2 - Pré-vol

### État 3 - Prêt à voler
## Pointage pour le projet
À venir
