# TP 8 — Microservices Architecture avec Spring Cloud  
## Configuration centralisée (Config Server Repository)

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.8-brightgreen)
![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2025.0.0-blue)
![Java](https://img.shields.io/badge/Java-17-orange)
![Build](https://img.shields.io/badge/Build-Maven-blue)
![Status](https://img.shields.io/badge/Status-Working-success)
![License](https://img.shields.io/badge/License-MIT-blue.svg)

Ce dépôt représente le **repository de configuration centralisée** utilisé par le **Config Server** dans le cadre du **TP 8 — Microservices Architecture avec Spring Cloud**.

Il contient l’ensemble des fichiers de configuration partagés par les micro-services de l’application **Bank App (MSA)**.

---

## 📌 Sommaire

1. [Présentation](#-présentation)
2. [Micro-services concernés](#-micro-services-concernés)
3. [Structure du dépôt](#-structure-du-dépôt)
4. [Stack technique](#-stack-technique)
5. [Fonctionnement du Config Server](#-fonctionnement-du-config-server)
6. [Test du Refresh Config](#-test-du-refresh-config)
7. [Démarrage global de l’architecture](#-démarrage-global-de-larchitecture)
8. [Auteurs](#-auteurs)
9. [Licence](#-licence)

---

## 📖 Présentation

Dans une architecture **microservices**, la gestion des configurations distribuées est essentielle.<br/>

Ce dépôt est utilisé par **Spring Cloud Config Server** afin de :<br/>
- Centraliser les fichiers `.properties`
- Externaliser la configuration des micro-services
- Permettre le **rechargement dynamique** des propriétés via `/actuator/refresh`

---

## 🧩 Micro-services concernés

| Micro-service | Port | Description |
|---------------|------|-------------|
| discovery-service | 8761 | Service Eureka (Service Registry) |
| config-service | 8888 | Config Server |
| gateway-service | 9999 | API Gateway |
| customer-service | 8084 | Gestion des clients |
| account-service | 8083 | Gestion des comptes bancaires |

---

## 🗂️ Structure du dépôt

```
msa-bank-config/
├─ application.properties
├─ customer-service.properties
├─ account-service.properties
└─ README.md
```

### Description des fichiers

- **application.properties**  
  Configuration globale Spring Cloud, Eureka, Actuator, Swagger

- **customer-service.properties**  
  Configuration spécifique au micro-service Customer  
  (datasource H2 + paramètres métier)

- **account-service.properties**  
  Configuration spécifique au micro-service Account  
  (datasource H2 + paramètres techniques)

---

## 🛠️ Stack technique

| Technologie | Version |
|------------|--------|
| Java | 17 |
| Spring Boot | 3.5.8 |
| Spring Cloud Config | 2025.0.0 |
| GitHub | Repository distant |
| Maven | ✅ |

---

## ⚙️ Fonctionnement du Config Server

1. Le **Config Server** démarre
2. Il lit ce repository GitHub
3. Les micro-services clients chargent leur configuration via :
   ```properties
   spring.config.import=optional:configserver:http://localhost:8888
4. Les propriétés sont injectées automatiquement au démarrage

---

## 🔁 Test du Refresh Config

### 1️⃣ Modifier une propriété

Exemple dans customer-service.properties :<br/>
```
customer.service.default.location=Paris
customer.service.default.currency=EUR
customer.service.default.language=French
```
➡️ Commit & Push sur GitHub

### 2️⃣ Recharger la configuration (sans redémarrage)<br/>
```
POST http://localhost:8084/actuator/refresh
```

### 3️⃣ Vérifier la prise en compte<br/>

```
GET http://localhost:8084/configTest
```

#### Résultat attendu :<br/>
```
{
  "defaultLocation": "Paris",
  "defaultCurrency": "EUR",
  "defaultLanguage": "French"
}
```
✅ Rechargement dynamique confirmé.<br/>

---

## 🚀 Démarrage global de l’architecture

### Ordre de lancement (OBLIGATOIRE) : <br/>

1. discovery-service → http://localhost:8761
2. config-service → http://localhost:8888
3. gateway-service → http://localhost:9999
4. customer-service → http://localhost:8084
5. account-service → http://localhost:8083



### 🔗 Tests des endpoints <br/>
Accès direct<br/>

- Customers :
```
http://localhost:8084/customers
```
- Accounts :
```
http://localhost:8083/api/accounts
```
Accès via Gateway (MAJUSCULE obligatoire)<br/>
- Customers :
```
http://localhost:9999/CUSTOMER-SERVICE/customers
```
- Accounts :
```
http://localhost:9999/ACCOUNT-SERVICE/api/accounts
```

### 🛡️ Test Resilience4J (Circuit Breaker)<br/>

1. Arrêter customer-service
2. Appeler un compte via Gateway :
```
http://localhost:9999/ACCOUNT-SERVICE/api/accounts/{id}
```
3. Résultat attendu :
```
{
  "firstName": "Source not available",
  "lastName": "Source not available",
}
```
✅ Fallback Resilience4J fonctionnel.<br/>

---

## 👥 Auteurs

Anas KRIR & Adam EL YOURI<br/>
Étudiants Ingénieurs — MIAGE<br/>
Implémentation complète de l’architecture micro-services<br/>
Spring Boot · Spring Cloud · Eureka · Gateway · Config · Feign · Resilience4J <br/>

---

## 📄 Licence

✅Projet sous licence MIT.<br/>
Libre d’utilisation, modification et distribution à des fins pédagogiques.<br/>

© 2025 — Anas KRIR & Adam EL YOURI<br/>

---

