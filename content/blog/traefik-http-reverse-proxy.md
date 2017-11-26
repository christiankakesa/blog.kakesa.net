---
author: "Christian Kakesa"
categories: ["Traefik", "HTTP", "Réseau"]
date: 2017-11-26T01:05:00+02:00
description: ""
draft: true
featured: ""
featuredalt: ""
featuredpath: ""
linktitle: ""
title: "Traefik, Un Reverse Proxy HTTP Moderne"
type: "post"
---

[Traefik](https://traefik.io) est un *reverse* proxy HTTP dynamique adapté pour exposer des services distribués ou des microservices. Sa capacité de se mettre à jour dynamiquement m'interresse énormément pour optimiser ma plateforme *Apache Mesos*...

![Logo Traefik](/images/logo_traefik.png)

Traefik est écrit en Go, ce qui permet de ne plus avoir de problème de dépendance des bibliothèques. Un simple binaire à lancer.

Ce qui change par rapport aux *reverse* proxy plus connus tels que : **HA Proxy**, **Nginx** voir même **Apache HTTP server**, c'est l'interconnexion native avec les sytèmes de configuration distribués.
Un grand nombre de ces systèmes sont supportés :

* [Docker Swarm](https://docs.docker.com/engine/swarm/)
* [Kubernetes](http://kubernetes.io/)
* [Mesos](https://github.com/apache/mesos) and [Marathon (framework Mesos)](https://mesosphere.github.io/marathon/)
* [Rancher](https://rancher.com/)
* [Consul](https://www.consul.io/), [Etcd](https://coreos.com/etcd/), [Zookeeper](https://zookeeper.apache.org/), [BoltDB](https://github.com/boltdb/bolt) (moteurs de persistence distribués : découverte de service, configuration, ...)
* [Eureka](https://github.com/Netflix/eureka)
* [Amazon ECS](https://aws.amazon.com/fr/ecs/)
* [Amazon DynamoDB](https://aws.amazon.com/fr/dynamodb/)
* Fichier de configuration ([TOML](https://github.com/toml-lang/toml))
* API REST (HTTP)

Pour ma part je viens de passer à *Traefik* sur ma plateforme *Apache Mesos*.
L'idéee est de ne plus avoir de script bash exécuté toutes les minutes afin de mettre à jour les backends sur *[HAProxy](http://www.haproxy.org/)*.
Avec ce mécanisme d'exécution de script bash pour la mise à jour de la configuration [HAProxy](http://www.haproxy.org/), j'ai potentiellement jusqu'à 60 secondes d'intéruption de mes services lors d'une mise à jour d'un des services.

Avec *Traefik*, je suis directement connecté à *Marathon* ou *Apache Mesos* ; la configuration se met à jour au fur et à mesure des changements sur le cluster *Apache Mesos*.

![Architecture de Traefik](/images/traefik_architecture.png)

[Traefik](https://traefik.io) fournit aussi une interface web permettant de voir la configuration des *frontends* et *backends* ainsi que de suivre les temps de réponses et les statuts *HTTP* servis.

![Traefik, copnfiguration](/images/traefik_stats.png)

![Traefik, ](/images/traefik_webui.png)

N'hésitez pas à vous documenter sur [Traefik](https://traefik.io) et allez discuter avec la communauté des développeurs et utilisateurs de [Traefik sur le Slack](https://traefik.herokuapp.com/).
