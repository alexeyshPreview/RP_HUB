## 👤 AccountService (user management api)
Сервис отвечает за управление профилем пользователя, обновление личных данных, сброс паролей и завершение активных сессий (logout). 

**Базовый URL:** `/account`

---

### 🚀 Эндпоинты API

#### 1. Получение информации о пользователе
Возвращает профиль текущего авторизованного пользователя на основе валидного JWT-токена доступа.

* **Метод:** `GET`
* **Путь:** `/userInfo`
* **Пример полного пути:** `http://localhost:8765/account/userInfo`
* **Заголовки / Куки:**
  * `Cookie: access_token=<jwt_token>` (Обязательный)
* **Тип ответа:** `UserProfileDto`
* **Успешный ответ (`200 OK`):**
  ```json
  {
    "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "username": "ivan_ivanov",
    "name": "Иван Иванов",
    "firstName": "Иван",
    "lastName": "Иванов",
    "email": "ivan@example.com",
    "emailVerified": true
  }
  ```
  
#### 2. Обновление данных профиля
изменяет персональные данные пользователя. идентификация происходит по внутреннему заголовку шлюза.

* **метод:** `PUT`
* **путь:** `/update`
* **Пример полного пути:** `http://localhost:8765/account/update`
* **заголовки:**
  * `X-User-Id: <UUID>` (подставляется автоматически api gateway)
* **тело запроса (`UpdateDataUserRequest`):**
  ```json
  {
    "username": "new_username",
    "password": "optional_new_password",
    "firstName": "Иван",
    "lastName": "Новый-Иванов",
    "email": "new_email@example.com"
  }
  ```
#### 3. сброс пароля
позволяет авторизованному пользователю установить новый пароль для своей учетной записи.

* **метод:** `PUT`
* **путь:** `/reset`
* **Пример полного пути:** `http://localhost:8765/account/reset`
* **заголовки:**
  * `X-User-Id: <UUID>` (подставляется автоматически api gateway)
* **тело запроса (`ResetPasswordRequest`):**
  ```json
  {
    "newPassword": "my_secure_new_password_123"
  }
  ```
#### 4. выход из системы (logout)
инвалидирует сессию пользователя на стороне oauth2/keycloak и полностью очищает авторизационные куки на клиенте.

* **метод:** `POST`
* **путь:** `/logout`
* **Пример полного пути:** `http://localhost:8765/account/logout`
* **заголовки / куки:**
  * `Cookie: refresh_token=<jwt_refresh_token>` (обязательный)
* **эффект:** полностью удаляет куки `access_token` и `refresh_token` на стороне клиента (устанавливает `maxAge = 0`, `path = "/"` и флаг `HttpOnly`).
* **тип ответа:** `JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    "status": "success",
    "message": "Logout user successfully",
    "timestamp": "2026-06-04T15:10:37Z"
  }
  ```

#### 5. удаление аккаунта
полностью удаляет учетную запись пользователя из системы и очищает сессионные куки на клиенте.

* **метод:** `POST`
* **путь:** `/delete/{id}`
* **Пример полного пути:** `http://localhost:8765/account/delete/456e4567-e89b-12d3-a456-426614174111`
* **параметры пути:**
  * `id` — уникальный идентификатор удаляемого пользователя (UUID / String).
* **эффект:** полностью удаляет куки `access_token` и `refresh_token` на стороне клиента (устанавливает `maxAge = 0`, `path = "/"` и флаг `HttpOnly`).
* **тип ответа:** `JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    "status": "success",
    "message": "Delete user successfully",
    "timestamp": "2026-06-04T15:11:00Z"
  }
  ```
