# DevOps Тестове Завдання: CI/CD для NestJS + Redis + Kubernetes

Цей проект — приклад повного DevOps-циклу для застосунку NestJS, який має один ендпоінт /redis, що перевіряє підключення до Redis.
Мета — побудувати Docker-образ, автоматично деплоїти його в Kubernetes через CI/CD, налаштувати моніторинг та впровадити best practices з безпеки.

 Структура проекту
nestjs-app/
├── src/
│   ├── redis/
│   │   └── redis.service.ts
│   ├── app.controller.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── package.json
├── tsconfig.json
├── Dockerfile
├── .dockerignore
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── configmap.yaml
│   ├── secrets.yaml
│   ├── redis-deployment.yaml
│   ├── redis-service.yaml
│   ├── networkpolicy.yaml
│   └── hpa.yaml
└── .github/workflows/cicd.yml
🔹 Що реалізовано
1. Dockerfile
Використано багатоетапну збірку:

node:18-alpine для етапу компіляції

node:18-alpine для фінального lightweight-образу

Зменшено розмір образу за рахунок:

npm ci --omit=dev

очищення кешу

Додано користувача без root прав

.dockerignore виключає непотрібні файли (node_modules, dist, .git)

2. CI/CD Pipeline (GitHub Actions)
Автоматична збірка Docker-образу при пуші в main

Push у Docker Hub з використанням GitHub Secrets

Деплой у minikube через kubectl

Kubeconfig передається як зашифрований секрет

3. Kubernetes Маніфести
Deployment + Service для NestJS

Deployment + Service для Redis

ConfigMap — конфігураційні змінні (хост, порт)

Secret — зберігання пароля Redis

Ingress — HTTP доступ

NetworkPolicy — обмеження доступу до Redis тільки з NestJS подів

SecurityContext — контейнер не працює під root

HPA (Horizontal Pod Autoscaler) — автоматичне масштабування

4. Моніторинг (Prometheus + Grafana)
Встановлено kube-prometheus-stack через Helm

NestJS має endpoint /metrics

Prometheus збирає метрики через ServiceMonitor

Grafana з dashboard для перегляду стану сервісу та Redis

5. Health Checks
livenessProbe та readinessProbe для NestJS

Перевірка /redis при запуску пода

 Як розгорнути локально

# 1. Запустити minikube або AKS
minikube start

# 2. Застосувати маніфести
kubectl apply -f k8s/

# 3. Перевірити поди
kubectl get pods

# 4. Отримати доступ через ingress
curl http://nestjs.local/redis
 Моніторинг

# Портфорвард Grafana
kubectl port-forward svc/monitoring-grafana 3000:80

# Логін:
admin / <пароль з секрету monitoring-grafana>