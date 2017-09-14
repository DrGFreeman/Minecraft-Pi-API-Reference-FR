# Référence d'API Minecraft Pi

## Introduction

Ceci est une référence pour la librairie Python de l'API de Minecraft supportée par l'[édition Minecraft: Pi](https://minecraft.net/fr-ca/edition/pi/) pour le [Raspberry Pi](https://www.raspberrypi.org/).

## Contenu et format

La première section ci-dessous montre la structure de la librairie. Par la suite, l'ensemble des méthodes des différentes classes sont décrites dans le format suivant:

#### `.nomDeMethode(arguments)`
Description => type de retour (si applicable)
```Python
# Exemple de code utilisant la méthode
variable = mc.nomMethode(arguments)
```

La description de la classe `Block` fournit la liste des `id` de blocs disponibles via l'API ainsi que les valeurs possibles du paramètre `data` lorsqu'applicable.

## Référence de l'API

### Structure de l'API

* `minecraft.py`
  * [Classe `Minecraft`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-minecraft): classe principale pour se connecter et interagir avec le jeu
    * [Classe `player`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-minecraftplayer): obtenir et changer la position et les réglages du joueur
    * [Classe `entity`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-minecraftentity): obtenir et changer la position et les réglages des entités (ou joueurs)
    * [Classe `camera`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-minecraftcamera): changer l'angle et la position de la caméra
    * [Classe `events`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-minecraftevents):  obtenir les événements qui se sont produits dans la partie
* `block.py`
  * [Classe `Block`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-block): définition d'un bloc, en particulier de son type
* `event.py`
  * [Classe `BlockEvent`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-blockevent): définition d'un événement de bloc, en particulier quel événement, quel bloc et quel joueur
* `vec3.py`
  * [Classe `Vec3`](https://github.com/DrGFreeman/Minecraft-Pi-API-Reference-FR#classe-vec3): classe générale pour la gestion d'un vecteur tridimensionnel (c.à.d. x, y, z)
* `connection.py`: module interne utilisé par l'API
* `util.py`: module interne utilisé par l'API

### Classe `Minecraft`

"La classe principale pour interagir avec une instance de Minecraft Pi". Inclus les fonctions pour créer une connection, modifier les joueurs et blocs et capturer les événements.

#### `.create(address = "localhost", port = 4711)`
Crée une connection avec Minecraft (adresse, port) => objet `Minecraft`.
```Python
# Utiliser l'adresse et le port par défaut
mc = minecraft.Minecraft.create()

# Spécifier l'adresse ip et le port
mc = minecraft.Minecraft.create("192.168.1.1", 4711)
```

#### `.getBlock(x, y, z)`
Obtient le type (id) du bloc à la position (x, y, z) => entier:id.

```Python
# Obtenir le type du bloc à (0, 0, 0)
typeBloc = mc.getBlock(0, 0, 0)
```

#### `.getBlockWithData(x, y, z)`
Obtient le type du bloc avec données à la position (x, y, z) => objet `Block`.

```Python
# Obtenir un objet bloc pour le bloc à (0, 0, 0)
objBloc = mc.getBlockWithData(0, 0, 0)
```

#### `.setBlock(x, y, z, id, *data)`
Place un bloc à (x, y, z) de type id avec donnée facultative \*data.

```Python
# Placer un bloc à 0, 0, 0 de type "DIRT"
mc.setBlock(0, 0, 0, block.DIRT.id)

# PLacer un bloc à 0, 0, de type "WOOD" avec sous-type 1
mc.setBlock(0, 0, 0, block.WOOD.id, 1)
```

#### `.setBlocks(x0, y0, z0, x1, y1, z1, id, *data)`
Remplit une cuboïde de blocs dans l'espace entre (x0, y0, z0) et (x1, y1, z1) de type id avec donnée facultative \*data.

```Python
# Place de multiples blocs en remplissant l'espace
# entre deux points (-2, -2, -2) et (2, 2, 2).
mc.setBlocks(-2, -2, -2, 2, 2, 2, block.STONE.id)
```

#### `.getHeight(x, z)`
Obtient la hauteur du monde à la position (x, z) => entier.

```Python
# Obtenir la position y (verticale) du bloc le plus
# haut (n'étant pas de l'air) à la position 0, 0
y = mc.getHeight(0, 0)
```

#### `getPlayerEntityIds()`
Obtient une liste des id des entités des joueurs connectés => [entier:id]

```Python
# Afficher la liste des id des entités des joueurs connectés
idEntites = mc.getPlayerEntityIds()
for idEntite in idEntites:
  print(idEntite)
```

#### `.saveCheckpoint()`
Sauvegarde un "checkpoint" qui peut être utilisé pour restaurer le monde.

#### `.restoreCheckpoint()`
Restaure le monde à sa condition au moment de la sauvegarde du checkpoint avec `.saveCheckpoint()`.

#### `.postToChat(message)`
Affiche un message dans le "chat" du jeu.

```Python
# Écrire "Bonjour monde Minecraft!" dans la fenêtre de jeu
mc.postToChat("Bonjour monde Minecraft!")
```

#### `.setting(setting, status)`
Règle les paramètres du jeu (setting, status). Clés de paramètres: `'world_immutable'`, `'nametags_visible'`.

```Python
# Régler 'world_immutable' à vrai (True)
mc.setting('world_immutable', True)

# Régler 'nametags_visible' à faux (False)
mc.setting('nametags_visible', False)
```

### Classe `Minecraft.player`

Classe qui permet d'interagir avec le joueur, pour obtenir et régler sa position.

#### `.getPos()`
Obtient la position du joueur dans le monde => objet `Vec3` (nombres décimaux).  
Si le joueur est au milieu d'un bloc, x.5 est retourné.

```Python
# Obtenir la position du joueur (nombres décimaux)
posJoueur = mc.player.getPos()

# Assigner les coordonnées de la position du joueur
# à des variables individuelles
x, y, z = mc.player.getPos()
```

#### `.setPos(x, y, z)`
Déplace le joueur à la position (x, y, z).

```Python
# Déplacer le joueur à la position (0.5, 1.0, 10.1)
mc.player.setPos(0.5, 1, 10.1)
```

#### `.getTilePos()`
Obtient la position de la "tuile" ou bloc sur lequel le joueur se tient => objet `Vec3`.

```Python
# Obtenir la position du bloc sur lequel le joueur se tient
tuileJoueur = mc.player.getTilePos()
```

#### `.setTilePos(x, y, z)`
Déplace le joueur sur le bloc à la position (x, y, z).

```Python
# Déplacer le joueur sur le bloc à la position (0.5, 1.0, 10.1)
mc.player.setTilePos(0.5, 1, 10.1)
```

#### `.setting(setting, status)`
Règle un paramètre du joueur (setting, status). Clé de paramètre: `'autojump'`.

```Python
# Régler 'autojump' à vrai (True)
mc.player.setting('autojump', True)
```

### Classe `Minecraft.entity`

Les méthodes de la classe `Minecraft.entity` sont utilisées en conjonction avec la méthode `.getPlayerEntityIds()` pour interagir avec les entités (ou les joueurs) dans une partie. Les méthodes de cette classe sont utiles pour les parties multi-joueurs.

```Python
# Obtenir l'id des entités des joueurs connectés à la partie
idEntites = mc.getPlayerEntityIds()
idEntite1 = idEntites[0]
idEntite2 = idEntites[1]
# ...
```

#### `.getPos(entityId)`
Obtient la position d'une entité dans le monde => objet `Vec3` (nombres décimaux).  
Si l'entité' est au milieu d'un bloc, x.5 est retourné.

```Python
# Obtenir la position de l'entité 'idEntite'(nombres décimaux)
posEntite = mc.entity.getPos(idEntite)

# Assigner les coordonnées de la position de l'entité
# à des variables individuelles
x, y, z = mc.entity.getPos(idEntite)
```

#### `.setPos(entityId, x, y, z)`
Déplace l'entité à la position (x, y, z).

```Python
# Déplacer l'entité 'idEntite' à la position (0.5, 1.0, 10.1)
mc.entity.setPos(idEntite, 0.5, 1, 10.1)
```

#### `.getTilePos(entityId)`
Obtient la position de la "tuile" ou bloc sur lequel l'entité se tient => objet `Vec3`.

```Python
# Obtenir la position du bloc sur lequel l'entité 'idEntite' se tient
tuileEntite = mc.entity.getTilePos(idEntite)
```

#### `.setTilePos(entityId, x, y, z)`
Déplace l'entité sur le bloc à la position (x, y, z).

```Python
# Déplacer l'entité 'idEntite' sur le bloc à la position (0.5, 1.0, 10.1)
mc.entity.setTilePos(idEntite, 0.5, 1, 10.1)
```

### Classe `Minecraft.camera`

Classe permettant de régler les paramètres de la caméra.

#### `.setNormal(*entityId)`
Règle le mode de la caméra à la vue normale de Minecraft. Argument facultatif \*entityId.

```Python
# Régler le mode de la caméra à normal pour le joueur
mc.camera.serNormal()

# Règler le mode de la caméra à normal pour l'entité 'idEntite'
mc.camera.setNormal(idEntite)
```

#### `.setFixed()`
Règle le mode de la caméra à fixe.
```Python
# Régler le mode de la caméra à fixe
mc.camera.setFixed()
```

#### `.setFollow(*entityId)`
Règle le mode de la caméra pour suivre le joueur ou une entité facultative \*entityId.

```Python
# Régler la caméra pour suivre le joueur
mc.camera.setFollow()

# Régler la caméra pour suivre l'entité 'idEntite'
mc.camera.setFollow(idEntite)
```

#### `.setPos(x, y, z)`
Définit la position de la caméra aux coordonnées (x, y, z)

```Python
# Régler la position de la caméra à (0, 0, 0)
mc.camera.setPos(0, 0, 0)
```

### Classe `Minecraft.events`

Cette classe permet de récupérer la liste des événements qui se sont produits dans la partie et leurs données associées.

### Classe `Block`

### Classe `BlockEvent`

### Classe `Vec3`

## Remerciements

Merci à [Martin O'Hanlon](http://www.stuffaboutcode.com/) pour la [version originale anglaise](http://www.stuffaboutcode.com/p/minecraft-api-reference.html) de la référence sur laquelle cette page a été basée.

## Licence

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>  
Sauf si indiqué autrement, le contenu de cette page est mis à disposition selon les termes de la <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Licence Creative Commons Attribution-ShareAlike 4.0 International</a>.  
Copyright (c) 2017, Julien de la Bruère-Terreault, [drgfreeman@tuta.io](mailto:drgfreeman@tuta.io)  
