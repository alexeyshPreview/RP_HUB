## 💬 Chat Service (Centrifugo Integration API)

раздел описывает контроллер, отвечающий за интеграцию с real-time сервером centrifugo. он обрабатывает вебхуки авторизации подключений от самого centrifugo и координирует сетевой статус пользователей (online/offline) через брокер сообщений.

### 📋 Класс: `CentrifugoConnectController`
**базовый url:** `/api/centrifugo`

---

### 🚀 Эндпоинты API

#### 1. Авторизация подключения к веб-сокетам (Connect Webhook)
вызывается сервером centrifugo при попытке клиента установить веб-сокет соединение. проверяет пользователя, формирует результат авторизации и публикует в глобальный канал событие о том, что юзер зашел в сеть.

* **метод:** `POST`
* **путь:** `/connect`
* **полный путь:** `/api/centrifugo/connect`
* **заголовки:**
  * `X-User-Id: <UUID>` (идентификатор пользователя, прошедший через api gateway)
* **тип ответа:** `JSON`
* **успешный ответ (`200 OK`):**
```json
  {
    "result": {
      "user": "123e4567-e89b-12d3-a456-426614174000"
    }
  }
```

#### 2. Обработка отключения от веб-сокетов (Disconnect Webhook)
вызывается сервером centrifugo, когда клиент разрывает соединение с веб-сокетами. фиксирует выход пользователя из сети и уведомляет об этом систему.

* **метод:** `POST`
* **путь:** `/disconnect`
* **полный путь:** `/api/centrifugo/disconnect`
* **тело запроса (body):**
```json
  {
    "user": "123e4567-e89b-12d3-a456-426614174000"
  }
```

### 📋 Класс: `ChatController`
**базовый url:** `/chats`

---

### 🚀 Эндпоинты API

#### 1. Поиск пользователя или чата (Search Chat or User)
выполняет глобальный поиск среди доступных чатов и зарегистрированных пользователей по их имени или юзернейму для инициации диалога.

* **метод:** `GET`
* **путь:** `/search`
* **полный путь:** `/chats/search`
* **заголовки:**
  * `X-User-Id: <UUID>` (идентификатор текущего пользователя)
* **параметры запроса (query params):**
  * `name` — строка поиска (имя или часть имени).
* **тип ответа:** `JSON` (`List<ViewResultInSearchWindow>`)
* **успешный ответ (`200 OK`):**
```json
  [
    {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "name": "ivan_pavel",
      "avatar_url": "http://localhost/avatars/1.png",
      "isGroup": false
    }
  ]
```

#### 2. Создание приватного чата/диалога (Create Dialog)
инициализирует новый приватный чат (тет-а-тет) между текущим пользователем и целевым собеседником с возможностью сразу отправить первое сообщение.

* **метод:** `POST`
* **путь:** `/create/dialog`
* **полный путь:** `/chats/create/dialog`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `CreateDialogRequest`
  ```json
  {
    "hisId": "876e4567-e89b-12d3-a456-426614174999",
    "sendMessageRequest": {
      "chatId": null,
      "type": "TEXT",
      "content": "привет! как сам?",
      "fileName": null,
      "fileSize": null
    }
  }
  ```
* **тело ответа (body):** `ViewOneChatResponse`
 ```json
  {
  "chatId": "456e4567-e89b-12d3-a456-426614174111",
  "name": "ivan_pavel",
  "favoriteGame": "GTA 5 RP",
  "avatar_url": "http://localhost/avatars/1.png",
  "chatType": "DIALOG",
  "members": [
    {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "username": "текущий_пользователь",
      "role": "MEMBER",
      "avatar_url": "http://localhost/avatars/default.png"
    },
    {
      "id": "876e4567-e89b-12d3-a456-426614174999",
      "username": "ivan_pavel",
      "role": "MEMBER",
      "avatar_url": "http://localhost/avatars/1.png"
    }
  ],
  "messages": [
    {
      "messageId": "987e4567-e89b-12d3-a456-426614174999",
      "senderId": "123e4567-e89b-12d3-a456-426614174000",
      "senderName": "текущий_пользователь",
      "content": "привет! как сам?",
      "type": "TEXT",
      "fileName": null,
      "fileSize": null,
      "timestamp": "2026-06-04T15:58:03Z"
    }
  ],
  "lastSender": "текущий_пользователь",
  "lastMessage": "привет! как сам?"
}
```

#### 3. Добавление участников в чат (Add Participants)
позволяет добавить одного или нескольких пользователей в существующий групповой чат.

* **метод:** `POST`
* **путь:** `/add/participant`
* **полный путь:** `/chats/add/participant`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `AddUsersInChatRequest`
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "users": [
      "999e4567-e89b-12d3-a456-426614174222"
    ]
  }
  ```
* **тело ответа (body):** `List<AddUsersInChatResponse>`
  ```json
  {
    "username": "gamer_gta",
    "message": "User added successfully",
    "isAdded": true
  }
  ```


#### 4. Назначение администраторов (Add Admins)
наделяет выбранных участников чата правами администратора для управления группой.

* **метод:** `POST`
* **путь:** `/add/admin`
* **полный путь:** `/chats/add/admin`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `AddAdminInChat`
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "admins": [
      "999e4567-e89b-12d3-a456-426614174222"
    ]
  }
  ```
* **тело ответа (body):** `List<AddUsersInChatResponse>`
  ```json
  {
    "username": "gamer_gta",
    "message": "User added successfully",
    "isAdded": true
  }
  ```

#### 5. Получение списка чатов пользователя (Get My Chats)
загружает базовый список всех активных чатов и диалогов авторизованного пользователя для первичной инициализации левой панели интерфейса (включая счетчики непрочитанных, аватарки и статус сети).

