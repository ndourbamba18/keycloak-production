# Déploiement de Keycloak en environnement de production via Docker Compose

## 🔐 Qu'est-ce que Keycloak ?

Keycloak est une solution open-source de gestion des identités et des accès (IAM - Identity and Access Management), développée par Red Hat.  Il facilite la mise en place de l'authentification unique (SSO) pour les applications modernes en gérant des protocoles tels que OAuth2, OpenID Connect (OIDC) et SAML.

## 🔍 Keycloak vs JWT & Autres Solutions d'Authentification

| Solution          | Avantages                                      | Limitations                              |
|-------------------|-----------------------------------------------|------------------------------------------|
| **Keycloak**      | SSO centralisé, gestion des rôles, multi-protocoles | Nécessite un serveur dédié              |
| **JWT (Stateless)** | Léger, facile à implémenter                 | Pas de révocation facile des tokens     |
| **Firebase Auth** | Simple, intégration Google/Apple            | Dépendance à un service tiers (Google)  |
| **Auth0**         | SaaS clé en main, haute disponibilité       | Coût élevé à grande échelle             |

### Pourquoi choisir Keycloak ?
- ✅ Open-source et auto-hébergé
- ✅ Support multi-protocoles (OAuth2, OIDC, SAML)
- ✅ Gestion centralisée des utilisateurs et rôles
- ✅ Intégration facile avec Docker/Kubernetes

## 🚀 Installation de Keycloak via Docker Compose

### 1. Prérequis
- Docker & Docker Compose installés
- Un domaine (optionnel pour HTTPS)

### 2. Fichier `docker-compose.yml`
```yaml
version: "3.7"

services:
  keycloak:
    container_name: keycloak_sso
    image: quay.io/keycloak/keycloak:latest
    restart: always
    ports:
      - "8443:8443"
    volumes:
      - "/etc/ssl/keycloak/:/etc/x509/https" # map certificates to container
    environment:
      KEYCLOAK_ADMIN: ${KEYCLOAK_USER}
      KEYCLOAK_ADMIN_PASSWORD: ${KEYCLOAK_PASSWORD}
      KC_HOSTNAME: ${KEYCLOAK_URL}
      KC_DB_URL: jdbc:postgresql://keycloak_postgres:5432/keycloak
      KC_DB: postgres
      KC_DB_USERNAME:  ${POSTGRES_USER}
      KC_DB_PASSWORD: ${POSTGRES_PASSWORD}
      KC_HTTPS_CERTIFICATE_FILE: /etc/x509/https/tls.crt
      KC_HTTPS_CERTIFICATE_KEY_FILE: /etc/x509/https/tls.key
    depends_on:
      - keycloak_postgres
    networks:
      - keycloak-network
    command:
      - 'start'

  keycloak_postgres:
    container_name: keycloak_postgres
    image: postgres:latest
    restart: always
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_PORT: 5432
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - keycloak-network


networks:
  keycloak-network:
    name: keycloak-network
    driver: bridge

volumes:
  postgres_data:

```
### 3. Démarrer Keycloak

```
docker-compose -f docker-compose.yaml up -d
```

### Accès à l'interface admin :

- 🌐 http://server_ip_address:8080
- 👤 Identifiants : admin / admin

## 🖥 Démonstration de l'Interface Keycloak


### 1. Connexion à l'Admin Console
https://via.placeholder.com/600x400?text=Keycloak+Login+Screen

### 2. Création d'un Realm
Un Realm est un espace isolé pour gérer utilisateurs et applications.
https://via.placeholder.com/600x400?text=Create+Realm+Keycloak

### 3. Configuration d'un Client (Application)
Ajoutez un client pour une application avec OpenID Connect.
https://via.placeholder.com/600x400?text=OIDC+Client+Setup

### 4. Ajout d'Utilisateurs et Rôles
Créez des utilisateurs et assignez-leur des permissions.
https://via.placeholder.com/600x400?text=User+Roles+in+Keycloak

#💡 Pourquoi Docker Compose en Production ?
- Isolation : Conteneurisation sécurisée

- Scalabilité : Montée en charge facile

- Persistance : Volume Docker pour les données

## ⚠ Bonnes Pratiques
- Utilisez HTTPS (Let's Encrypt)

- Sauvegardez régulièrement le volume keycloak_data

- Configurez une base de données externe (PostgreSQL/MySQL)

# 🔚 Conclusion
Keycloak est une solution puissante et flexible pour gérer l'authentification centralisée. Son déploiement via Docker Compose simplifie l'intégration en production tout en garantissant scalabilité et sécurité.

## 🚀 Prêt à essayer ?

```
git clone https://github.com/ndourbamba18/keycloak-production.git

```
``` 
docker-compose -f docker-compose.yaml up -d

``` 
# 📌 #Keycloak #SSO #Docker #DevOps #Cybersécurité



