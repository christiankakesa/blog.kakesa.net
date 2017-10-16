---
author: "Christian Kakesa"
categories: ["Arduino", "ESP8266", "Programmation"]
title: "Arduino ESP8266, Démarrage En Douceur"
description: ""
type: "post"
featured: ""
featuredalt: ""
featuredpath: ""
linktitle: ""
date: 2017-10-16T08:00:00+02:00
---

![Photo ESP8266 NodeMCU](/images/esp8266.png#center)

Pour la petite histoire, lors du dernier [Paris Redis Meetup](https://www.meetup.com/fr-FR/Paris-Redis-Meetup/), [RedisLab](https://redislabs.com/) nous a gracieusement offert un [ESP8266 (NodeMCU ESP-12E)](https://github.com/esp8266/Arduino) qui peut se programmer avec un IDE Arduino.

![Redis iot ESP8266](/images/redis_iot_esp8266.png) ![Lab ESP8266 à Paris Redis meetup](/images/redis_iot_labs.png)

Le *LAB* consistait à envoyer les données du capteur de luminosité à travers le Wifi de l'ESP8266 vers une base de données **Redis**.
Pour la visualisation, un dashboard conçu avec AngularJS se chargeait de montrer les captures de chaque participant.

## Installer l'IDE Arduino et configurer le module ESP8266

* Télécharger l'IDE Arduino pour votre système d'exploitation : [Linux, Windows ou Mac](https://www.arduino.cc/en/Main/Software)
 * Selon votre système d'exploitation, suivez le [guide de démarrage](https://www.arduino.cc/en/Guide/HomePage)
 * Exemple sur *Ubuntu* : ajouter votre utilisateur dans le groupe *dialout* afin d'avoir les permissions nécessaires d'écriture : `sudo usermod -a -G dialout <username>`. Fermer votre session graphique, puis ré-ouvrez de nouveau.
* Ajouter le dépôt pour la carte ESP8266 : 
 * *Fihier* > *Préférences* > *URL de gestionnaire de cartes supplémentaires*, coller l'URL : *http://arduino.esp8266.com/stable/package_esp8266com_index.json* ![URL du dépôt communautaire ESP8266](/images/arduino_preferences_url_esp8266.png)
* Installer le *plugin* de la carte NodeMCU 1.0 : 
 * *Outils* > *Type de carte* > *Gestionnaire de carte* ![Imprimé écran du gestiionnaire de carte](/images/arduino_gestionnaire_de_carte_esp8266.png)
 * Rechercher *esp8266* et sélectionner la dernière version du module ![Module ESP8266 community](/images/arduino_installation_carte_esp8266.png)
 * Sélectionner le type de carte qui correspond à votre module, pour moi il s'agit du module *NodeMCU 1.0 (ESP-12E Module)* ; [liste de la famille des cartes ESP8266](http://esp8266.github.io/Arduino/versions/2.3.0/doc/boards.html) ![Image de sélection du module NodeMCU 1.0 (ESP-12E Module)](/images/arduino_selection_nodemcu1.0_esp-12e.png)

## Premier programme, *LED dancing*

Ce premier programme de type *Hello World* que j'appelle *LED dancing* est relativement simple.
Les commentaires vous expliquent ce que fait chaque ligne.

> **D4** correspond au connecteur numérique de la LED qui se trouve sur le module Wifi, les informations du schéma se trouvent sur le [site internet officiel de l'ESP8266](http://esp8266.github.io/Arduino/versions/2.3.0/doc/boards.html#nodemcu-1-0). vous pouvez essayer avec **LED_BUILTIN** qui correspond à **D0** sur ma carte, pour la led par défaut.

Vous pouvez copier/coller le code ci-dessous puis appuyer sur les touches <kbd>CTRL + U</kbd> pour compiler et téléverser *(uploader)* le programme dans le microcontrôleur.

```cpp
void setup() {
  pinMode(D4, OUTPUT);  // On configure D4 en mode OUTPUT
  Serial.begin(115200); // On configure le taux de transfert à 115200 bps
  while (!Serial);      // On attend que le canal de transmission soit prêt
  delay(10);            // On laisse une chance à d'autres traitements de se lancer
  Serial.println("LED dancing is ready !"); // On affiche un message sur le *Moniteur série*
}

void loop() {
  digitalWrite(D4, LOW);  // Allume la LED (bizarrement on utilise LOW)
  delay(1000);            // On attend 1 seconde (milli seconde)
  digitalWrite(D4, HIGH); // Éteint la LED
  delay(1000);            // On attend 1 seconde (milli seconde)
}
```

> La led se met à clignoter toutes les secondes.

![ESP8266 blink](/images/esp8266_blink.gif#center)

Le prochain article expliquera les fonctions `void setup();` et `void loop();`, puis affichera sur le *moniteur série* la valeur du capteur de lumière de notre montage.

> On est loin de mon expérience [FPGA](https://fr.wikipedia.org/wiki/Circuit_logique_programmable) avec l'[IDE de Xilinx](https://www.xilinx.com/products/design-tools/ise-design-suite/ise-webpack.html) qui me semblait tout de même assez complexe il y a plusieurs années. Je vais progresser en même temps que mes articles sur Arduino et dans un premier temps avec la carte *ESP8266*.
