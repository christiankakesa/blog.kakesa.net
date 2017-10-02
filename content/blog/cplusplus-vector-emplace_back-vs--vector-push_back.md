---
author: "Christian Kakesa"
date: 2017-09-27T08:00:00+02:00
categories: ["C++", "Programmation"]
title: "C++ std::vector::emplace_back VS std::vector::push_back"
type: "post"
---

![Logo ISOCPP](/images/logo_cpp_w260.png#floatleft)

Vous avez sans doute déjà lu un programme **C++** qui utilisait **`std::vector::emplace_back`** et vous vous demandiez quelle est la différence avec `std::vector::push_back` ? Je me pose la même question.

À travers un mini sujet, nous allons voir quelles sont les différences entre **`std::vector::emplace_back`** et `std::vector::push_back`.

Bizarrement, je vais exposer ma conclusion et laisser ceux qui ont le temps de lire le déroulé complet de mon sujet.

> Idéalement utiliser **`std::vector::emplace_back`** qui crée les instances directement à l'endroit où elles doivent être stockées, sans utiliser de déplacement ou de copie intermédiaires d'objets.

# Le petit sujet en C++

Pour tester ces 2 méthodes on va créer une structure **Image** qui contient 2 membres : **name** et **type**.
Pour suivre l'utilisation des constructeurs par copie et par déplacement, on va implémenter le construsteur par copie et le constructeur par déplacement et afficher le texte *Copied!* ou *Moved!*.
Vous remarquerez que pour le constructeur par déplacement : **`Image(const Image&& image) noexcept`**, j'utilise le mot clé **noexcept** qui remplit les conditions nécessaires pour que `std::vector` utilise le constructeur par déplacement plutôt que celui par copie, pour réduire la consommation en temps et en mémoire. ([voir la documentation de `std::vector::emplace_back`](http://en.cppreference.com/w/cpp/container/vector/emplace_back))

## Avec std::vector::emplace_back

Avec **`std::vector::emplace_back`**, pour stocker nos **3 objets** dans notre conteneur, on utilise 3 fois le constructeur par déplacement.
En optimisant le code avec `images.reserve(3)`, **on ne fait pas appel au constructeur par déplacement** et l'instance est créée directement à l'emplacement prévu.
Lorsqu'on fournit le nombre d'élément dont on a besoin, le programme n'a pas besoin de réserer des objets vides dans notre conteneur (`std::vector::capacity`).
Ça semble parfait.

```bash
# Résultat avec std::vector::reserve

```

```bash
# Résultat sans std::vector::reserve
Moved!
Moved!
Moved!
```

```cpp
#include <iostream>
#include <string>
#include <vector>

struct Image {
  std::string name;
  std::string type;
  
  Image(std::string name, std::string type = "png"): name{name}, type{type} {}
  Image(const Image& image): name{image.name}, type{image.type}
  {
    std::cout << "Copied!" << std::endl;
  }
  Image(const Image&& image) noexcept : name{std::move(image.name)}, type{std::move(image.type)}
  {
    std::cout << "Moved!" << std::endl;
  }
};

int main()
{
  std::vector<Image> images;

  images.reserve(3);
  images.emplace_back("destiny_2.png");
  images.emplace_back("transformers_forged-to-fight.png");
  images.emplace_back("cat.gif", "gif");

  return EXIT_SUCCESS;
}

```

## Avec std::vector::push_back

Avec `std::vector::push_bak`, pour stocker nos **3 objets**, on utilise **6 fois le constructeur par déplacement** ; *Kezako* !

En plus des objets temporaires utilisés dans le contexte de la fonction `int main();`, des instances sont préparées pour se loger dans les slots alloués dynamiquement par `std::vector::capacity`.

```bash
# Résultat
Moved!
Moved!
Moved!
Moved!
Moved!
Moved!
```

```cpp
#include <iostream>
#include <string>
#include <vector>

struct Image {
  std::string name;
  std::string type;
  
  Image(std::string name, std::string type = "png"): name{name}, type{type} {}
  Image(const Image& image): name{image.name}, type{image.type}
  {
    std::cout << "Copied!" << std::endl;
  }
  Image(const Image&& image) noexcept : name{std::move(image.name)}, type{std::move(image.type)}
  {
    std::cout << "Moved!" << std::endl;
  }
};

int main()
{
  std::vector<Image> images;

  images.push_back(Image{"destiny_2.png"});
  images.push_back(Image{"transformers_forged-to-fight.png"});
  images.push_back(Image{"cat.gif", "gif"});

  return EXIT_SUCCESS;
}

```

À bientôt !