* **метод:** `GET`
* **путь:** `/my/init`
* **полный путь:** `/chats/my/init`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тип ответа:** `JSON` (`List<ViewChatsResponse>`)
* **успешный ответ (`200 OK`):**
  ```json
  [
    {
      "chatId": "456e4567-e89b-12d3-a456-426614174111",
      "name": "gta5rp фракция",
      "type": "GROUP",
      "avatar_url": "http://localhost/avatars/group1.png",
      "lastSender": "ivan_pavel",
      "lastMessage": "собираемся на дроп через 10 минут",
      "unreadCount": 3,
      "isOnline": false,
      "isMuted": false
    }
  ]
  ```

#### 6. Получение информации об одном чате (Get One Chat)
возвращает детальную информацию по конкретному чату для его открытия, включая полный список участников, метаданные и историю последних сообщений.

* **метод:** `GET`
* **путь:** `/get`
* **полный путь:** `/chats/get`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **параметры запроса (query params):**
  * `chatId` — идентификатор запрашиваемого чата.
* **тип ответа:** `JSON` (`ViewOneChatResponse`)
* **успешный ответ (`200 OK`):**
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "name": "gta5rp фракция",
    "favoriteGame": "GTA 5 RP",
    "avatar_url": "http://localhost/avatars/group1.png",
    "chatType": "GROUP",
    "members": [
      {
        "id": "123e4567-e89b-12d3-a456-426614174000",
        "username": "ivan_pavel",
        "role": "OWNER",
        "avatar_url": "http://localhost/avatars/1.png"
      }
    ],
    "messages": [
      {
        "messageId": "987e4567-e89b-12d3-a456-426614174999",
        "senderId": "123e4567-e89b-12d3-a456-426614174000",
        "senderName": "ivan_pavel",
        "content": "собираемся на дроп через 10 минут",
        "type": "TEXT",
        "fileName": null,
        "fileSize": null,
        "timestamp": "2026-06-04T16:06:11Z"
      }
    ],
    "lastSender": "ivan_pavel",
    "lastMessage": "собираемся на дроп через 10 минут"
  }
  ```

#### 7. Удаление чата (Delete Chat)
полностью удаляет чат со всеми сообщениями. для групповых чатов операция доступна исключительно владельцу (создателю) группы.

* **метод:** `DELETE`
* **путь:** `/{chatId}`
* **полный путь:** `/chats/456e4567-e89b-12d3-a456-426614174111`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тип ответа:** `отсутствует (Void)`
* **успешный ответ (`204 No Content`):** `Пустое тело ответа`

#### 8. Выход из группового чата (Leave Chat)
позволяет обычному пользователю самостоятельно покинуть групповой чат, исключив себя из списка участников.

* **метод:** `POST`
* **путь:** `/{chatId}/leave`
* **полный путь:** `/chats/456e4567-e89b-12d3-a456-426614174111/leave`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тип ответа:** `отсутствует (Void)`
* **успешный ответ (`200 OK`):** `Пустое тело ответа`

#### 9. Включение/выключение уведомлений (Toggle Mute)
изменяет режим уведомлений для конкретного чата (включает или отключает звук и индикацию обновлений на левой панели).

* **метод:** `PATCH`
* **путь:** `/{chatId}/mute`
* **полный путь:** `/chats/456e4567-e89b-12d3-a456-426614174111/mute`
* **параметры запроса (query params):**
  * `userId` — идентификатор пользователя.
* **тип ответа:** `JSON`
* **успешный ответ (`200 OK`):**
```json
  {
    "isMuted": true
  }
```


### 📋 Класс: `MessageController`
**базовый url:** `/messages`

---

### 🚀 Эндпоинты API

#### 1. Отправка сообщения (Send Message)
публикует новое сообщение в чат, сохраняет его в бд и транслирует событие в real-time каналы centrifugo для обновления интерфейса (окна чата и левой панели) у всех участников.

* **метод:** `POST`
* **путь:** `/send`
* **полный путь:** `/messages/send`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `SendMessageRequest`
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "type": "TEXT",
    "content": "привет, заходи в игру",
    "fileName": null,
    "fileSize": null
  }
  ```
  ```json
  {
    "action": "CREATE",
    "data": {
      "messageId": "987e4567-e89b-12d3-a456-426614174999",
      "senderId": "123e4567-e89b-12d3-a456-426614174000",
      "senderName": "ivan_pavel",
      "content": "привет, заходи в игру",
      "type": "TEXT",
      "fileName": null,
      "fileSize": null,
      "timestamp": "2026-06-04T16:10:00Z"
    }
  }
  ```

#### 2. Удаление сообщения только для себя (Delete Message For Me)
скрывает выбранное сообщение из истории чата исключительно для текущего авторизованного пользователя.

* **метод:** `POST`
* **путь:** `/delete/for/me`
* **полный путь:** `/messages/delete/for/me`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `DeleteMsgRequest`
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "messageId": "987e4567-e89b-12d3-a456-426614174999"
  }
  ```

#### 3. Удаление сообщения для всех участников (Delete Message For Everyone)
полностью удаляет сообщение из истории чата для всех его участников.

* **метод:** `POST`
* **путь:** `/delete/for/everyone`
* **полный путь:** `/messages/delete/for/everyone`
* **заголовки:**
  * `X-User-Id: <UUID>`
* **тело запроса (body):** `DeleteMsgRequest`
  ```json
  {
    "chatId": "456e4567-e89b-12d3-a456-426614174111",
    "messageId": "987e4567-e89b-12d3-a456-426614174999"
  }
  ```
