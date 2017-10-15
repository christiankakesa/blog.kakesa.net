---
author: "Christian Kakesa"
date: 2017-10-02T08:00:00+02:00
categories: ["Go"]
title: "Hugo, Un Puissant Generateur De Site Statique"
type: "post"
---

C'est une première pour mon blog de passer par un moteur de génération de site internet statique.

Il existe [un grand choix de moteur](https://www.staticgen.com/) qui répondent à tous les besoins.
Le plus connu, d'après mon expérience, est [Jekyll](http://jekyllrb.com/) écrit en [Ruby](https://www.ruby-lang.org).

Mon choix s'est arrêté sur [Hugo](https://gohugo.io/) écrit en [Go](https://golang.org/).
C'est un prétexte suffisant pour découvrir un projet qui n'arrête pas de monter en popularité et [les exemples de sites internets](https://themes.gohugo.io/) sont vraiment très différents des uns des autres ; tant en *design* qu'en apparence.
Si on se fie à sa popularité sur [Github](https://github.com/), c'est le second moteur après *Jekyll* : https://www.staticgen.com.

![Logo Hugo](/images/logo_hugo.png#center)

Pourquoi *Hugo* ? Tout d'abord pour sa simplicité, à 2 niveaux, les articles sont écrits en <abbr title="HyperText Markup Language">HTML</abbr> ou [Markdown](https://wikipedia.org/wiki/Markdown) et le binaire statique **hugo** (c'est du Go) permet de tout faire : 

  * Générer son site
  * Lancer le site en mode développement avec rechargement automatique des changements
  * Lancer le site en mode production 
  * Ajouter un aricle
  * Mesurer la perfomance globale lors de la génération du site
  * ...

L'autre point qui m'a vraiment plu, c'est le nombre de thème proposé : https://themes.gohugo.io/.
Tout le monde s'y retrouve.

Puisque tous mes projets, y compris mon blog, sont déployés sur **[Apache Mesos](http://mesos.apache.org/)**, orchestrés par le framework *Mesos* **[Marathon](https://mesosphere.github.io/marathon/)** et pour mes jobs le framework *Mesos* **[Chronos](https://mesos.github.io/chronos/)**.

![Logo Apache Mesos](/images/logo_apache-mesos_260x260.png) ![Logo Marathon : Apache Mesos framework](/images/logo_marathon-mesos-framework_260x260.png) ![Logo Chronos : Apache Mesos framework](/images/logo_chronos-mesos-framework_260x260.png)


Le choix d'architecture était tout simple avec les éléments suivants : 

  * Mon blog est hébergé sur *Github* ; *Github* fournit une URL permettant de récupérer l'archive de la branche *master*
  * Le projet *Hugo* est aussi disponible sur *Github*, je peux récupérer l'archive du binaire **hugo** pour ma plateforme (Linux x86_64)
  * Mon fichier de configuration pour *Marathon* est aussi simple que celui-ci ([sans les *healthCheck*](https://mesosphere.github.io/marathon/docs/health-checks.html)) :

```json
{
  "id": "/kakesa",
  "labels": {
    "environment": "production",
    "technologies": "Hugo, Go(lang)"
  },
  "apps": [
    {
      "id": "/kakesa/www",
      "cmd": "export HOME=$(pwd); cd ./kakesa.net-master; ../hugo --theme=hugo-future-imperfect; ../hugo server --baseURL=https://kakesa.net --appendPort=false --config=config.toml --log --port=${PORT0} --watch=false --bind=0.0.0.0",
      "cpus": 0.1,
      "mem": 64,
      "instances": 1,
      "ports": [65000],
      "uris": [
        "https://github.com/gohugoio/hugo/releases/download/v0.29/hugo_0.29_Linux-64bit.tar.gz",
        "https://github.com/fenicks/kakesa.net/archive/master.zip"
      ]
    }
  ]
}
```

Le workflow pour mettre à jour mon blog est tout aussi simple : 

  1. Écriture d'un nouveau post
  2. Git commit/push de la branche *master* sur *Github*
  3. Redémarrage de l'application depuis *Marathon* pour la prise en compte

Et voilà, c'est tout.

> Je pourrais limiter mon workflow à 2 étapes : `1. écriture du post`, `2. git commit/push` qui déclencherais automatiquement un redémarrage sur *Marathon* ; mais je préfère garder les 3 étapes pour plus de souplesse dans l'élaboration de mes contenus.
