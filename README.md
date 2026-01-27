# Security & Gateway Layer
### Важливо: за причини тимчасової відсутності обладнання, я не маю можливості виконати завдання та перевірити роботу системи вчасно. Відповідно, зміни для минулих репозиторіїв, файли та додаткові пояснення будуть надані не пізніше 06.02.2026. Прошу вибачення за незручності!

## Опис проєкту  
До системи астрономічних спостережень Cosmorum додані централізований шар безпеки та доступу:  
- Єдиний Gateway для доступу до всіх мікросервісів  
- Автентифікація користувачів через Google OAuth2  
- Авторизація запитів до API  
- Ендпоїнти для отримання інформації про залогіненого користувача
- Інтеграція security у frontend
- Деплой у Google Cloud Platform

___

## Технологічний стек
- Spring Cloud Gateway  
- Spring Security  
- Google OAuth2  
- JWT  
- React  
- Docker  
- Google Cloud Platform  
- GitHub Actions (CI/CD)  
- Kubernetes  
- RabbitMQ  
- Elasticsearch  
- PostgreSQL  
___

## Архітектура  
Gateway виступає єдиною точкою входу та відповідає за routing до мікросервісів, перевірку JWT, інтеграцію з Google OAuth2, CORS та security headers. Архітектура має такий вигляд:
```
      [Frontend]
          |
          v
    [API Gateway]
          |
          v
   [Authorization]
          |
          v
-------------------------
|  observations-service |
|  email-service        |
-------------------------
          |
          v
      [Backend]

```
___

## Запуск (на поточному етапі, буде змінено)
### Клонування репозиторію (завантаження https://github.com/OlesiaShevchenko245/Internship_Task5.1)  
```
git clone <repository-url>
cd Internship_Task5.1
cp .env.example .env
docker compose up -d --build
```
___

## Компоненти  

### API Gateway (Spring Cloud Gateway)  
Gateway надає:
- Єдиний базовий URL для всієї системи  
- Routing до:  
  - /api/observation/**  
  - /api/author/**  
  - /api/email/**  
- Інтеграцію з Google OAuth2  
- Перевірку токенів  

### Google OAuth2 Authentication  
Використовується Google як Identity Provider:  
- Користувач проходить логін через Google  
- Gateway отримує ID Token  
- Token використовується для доступу до API  
- Без авторизації користувач отримує 401

### Endpoint /profile
Gateway або окремий auth-сервіс надає endpoint:  
```
GET /profile
```
Відповідь (при успіху):  
```
{
  "email": "user@gmail.com",
  "name": "User Name"
}
```
Якщо користувач не залогінений:  
```
HTTP 401 Unauthorized
```
___

## Інтеграція з Frontend  
Frontend таку реалізує логіку:
1. При старті викликає /profile  
2. Якщо отримано 401, показує сторінку Login  
3. Кнопка Login перенаправляє на Google OAuth2  
4. Після логіну користувач повертається у frontend  
5. Frontend знов викликає /profile  
6. При 200 показує основний UI  
Для передачі cookies/session використовується:  
```
credentials: "include"
```
___

## Docker & Docker Compose  
Для системи підготовлено Docker images для:
- Gateway  
- task2-observations-service  
- email-service  
- Frontend  
- RabbitMQ  
- Elasticsearch  
- Kibana  
- PostgreSQL  
docker-compose.yml піднімає всю систему локально.
___

## Google Cloud Platform  
Система деплоїться у Google Kubernetes Engine. Кожен сервіс існує як окремий Deployment, а Gateway - як LoadBalancer Service. 
Також створено Secrets для OAuth2 і SMTP та ConfigMaps для конфігурацій. 

___

## CI/CD  
Використано GitHub Actions:  
Pipeline:  
- Push у main  
- Збірка Docker image  
- Push у Container Registry  
- Автоматичний redeploy у GKE  
- Rolling update сервісів

___

## Безпека  
Для безпеки даних системи зроблені такі рішення:  
- OAuth2 secrets зберігаються у:  
  - GCP Secret Manager  
  - або Kubernetes Secrets  
- Токени НЕ зберігаються у репозиторії  
- .env файли не комітяться
___

## Автор

Проєкт виконала Олеся Шевченко в рамках **Full-Stack Internship** :)
