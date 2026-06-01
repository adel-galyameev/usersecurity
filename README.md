# UserSecurity

Сервис управления пользователями с JWT-аутентификацией.

### Предварительные требования

- Minikube запущен
- установлен  nginx через helm (как описано в Базовые сущности Kubernetes: Service, Ingress // ДЗ)


### Запуск helm чарта (выполнять в корне репозитория)

```bash
helm install usersecurity ./usersecurity
```

**Ожидаемый ответ:**

```bash
NAME: usersecurity
LAST DEPLOYED: Mon May  4 12:37:17 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
```

### Остановка helm чарта

```bash
helm uninstall usersecurity
```

**Ожидаемый ответ:**

```bash
release "usersecurity" uninstalled
```

## API

| Метод | Endpoint | Описание | Auth |
|-------|----------|----------|------|
| POST | `/api/auth/registration` | Регистрация | Нет |
| POST | `/api/auth/login` | Вход | Нет |
| GET | `/api/users/{id}` | Получить профиль | Bearer token |
| PATCH | `/api/users/{id}` | Обновить профиль | Bearer token |


### Запуск

```bash
newman run UserSecurity_API_Tests.postman_collection.json \
  --env-var "baseUrl=http://arch.homework"  \
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
