
![Keycloak](https://img.shields.io/badge/Keycloak-Production--Ready-blue?style=for-the-badge&logo=keycloak)
![Docker](https://img.shields.io/badge/Docker-Compose-informational?style=for-the-badge&logo=docker)
![Status](https://img.shields.io/badge/Status-Stable-brightgreen?style=for-the-badge)

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

![Écran de login administrateur Keycloak](images/00.png)

## 🖥 Démonstration de l'Interface Keycloak


### 1. Connexion à l'Admin Console

![Écran de login administrateur Keycloak](images/01.png)

### 2. Création d'un Realm
Un Realm est un espace isolé pour gérer utilisateurs et applications.
![Création d'un nouveau realm](images/webstore_00.png)

![Création d'un nouveau realm](images/webstore_01.png)

### 3. Configuration d'un Client (Application)
Ajoutez un client pour une application avec OpenID Connect.
![Configuration OIDC Client](images/client_00.png)

![Configuration OIDC Client](images/client_01.png)

![Configuration OIDC Client](images/client_02.png)

![Configuration OIDC Client](images/client_03.png)

![Configuration OIDC Client](images/client_04.png)

![Configuration OIDC Client](images/client_05.png)

### 4. Ajout d'Utilisateurs et Rôles
Créez des utilisateurs et assignez-leur des permissions.
![Configuration OIDC Client](images/user_00.png)

![Configuration OIDC Client](images/user_01.png)

![Configuration OIDC Client](images/user_02.png)

### 5. Se connecter en tant que nouvel utilisateur

*Étapes :*  
   - Aller sur `http://localhost:8080/realms/WebStore/account/`
       
   - Saisir les identifiants du nouvel utilisateur 

![Configuration OIDC Client](images/a_00.png)

![Configuration OIDC Client](images/a_01.png)

![Configuration OIDC Client](images/a_02.png)

![Configuration OIDC Client](images/a_03.png)

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



