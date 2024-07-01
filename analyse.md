
# Modèle CNN (Convolutional Neural Network)

Le CNN est un modèle de deep learning utilisant des réseaux de neurones artificiels avec plusieurs couches. Il est particulièrement efficace pour le traitement d’images ou de vidéos, ce qui le rend très adapté à notre cas d’usage.

## Structure du Modèle

Notre CNN est composé de plusieurs couches convolutives, de normalisation par lot, de couches d’activation LeakyRelu, de couches de maxpooling ainsi que de dropout pour réduire le surapprentissage.

### Blocs de Convolution

Chaque bloc de convolution comprend :
- **Conv2D** : 32, 64 ou 128 filtres pour extraire des caractéristiques comme les angles et les coins.
- **BatchNormalization** : Recentre les valeurs d’activation entre 0 et 1, stabilise et accélère l’apprentissage.
- **LeakyRelU** : Ajoute de la non-linéarité pour combiner les features détectées.
- **MaxPooling2D** : Redimensionne les données pour réduire les dimensions.
- **Dropout** : Désactive aléatoirement 30% des neurones pour améliorer la généralisation et éviter le surapprentissage.

![Schéma du modèle CNN](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/8bb3cd91-f959-4c81-b197-84e5a890f873)

### Partie Catégorisation

Après les blocs de convolution, nous procédons à la catégorisation avec :
- **Flatten** : Convertit les tableaux de features de 3D en 1D.
- **Dense (512 neurones)** : Chaque neurone reçoit les informations du précédent.
- **BatchNormalization** : Stabilise et accélère à nouveau l’apprentissage.
- **LeakyRelU** : Ajoute de la non-linéarité et combine les features.
- **Dropout** : Désactive 50% des neurones cette fois.
- **Dense (Softmax)** : Fournit une distribution de probabilité pour chaque classe.

![Architecture du modèle](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/10efb662-7b7a-43e7-9061-22abf2467839)

## Compilation du Modèle

Nous compilons ce modèle avec :
- **Optimizer** : Adam, qui ajuste les poids en utilisant le principe de backpropagation.
- **Loss Function** : `categorical_crossentropy`, efficace pour la classification de plusieurs classes.
- **Métrique** : `accuracy` pour évaluer la performance du modèle pendant et après l’entraînement.

## Préparation des Données

Nous réorganisons nos datasets pour obtenir une homogénéité des données de base :

Train (50%):
- VIRUS : 747
- BACTERIA : 747
- NORMAL : 747

Validation (35%) :
- VIRUS : 523
- BACTERIA : 523
- NORMAL Train : 523

Test (15%) :
- VIRUS : 223
- BACTERIA : 223
- NORMAL : 223

### Génération et Augmentation des Données

Nous créons deux générateurs :
- **Validation** : Normalise les données de validation.
- **Entraînement** : Augmente les données d'entraînement avec divers paramètres :
  - Rotation de l’image +/- 10°
  - Décalage Horizontal et Vertical de +/- 30%
  - Remplissage des pixels manquants par les plus proches
  - Étirement des images de +/- 20%
  - Zoom aléatoire jusqu’à 20%
  - Translation horizontale aléatoire
  - Augmentation ou réduction de la luminosité de +/- 30%

![Exemples d'augmentation des données](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/8bb35859-02f0-47ca-8296-46bc2c55858c)


Nous redimensionnons ensuite nos images en 190x190 pour réduire leur taille et accélérer l’entraînement, organisant notre génération en batchs de 15 images.

## Entraînement du Modèle

Nous définissons des callbacks qui ajusteront l’apprentissage en fonction de l’évolution de `validation_loss` et nous entraînons le modèle sur 20 epochs.

![Définition des callback et du setup d'entrainement](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/9298088e-8f4d-44e6-9e24-095178bf0a21)

![Rapport d'entrainement](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/b5e84aeb-7e08-4904-98c0-9a3f5c980893)

### Analyse des Performances

![Training_accuracy](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/d531ec63-c01c-4501-93bd-18e50ebf2c95)
![Training_loss](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/f5707d84-e5e3-41ae-a8fd-46380f50a5fc)

- **Entraînement (bleu)** : L’accuracy augmente et la loss diminue progressivement, montrant que le modèle apprend bien.
- **Validation (orange)** : L’accuracy fluctue, indiquant une instabilité, et la loss fluctue également.

#### Raisons des Fluctuations :
- **Taille de l'ensemble de validation** : Un petit ensemble peut entraîner des fluctuations importantes.
- **Taux d'apprentissage** : Un taux trop élevé peut entraîner des variations d’une époque à l’autre.
- **Surapprentissage** : Peut faire perdre au modèle sa capacité à généraliser.

## Conclusion

Le modèle est performant pour l’apprentissage mais instable sur les données de validation. Il est nécessaire d’ajuster les hyperparamètres, d’ajouter des couches de régularisation ou de dropout pour améliorer la généralisation.

### Évaluation sur les Données de Test

![test_report](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/c1466c03-8bd6-484b-9899-8b647b6bf10b)


- **Précision** : 73%, ce qui est relativement faible.
- **Loss** : 1.36, indiquant un manque de performance sur des données non vues.

### Matrice de Confusion Catégorisation en 3 classes : Normal, Bacterie, Virus

![Matrice de confusion](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/b6109619-bc67-4e91-af0f-6ab50766a629)
![Détail des classes](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/5f9d6a41-76f5-4c7d-acbe-856f903215c7)

- **Précision par classe** :
  - Bactéries : 67%
  - Poumons normaux : 76%
  - Pneumonies virales : 76%

![Méthode de calcul de précision](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/0f60dc0e-1f13-4cbf-898e-8846ae432f4d)

- **Recall par classe** :
  - Bactéries : 73%
  - Normales : 94%
  - Virus : 51%

![Méthode de calcul de recall](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/d0d3dd2f-112a-46c1-bfd7-84ce00d53408)

On a donc une précision globale de 73% pour une catégorisation en 3 classes.

On va donc maintenant rassembler les catégories Virus/Bactéries en Pneumonie 

### Matrice de Confusion Catégorisation en 2 classes : Normal, Pneumonie

![Matrice de confusion](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/f527573e-8b90-471f-8912-d234db60efc5)
![Détail des classes](https://github.com/ArrnaudMoreau22/Convolutional_neural_network/assets/113347237/684306d2-9115-454a-9927-8f56d4aebf08)

- **Précision par classe** :
  - Pneumonies : 97%
  - Poumons normaux : 76%

- **Recall par classe** :
  - Pneumonies : 85%
  - Poumons normaux : 94%


Précision globale de 73% pour une catégorisation en 3 classes.

Nous analysons ensuite la performance du modèle en regroupant les pneumonies virales et bactériennes. La nouvelle matrice montre :

Poumons normaux : 94% identifiés correctement mais avec beaucoup de faux positifs.
Poumons atteints de pneumonie : 85% détectés, mais les détectés le sont à 97%. Peu de faux positifs mais beaucoup de faux négatifs.
Précision de 88% pour la classification entre poumons sains et poumons atteints de pneumonie.


### Conclusion Finale

Le CNN semble prometteur pour la classification des scanners de poumons d’enfants atteints de pneumonie. Il permet également de dissocier les pneumonies en deux classes (virales et bactériennes). Cependant, il manque de précision pour la classification des pneumonies virales. Un approfondissement du travail sur l’apprentissage et les hyperparamètres est nécessaire pour améliorer la performance globale.

---

Pour toute question ou suggestion, veuillez ouvrir une issue ou soumettre une pull request.

---

Merci d'avoir consulté ce README !
