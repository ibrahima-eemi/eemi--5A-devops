# Kubernetes Deployment Project - TP1

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)

## Objectif

Ce projet déploie une architecture Kubernetes composée de deux services conteneurisés :

- **Backend Node.js** : Basé sur l'image `barry/demo-node:latest`, exposant une application web sur le port 3000
- **Frontend Nginx** : Basé sur `nginx:alpine`, configuré comme reverse proxy vers le backend Node.js avec exposition `LoadBalancer`

L'ensemble est configuré via `ConfigMap` et labellisé pour une gestion optimisée.

## Architecture

```txt

                    ┌─────────────────┐             ┌─────────────────┐
                    │   Nginx Pod     │ ClusterIP   │   Node.js Pod   │
Internet ─────────► │   (Frontend)    │◄───────────►│   (Backend)     │
                    │  nginx:alpine   │  proxy_pass │  node:alpine    │
                    └─────────────────┘             └─────────────────┘
                            ▲
                            │ LoadBalancer (Port 80)
                            ▼
                     [ External IP ]
```

## Structure du Projet

| Fichier | Description |
|---------|-------------|
| `configmap.yaml` | Variables d'environnement (`PORT`, `PROXY_URL`) |
| `nginx-configmap.yaml` | Configuration Nginx |
| `deployment-node.yaml` | Déploiement du backend Node.js |
| `service-node.yaml` | Service interne pour le backend |
| `deployment-nginx.yaml` | Déploiement de Nginx |
| `service-nginx.yaml` | Service LoadBalancer pour le frontend |

## Déploiement

### Installation

```bash
# Se placer dans le dossier du projet
cd kube-tp1

# Appliquer tous les fichiers de configuration
kubectl apply -f .

# Vérifier le déploiement
kubectl get all -l app=kube-tp1
```

### Accès à l'Application

Une fois les pods en état **Running** :

```bash
# Accéder via l'IP externe du LoadBalancer
http://<EXTERNAL-IP>

# Exemple
http://10.0.10.242

# Alternative avec port-forward si pas d'IP externe
kubectl port-forward service/kube-tp1-nginx-service 8080:80
# Puis accéder à http://localhost:8080
```

## Vérification et Diagnostic

```bash
# Consulter les logs des pods
kubectl logs <pod-name>

# Filtrer les ressources par label
kubectl get all -l app=kube-tp1

# Test de connectivité interne
kubectl exec -it <pod-name> -- curl <service-name>
```

## Nettoyage

```bash
# Supprimer toutes les ressources du projet
kubectl delete -f .
```

## Remarques

- Le projet respecte les contraintes imposées : images spécifiées, configuration via ConfigMap, exposition via LoadBalancer
- L'image Node.js personnalisée `barry/demo-node:latest` est chargée dans le cluster local

## Troubleshooting

| Problème | Solution |
|----------|----------|
| Pods en état CrashLoopBackOff | Vérifier les logs avec `kubectl logs <pod-name>` |
| Service inaccessible | Vérifier les labels et selectors dans les fichiers YAML |
| Erreur ConfigMap | Vérifier la correspondance des noms référencés |

## Évolutions Possibles

- Ajout d'un service de base de données
- Configuration de la persistance des données
- Mise en place d'un Ingress pour gérer plusieurs domaines
- Configuration des ressources CPU/mémoire
- Implémentation des probes de santé

## Références

- [Documentation Kubernetes](https://kubernetes.io/docs/home/)
- [Documentation Nginx](https://nginx.org/en/docs/)
- [Documentation Node.js](https://nodejs.org/en/docs/)

## Auteur

Ibrahima
