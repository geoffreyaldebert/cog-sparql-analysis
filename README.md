# Test du endpoint SPARQL INSEE

Le but est de pouvoir parcourir les communes et leur historique depuis le end-point SPARQL de l'INSEE pour reconstituer un graphe des évolutions de commune


## Principes pour la constitution du graphe :

Chaque commune possède un identifiant unique : TYPE-CODEINSEE@DATE_EVT_CREATION

Exemple :
Tartampion 27455 devient Tartampion 27455 le 1er janvier 1970
à considérer comme deux objets distincts même si le cas semble bizarre, du point de vue de la base de données, il faut le considérer comme deux objets distincts. Ici, avec deux identifiants :
COM-27455@1943-01-01 
COM-27455@1970-01-01

Pour gérer l'historique et le graphe, nous avons besoin de créer deux nouveaux types de commune :
- COMP : commune Périmée : une fois qu'une commune est supprimée mais n'a pas de successeur, il faut créer une COMP
- COMT : commune Transitoire : lorsque l'événement ne permet pas d'associer un successeur ou un prédecesseur unique, nous avons besoin de créer une commune transitoire qui se charge de faire la jointure entre les communes supprimées et communes créées

Quand on parle ci-dessous de création ou de suppression, il s'agit de création ou de suppression d'objet Commune COM (et non pas COMA ou COMD) :

Gestion des événements, plusieurs cas :
- 0 suppression, 1 création : 
    - la commune créé ne possède pas de prédécesseur
- 0 suppression, N création : ok
    - les communes créées ne possèdent pas de prédecesseurs
- 1 suppression, 1 création : ok
    - la commune créé possède un prédécesseur
    - la commune supprimée possède un successeur
- 1 suppression, N création : besoin d'une commune transitoire
    - la commune supprimée a pour successeur la commune transitoire
    - les communes créées ont pour prédecesseur la commune transitoire
    - la commune transitoire créée n'a pas de successeur ni de prédecesseur
- N suppression, 0 création : ok
    - les communes supprimées ont pour chacune un objet COMP (Commune Périmée) a créer.
- N suppression, 1 création : besoin d'une commune transitoire
    - les communes supprimées ont pour successeurs la commune transitoire
    - la commune créée a pour prédecesseur la commune transitoire
    - la commune transitoire créée n'a pas de successeur ni de prédecesseur
- N suppression, N création : besoin d'une commune transitoire
    - les communes supprimées ont pour successeurs la commune transitoire
    - les communes créées ont pour prédecesseurs la commune transitoire
    - la commune transitoire créée n'a pas de successeur ni de prédecesseur

Propriétés des objets communes COM, COMD... :
- nom ; type ; dateDebut ; code ; predecesseur ; id ; idEvt ; successeur
Propriétés des objets commune transitoire COMT :
- nom ; type ; dateDebut ; code ; id ; idEvt


## Guide minimal pour tester

```
# Chargement pyenv en manuel
# Ignoré si pas d'usage de pyenv
eval "$(pyenv init --path)"
```

### Préparation de l'environnement virtuel

```
virtualenv venv --python=python3.9
source venv/bin/activate
```

### Installation des dépendances


```
python -m pip install -U pip
python -m pip install sparqlwrapper
python -m pip install pandas
python -m pip install jupyter
```

### Exécution du notebook

```
source venv/bin/activate
jupyter notebook
```
