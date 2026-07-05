# DevOps СИТУ Лабораторная работа №9: Cloud-Native приложение и CI/CD (GitHub Actions + Kubernetes + ArgoCD)

## Описание

Цель лабораторной работы — разработать **cloud-native приложение** и реализовать полный CI/CD цикл доставки в Kubernetes с использованием:

- GitHub Actions (CI/CD)
- Docker
- Kubernetes
- ArgoCD
- Git branching strategy (dev / release / main)

---

## Этапы выполнения

### 1. Создание приложения

Разработано простое Python-приложение:

- расположение: `server/application.py`
- реализует HTTP-сервер
- возвращает тестовый ответ
- используется как базовый сервис для CI/CD pipeline

---

### 2. Unit-тестирование

Добавлены тесты:

- `server/test_application.py`
- проверка корректности работы приложения
- используется для этапа CI (GitHub Actions)

---

### 3. Dockerization

Создан Dockerfile:

- `server/dockerfile`
- собирает контейнер с приложением
- обеспечивает запуск сервиса в изолированной среде

---

### 4. CI pipeline (GitHub Actions)

Настроены workflows:

- `.github/workflows/cicd.yml`
- `.github/workflows/devops_course_pipeline.yml`

CI включает этапы:

- lint (проверка качества кода)
- unit tests (pytest)
- build test (сборка Docker образа)

Pipeline запускается при:

- push в `dev`
- pull request в `main`

---

### 5. Kubernetes manifests

Созданы манифесты:

```text
server-k8s-manifests/
└── devopslab9situ.yml
```

Они содержат:

- Deployment приложения
- Service типа LoadBalancer
- проброс порта `12345 → 8000`
- использование Docker-образа:
  ```
  artyomgorlanov/devopslab9situ:latest
  ```

---

### 6. CD pipeline (GitHub Actions)

CD сценарий выполняет:

- сборку Docker image
- push в Docker Hub

Используются секреты GitHub:

- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`

Pipeline автоматически публикует образ при изменениях в `release` ветке.

---

### 7. ArgoCD

Установлен и подключен ArgoCD:

- развернут в Kubernetes
- подключен к GitHub репозиторию
- отслеживает изменения в ветке `release`

ArgoCD обеспечивает:

- автоматический деплой новых версий
- синхронизацию состояния кластера с Git репозиторием

---

### 8. Git workflow

Используется модель веток:

- `dev` — разработка
- `main` — стабильная версия
- `release` — деплой в Kubernetes (ArgoCD)

Пример работы:

```bash
git checkout -b dev
git push origin dev

git checkout -b release origin/release
git merge dev
git push origin release
```

---

## Kubernetes развертывание

Манифест Deployment:

- 1 реплика приложения
- containerPort: `8000`
- образ из Docker Hub

Service:

- тип: LoadBalancer
- внешний порт: `12345`
- targetPort: `8000`
- externalIP: `172.16.10.100`

---

## Проверка работы

Пример ответа сервиса:

```text
Hello World VERSION 5!
I have been seen 942 times.
My name is: devopslab9situ-xxx
```

---

## CI/CD workflow (общая схема)

```text
Developer → GitHub (dev)
        ↓
   GitHub Actions CI
   (lint + test + build)
        ↓
   Merge → release
        ↓
   CD pipeline (build + push Docker image)
        ↓
   ArgoCD sync
        ↓
   Kubernetes deploy
        ↓
   Application running
```

---

## Используемые технологии

- Python
- Docker
- GitHub Actions
- Kubernetes
- ArgoCD
- Git
- YAML

---

## Результат

В ходе лабораторной работы реализовано:

- cloud-native приложение
- unit-тесты
- Docker-контейнеризация
- CI pipeline (lint/test/build)
- CD pipeline (Docker Hub push)
- Kubernetes deployment
- GitOps-деплой через ArgoCD
- полноценный CI/CD цикл доставки
