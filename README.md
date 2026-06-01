# UserSecurity

Сервис управления пользователями с JWT-аутентификацией.

## Установка

### 1. Установка nginx-ingress controller

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx --create-namespace
```

### 2. Установка приложения

```bash
helm install usersecurity ./usersecurity -n usersecurity --create-namespace
```

### 3. Настройка arch.homework

```bash
# Получить IP ingress-а
kubectl get svc -n ingress-nginx ingress-nginx-controller

# Добавить в /etc/hosts (или использовать minikube ip / kind)
echo "<IP> arch.homework" | sudo tee -a /etc/hosts
```

### 4. Проверка, что всё запустилось

```bash
kubectl get pods -n usersecurity -w
```

Дождаться статуса `Running` для обоих подов (PostgreSQL и приложение).

## API

| Метод | Endpoint | Описание | Auth |
|-------|----------|----------|------|
| POST | `/api/auth/registration` | Регистрация | Нет |
| POST | `/api/auth/login` | Вход | Нет |
| GET | `/api/users/{id}` | Получить профиль | Bearer token |
| PATCH | `/api/users/{id}` | Обновить профиль | Bearer token |

## Запуск тестов

### Установка newman

```bash
npm install -g newman
```

### Запуск

```bash
newman run usersecurity.postman_collection.json \
  --env-var "baseUrl=http://arch.homework" \
  --reporters cli \
  --verbose
```

Флаг `--verbose` выводит тело запроса и ответа для каждого шага.

### Что тестируется

1. Регистрация пользователя 1
2. Проверка, что эндпоинты недоступны без токена (ожидается 403)
3. Вход пользователя 1
4. Изменение профиля пользователя 1
5. Проверка, что профиль изменился
6. Регистрация пользователя 2
7. Вход пользователя 2
8. Проверка, что пользователь 2 не имеет доступа к профилю пользователя 1 (ожидается 403)

В тестах используются случайно сгенерированные данные (username, email, пароль) и переменная `{{baseUrl}}` с начальным значением `http://arch.homework`.
