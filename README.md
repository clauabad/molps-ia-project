# molps-ia-project

## Structure du projet
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


Pour commencer, assurez-vous que les outils suivants sont installés sur votre système :

-Python 3.11

-Git

-Visual Studio Code ou l’éditeur de votre choix

-UV – Gestionnaire de paquets et d’environnements Python

-Docker Desktop

## Préparation de votre environnement


Clonez le repo :

git clone https://github.com/
cd mlops-ia-project

Configurer un environnement virtuel Python avec UV :

uv venv --python python3.11
source .venv/bin/activate

Installer les dépendances :

uv pip install -r requirements.txt

### Configurer MLflow pour le suivi des expérimentations
Pour suivre les expérimentations et les exécutions de modèles, 
on installe mlflow separé pour pouvoir choisir le mellieur modèle.

cd deployment/mlflow
docker compose -f mlflow-docker-compose.yml up -d
docker compose ps

Accédez à l’interface MLflow à l’adresse : http://localhost:5555

### Flux de travail du modèle

- Étape 1 : Traitement des données
Nettoyez et prétraitez le jeu de données brut sur l’habitation :

python src/data/run_processing.py \
  --input data/raw/house_data.csv \
  --output data/processed/cleaned_house_data.csv

- Étape 2 : Ingénierie des variables
Appliquez les transformations et générez les variables :


python src/features/engineer.py \
  --input data/processed/cleaned_house_data.csv \
  --output data/processed/featured_house_data.csv \
  --preprocessor models/trained/preprocessor.pkl

- Étape 3 : Modélisation et expérimentation
Entraînez votre modèle et enregistrez toutes les informations dans MLflow :

python src/models/train_model.py \
  --config configs/model_config.yaml \
  --data data/processed/featured_house_data.csv \
  --models-dir models \
  --mlflow-tracking-uri http://localhost:5555


### Construire les images

On the root of the project

docker compose build
Valider la création
docker image ls
Lancer le tout comme un ensemble de services (stack)
docker compose up -d