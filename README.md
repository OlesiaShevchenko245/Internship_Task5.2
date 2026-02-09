# Security & Gateway Layer
### Статус роботи:  
Основна архітектура, API Gateway, OAuth2 авторизація та мікросервісна система повністю реалізовані та запускаються локально через Docker. У репозиторії міститься завершена робоча система backend з frontend. В свою чергу, Cloud deployment та CI/CD pipeline підготовлені відповідно до production-архітектури.
Репозиторій доступний за посиланням: https://github.com/OlesiaShevchenko245/Internship_Task5.2/tree/master. 
___

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
Gateway виконує:  
- routing  
- авторизацію  
- OAuth2 login  
- cookie/JWT management  
- CORS policy  
- security headers  
___

## Запуск (локально)
### Клонування репозиторію (завантаження https://github.com/OlesiaShevchenko245/Internship_Task5.2/tree/master)  
```
git clone <repository-url>
cd Internship_Task5.2
cp .env.example .env #з заповненням ваших даних
docker compose up -d --build
```
Після запуску (локально):  

| Сервіс      | URL                    |
| ----------- | -----------------------|
| Frontend    | [http://localhost:8088]|
| Gateway API | [http://localhost:8080]|
| MailHog     | [http://localhost:8025]|
| Kibana      | [http://localhost:5601]|
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

### Перевірка авторизації (Endpoint /profile)
Gateway або окремий auth-сервіс надає endpoint. Необхідно:
1. Відкрити frontend (http://localhost:8088)   
2. Натиснути Login  
3. Увійти через Google  
4. Після логіну викликається:  
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
Система спроєктована для деплою у GKE:  
- Кожен сервіс = окремий Deployment  
- Gateway = LoadBalancer Service  
- Secrets через Kubernetes  
- Rolling updates  
- Horizontal scaling ready

Повний деплой у Google Kubernetes Engine потребує активного billing-акаунту Google Cloud Platform. GKE, Artifact Registry, Load Balancer та інші необхідні сервіси належать до платних ресурсів GCP і не можуть бути активовані без підключеного billing-профілю. На момент виконання завдання, на жаль, billing-акаунт є мені недоступним, але попри це, у репозиторії реалізовано повну Kubernetes-архітектуру, підготовлено manifests для production-деплою та налаштовано GitHub Actions pipeline. Система запускається локально через Docker, а сама архітектура проєкту відповідає GKE deployment-моделі. 
___

## CI/CD  
Використано GitHub Actions:  
Pipeline:  
- Push у main  
- Збірка Docker image  
- Push у Container Registry  
- Автоматичний redeploy у GKE (архітектурно, без реалізації)
- Rolling update сервісів

___

## Безпека  
Для безпеки даних системи зроблені такі рішення:  
- OAuth secrets НЕ зберігаються у репозиторії  
- JWT secret реалізовані через environment variables  
- Kubernetes Secrets  
- HttpOnly cookies  
- SameSite policy  
- CORS restriction  
- Secure headers  
___

## Автор

Проєкт виконала Олеся Шевченко в рамках **Full-Stack Internship** :)
