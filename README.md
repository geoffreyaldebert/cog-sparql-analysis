# Test du endpoint SPARQL INSEE

Le but est de pouvoir parcourir les communes et leur historique depuis le end-point SPARQL de l'INSEE pour reconstituer un graphe des évolutions de commune

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
