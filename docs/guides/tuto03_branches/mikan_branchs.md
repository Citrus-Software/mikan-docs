# Les Branches dans Mikan

## Introduction

Une **branch** dans Mikan est un paramètre qui permet de créer plusieurs variantes d'un même module de template.  

Exemples typiques :

- symétrie gauche/droite pour les bras et les jambes,  
- duplication d'éléments répétés comme des tentacules ou des pattes.  

👉 Les branches garantissent la **cohérence** et simplifient le rigging en évitant les duplications manuelles.


## Principes de base

### Définir une branch

Le champ **Branches** se trouve dans les options du module.  

![options branchs](./img/option_branchs.png)  

1. Écrivez manuellement une liste de labels séparés par des virgules.  
   - Exemple : `[L, R]`, `[up, dn]`, `[1, 2, 3, 4]`  
2. Ou bien, faites un **clic droit** pour accéder à des labels **pré-définis** :  

![options branchs 02](./img/option_branchs_02.png)  

:::note
Si le champ est vide, Mikan ne crée **qu’un seul module**, sans duplication ni symétrie.  
Sur certains modules de template où la symétrie est évidente (par exemple les **bras** et les **jambes**), ce champ est déjà **pré-rempli** par défaut avec `[L, R]`. Vous pouvez bien sûr modifier ou remplacer ces valeurs.
:::

Au moment du **build du rig** :

Chaque label défini génère automatiquement une variante du module,  
Exemple : `[L, R]` → un module côté gauche et un module côté droit.  


### Notes et Modifiers

Lorsque vous utilisez des branches, vous n'avez pas besoin de répéter vos notes ou modifiers pour chaque côté.  

Exemple :

1. Vous travaillez sur un module de bras avec une branche `[L, R]`.  
2. Saisissez vos notes en utilisant les **IDs du côté gauche** (par exemple les contrôleurs du bras gauche).  
3. Mikan répliquera automatiquement cette logique en miroir pour le côté droit lors du build.  

![notes branchs](./img/branch_notes.png)  

Cela signifie :

- pas besoin de copier les notes pour le côté droit,  
- un setup **propre, cohérent et facile à maintenir**, même sur des rigs complexes.  


## Adapter les branches à un rig asymétrique

Imaginons un personnage dont le bras droit est plus gros, plus long.  

![modeling assymetrique](./img/modeling_assymetrique.png)  

Même si les deux bras viennent du **même module avec branches**, vous avez la possibilité d'aller modifier le module de template uniquement pour le côté **R** :  

1. Faites un clic droit sur le module concerné.  
2. Choisissez **Build Branches Template**.  

![build branch template](./img/build_branches.png)  

Mikan génère alors un **module branch** (par exemple `R` si vos branches sont `[L, R]`).  

![branch R](./img/branch_R.png)  

Vous pouvez ensuite modifier directement :

- la **position des joints**,  
- l'**aspect des shapes** (via le bouton *Toggle Shapes*).  

![edition branch R](./img/edition_branchR.png)  

---

:::warning
N'utilisez pas de branches si les variantes sont **structurellement différentes**.  
Exemple : un robot dont le bras gauche et le bras droit ont des mécaniques distinctes.  
👉 Dans ce cas, créez **deux modules séparés**, sans branches.
:::


## Exemple avancé : l'araignée

Les pattes d'araignée sont un **cas typique d’utilisation des branches** : elles sont nombreuses, mais partagent toutes le même comportement.  

### Mise en place du template de base

Commençons par créer un template très simple, composé de :

- un module **WORLD**,  
- un module **joint** pour le corps,  
- un module **bones** pour l'arrière de l’araignée.  

![base template](./img/base_araignee.png)  


### Créer un groupe pour les pattes

Créons ensuite un **modules group** pour rassembler toutes les pattes (*legs*).  

1. Ajoutez un group nommé **legs**.  
2. Définissez un set de branches : **A, B, C, D** pour les quatre pattes du côté gauche.  

![édition branch A B C D](./img/araignee_branchsABCD.png)  


### Ajouter un module quad

Ajoutons maintenant un **module quad** classique, configuré avec les branches `[L, R]`.  

![quad](./img/araignee_quad.png)  


### Edition des branches

Pour éditer les branches :  

1. Faites un clic droit sur le group **legs**.  
2. Sélectionnez **Build Branches Template**.  

Par défaut, Mikan génère des branches pour les deux côtés `[L, R]`.  
Mais dans notre cas, nous voulons uniquement editer les branches du côté gauche (**A_L, B_L, C_L, D_L**), et garder la symétrie à droite :  

3. Dans l'Outliner, sélectionnez les branches côté **R**.  
4. Supprimez-les avec l'icône **poubelle** 🗑️.  

![delete R](./img/delete_branchesR.png)  

5. Placez et ajustez ensuite les branches du côté gauche.  

![placement branches](./img/araignee_placementBranches.png)  


### Lancer un build de test

Enfin, lançons un build pour vérifier le résultat :  

![resultat](./img/araignee_resultat.png)  

Le rig est généré correctement sur toutes les pattes, côté gauche **et** côté droit.  
Et les fonctionnalités accessibles par clic droit fonctionnent aussi parfaitement.  


## Bonnes pratiques et astuces

- Utilisez des labels **clairs et courts** (`L/R`, `up/dn`, `1/2/3/4` …).  
- Ne multipliez pas les branches inutilement.  
- Vérifiez toujours le build avant de passer à l'étape suivante.  
- Pour des rigs complexes, privilégiez les branches > maintenance facilitée.  


## Résumé rapide

- **Champ Branches vide** > un seul module.  
- **Labels définis** > une variante par label.  
- **Notes/modifiers** > définis une fois, répliqués automatiquement.  
- **Asymétrie** > utiliser *Build Branches Template*.  
- **Cas trop différents** > créer des modules séparés.  
