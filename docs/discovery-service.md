## 🔍 Discovery Service (Scraper & Reconnaissance API)

сервис отвечает за сбор данных, обход блокировок анти-бот систем и парсинг актуального онлайна игровых серверов в реальном времени для различных roleplay-проектов.

**базовый url:** `/discovery`

---

### 🚀 Эндпоинты API

#### 1. Мониторинг общего онлайна GTA5RP (Get GTA5RP Online)
возвращает актуальную информацию о текущем количестве игроков на всех серверах проекта gta5rp.

* **метод:** `GET`
* **путь:** `/gta5rp/allOnline`
* **полный путь:** `http://localhost:8765/discovery/gta5rp/allOnline`
* **тип ответа:** `Строка / JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    14250
  }
  ```

#### 2. Мониторинг общего онлайна SAMP (Get SAMP Online)
запрашивает и парсит данные о суммарном онлайне серверов платформы samp.

* **метод:** `GET`
* **путь:** `/samp/allOnline`
* **полный путь:** `http://localhost:8765/discovery/samp/allOnline`
* **тип ответа:** `Строка / JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    4120
  }
  ```

#### 3. Мониторинг общего онлайна Arizona RP (Get Arizona Online)
извлекает текущую статистику посещаемости серверов проекта arizona rp.

* **метод:** `GET`
* **путь:** `/arizona/allOnline`
* **полный путь:** `http://localhost:8765/discovery/arizona/allOnline`
* **тип ответа:** `Строка / JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    23800
  }
  ```

#### 4. Мониторинг общего онлайна Radmir RP (Get Radmir RP Online)
получает информацию об активности игроков на серверах radmir rp, обходя защитные механизмы разметки.

* **метод:** `GET`
* **путь:** `/radmirRP/allOnline`
* **полный путь:** `http://localhost:8765/discovery/radmirRP/allOnline`
* **тип ответа:** `Строка / JSON`
* **успешный ответ (`200 OK`):**
```json
  {
    8950
  }
```

#### 5. Мониторинг общего онлайна Amazing Online (Get Amazing Online)
парсит и возвращает текущее количество активных пользователей на серверах amazing online.

* **метод:** `GET`
* **путь:** `/amazingOnline/allOnline`
* **полный путь:** `http://localhost:8765/discovery/amazingOnline/allOnline`
* **тип ответа:** `Строка / JSON`
* **успешный ответ (`200 OK`):**
  ```json
  {
    3150
  }
  ```
