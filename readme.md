# Infrastructure - Prometheus et Grafana

![Bannière Loutik](https://raw.githubusercontent.com/loutik/design-assets/main/banniere_loutik.png)

## Contexte

Ce dépôt contient le Docker Compose utilisé pour la supervision du projet Loutik. Il orchestre Prometheus pour la collecte et le stockage des métriques ainsi que Grafana pour leur visualisation.

Le choix de Docker Compose simplifie le déploiement de la stack par Ansible sur une machine virtuelle. Ce dépôt sert également de base de référence à Ansible pour déployer Grafana en production sur la VM de monitoring. Par défaut, Grafana est configuré pour authentifier les utilisateurs via le SSO du projet. Les données Prometheus sont conservées pendant 15 jours dans le volume Docker, tandis que la configuration de Grafana est sauvegardée dans une instance PostgreSQL située à un autre endroit de l’infrastructure.

La configuration fournie est destinée à l’environnement de développement. Le fichier `prometheus/prometheus.yml` doit être complété avec les cibles de supervision nécessaires avant toute utilisation fonctionnelle de Prometheus.

-----

## Structure du dépôt

L’organisation du dépôt suit la logique suivante :

```text
.
├── .env.example
├── docker-compose.yaml
├── prometheus/
│   └── prometheus.yml
├── LICENSE.md
└── readme.md
```

- **`.env.example`** : exemple des variables d’environnement nécessaires à Grafana, notamment pour le SSO OAuth et PostgreSQL.
- **`docker-compose.yaml`** : définition des services Prometheus et Grafana, de leur réseau, de leurs volumes et de leurs limites de ressources.
- **`prometheus/prometheus.yml`** : configuration Prometheus, montée en lecture seule dans le conteneur.
- **`LICENSE.md`** : licence Creative Commons Attribution-ShareAlike 4.0 International.

-----

## Utilisation en développement

### 1. Cloner le dépôt localement

```bash
git clone https://github.com/loutik/infrastructure-prometheus-grafana.git
cd infrastructure-prometheus-grafana
```

### 2. Préparer les variables d’environnement

Copier le modèle avant de démarrer les conteneurs :

```bash
cp .env.example .env
```

Le fichier `.env.example` contient des paramètres d’intégration au SSO et à PostgreSQL. Pour un environnement de développement, remplacer les valeurs entre accolades par des identifiants de test et utiliser les endpoints autorisés pour cet environnement. Ne jamais versionner `.env` ni y placer de secret de production.

### 3. Vérifier la configuration Compose

```bash
docker compose config
```

Cette commande valide la syntaxe du fichier Compose et la présence des variables référencées.

### 4. Démarrer la supervision

```bash
docker compose up -d
```

Les interfaces sont alors accessibles sur :

- Grafana : <http://localhost:3000>
- Prometheus : <http://localhost:9090>

Les ports doivent être protégés ou remappés si la machine est accessible depuis un réseau non fiable.

### 5. Consulter les journaux et arrêter les services

```bash
docker compose logs -f prometheus grafana
docker compose down
```

Pour supprimer également les volumes persistants et les données locales :

```bash
docker compose down -v
```

-----

## Bonnes pratiques

1. **Authentification centralisée** : conserver l’authentification Grafana via le SSO et vérifier que les groupes OAuth correspondent aux rôles attendus (`Admin` et `Viewer`).
2. **Protection des secrets** : ne jamais committer `.env`, les secrets OAuth ou le mot de passe PostgreSQL. Utiliser un gestionnaire de secrets dans les déploiements automatisés.
3. **Images immuables** : conserver les images Docker épinglées par digest et valider toute mise à jour avant déploiement.
4. **Accès réseau limité** : exposer Grafana et Prometheus derrière un reverse proxy avec TLS et limiter l’accès aux ports locaux ou au réseau de supervision.
5. **Validation avant déploiement** : vérifier la configuration et l’état des services avant toute mise en production.

```bash
docker compose config --quiet
docker compose ps
```

-----

## 👨‍💻 Mainteneurs

- **Louis MEDO** | [LinkedIn](https://www.linkedin.com/in/louismedo/) | [Portfolio](https://louis.loutik.fr/) | [GitHub](https://github.com/FireToak) | [louis.medo@loutik.fr](mailto:louis.medo@loutik.fr)

-----

<div align="center">
<br>
<small><i>Dernière mise à jour : 20 août 2026</i></small>
</div>