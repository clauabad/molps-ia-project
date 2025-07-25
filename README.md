# molps-ia-project

## Structure du projet

```
lab4-mlops/

├── configs/                # Configuration des modèles au format YAML
├── data/                   # Raw and processed datasets
├── deployment/
│   └── mlflow/             # Configuration Docker Compose pour MLflow
├── models/                 # Modèles entraînés et préprocesseurs
├── notebooks/              # Notebooks Jupyter optionnels pour l’expérimentation
├── src/
│   ├── data/               # Scripts de nettoyage et de prétraitement des données
│   ├── features/           # Pipeline d’ingénierie des variables
│   ├── models/             # Entraînement et évaluation des modèles
├── requirements.txt        # Dépendances Python
└── README.md               # Vous êtes ici !
```

## Prérequis

Assurez-vous que les outils suivants sont installés sur votre système :

- Python 3.11  
- Git  
- Visual Studio Code ou l’éditeur de votre choix  
- Docker Desktop

## Préparation de votre environnement

Cloner le dépôt :

```bash
git clone https://github.com/clauabad/molps-ia-project.git
cd mlops-ia-project
```

Configurer un environnement virtuel Python avec `uv` :

```bash
python3.11 -m venv .venv
source .venv/bin/activate
```

Installer les dépendances :

```bash
uv pip install -r requirements.txt
```

## Configuration de MLflow pour le suivi des expérimentations

Pour suivre les expérimentations et les exécutions de modèles, installez MLflow séparément pour pouvoir choisir le meilleur modèle :

```bash
cd deployment/mlflow
docker compose up -d
docker compose ps
```

Accédez à l’interface MLflow à l’adresse : [http://localhost:5555](http://localhost:5555)

## Flux de travail du modèle

### Étape 1 : Traitement des données

Nettoyez et prétraitez le jeu de données brut sur l’habitation :

```bash
python src/data/run_processing.py \
  --input data/raw/house_data.csv \
  --output data/processed/cleaned_house_data.csv
```

### Étape 2 : Ingénierie des variables

Appliquez les transformations et générez les variables :

```bash
python src/features/engineer.py \
  --input data/processed/cleaned_house_data.csv \
  --output data/processed/featured_house_data.csv \
  --preprocessor models/trained/preprocessor.pkl
```

### Étape 3 : Modélisation et expérimentation

Entraîner le modèle et enregistrez toutes les informations dans MLflow :

```bash
python src/models/train_model.py \
  --config configs/model_config.yaml \
  --data data/processed/featured_house_data.csv \
  --models-dir models \
  --mlflow-tracking-uri http://localhost:5555
```

## Construire les images Docker

Dans le dossier principal du projet :

```bash
docker compose build
```

Valider la création :

```bash
docker image ls
```

Lancer le tout comme un ensemble de services (stack) :

```bash
docker compose up -d
```
