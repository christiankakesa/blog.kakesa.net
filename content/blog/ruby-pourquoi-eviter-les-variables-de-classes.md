---
title: "Ruby, Pourquoi Éviter Les Variables De Classes ?"
categories: ["Ruby", "Programmation"]
type: "post"
date: 2017-10-07T08:00:00+02:00
---

![Logo Ruby](/images/logo_ruby_h260.png#floatright)

Vous vous demandez pourquoi éviter l'utilisation des variables de classe ?

Avec le code source qui suit, nous allons voir les raisons qui font qu'il faut les éviter.

Comme d'habitude, je propose en début d'article la bonne solution.

En [Ruby](https://www.ruby-lang.org), il vaut mieux utiliser une méthode de classe qui encapsule une variable d'instance. Cette variable dinstance sera unique pour l'objet de la classe qui la définit. C'est possible car *Ruby* est un langage de programmation où tout élément est un objet.

> Voilà ce qu'il faut faire :

```ruby
class Stream
  def self.get
    @chunk ||= 'init'
  end
  
  def self.set(value)
    @chunk = value
  end
end

puts Stream.get
puts Stream.set('OK')
@chunk = 'Noonoonoon !'
puts Stream.get # 8-)
```

Résultat :

```conf
init
OK
OK
```

Avec la version ci-dessous utilisant une variable de classe, le résultat est assez étonnant.
Pour autant cela respect ce qu'est une variable de classe en *Ruby* : 

```ruby
class Stream
  @@chunk = String.new('init')

  def self.get
    @@chunk
  end
  
  def self.set(value)
    @@chunk = value
  end
end

puts Stream.get
puts Stream.set('OK')
@@chunk = 'Noonoonoon !'
puts Stream.get # 0°~(
```

Résultat : 

```conf
init
OK
Noonoonoon !
```

Décortiquons cette version de la classe `Stream` :

* Tout d'abord une variable de classe non initialisée lève une exeception contrairement à une variable d'instance qui vaut `nil` par défaut. L'initialisation est obligatoire : `@@chunk = String.new('init')`.
* Il faut savoir qu'une variable de classe n'est pas *thread safe*. Dans un programme *multi-thread* il faut protéger l'accès aux variables de classe avec un `Mutex` par exemple.
* L'assignation `@@chunk = 'Noonoonoon !'`, équivaut à assigner `@@chunk` dans la classe `Object` dont toutes les classes héritent en *Ruby*. C'est pour cette raison que le dernier `puts Stream.get` renvoi `Noonoonoon !`. Imaginez qu'une bibliothèque écrase vos données parceque vous utiliser le même nom de variable de classe. *C'est un bug qui mettra des jours à être corrigé*.

Un des idiom en *Ruby* est de ne pas utiliser les variables de classe et favoriser l'utilisation des variables d'instance comme le montre la première version de la classe `Stream`.

N'hésitez pas à laisser vos commentaires.
