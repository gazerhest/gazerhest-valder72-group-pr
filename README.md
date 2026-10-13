# PC Assembly
Вебплатформа для створення власних конфігурацій ПК, а також пошук, порівняння та купівлі комп'ютерних комплектуючих.

## Functionality
- **Каталог запчастин**: Перегляд списку деталей з можливістю пошуку за назвою, фільтрацією за характеристиками й виробником, а також сортуванням за ціною та популярністю (оцінкою користувачів).


- **Детальна сторінка запчастини**: Перегляд її технічних характеристик, відгуків, оцінок від покупців.


- **Порівняння**: Можливість обрати кілька деталей одного типу та порівняти їхні характеристики.


- **Конструктор / Збірка ПК**: Можливість поетапно обрати сумісні комплектуючі, зберегти збірку у своєму профілі та порівняти з іншиим збірками, або одразу оформити на неї замовлення.


- **Особистий кабінет**: Реєстрація, збережені збірки, історія замовлень та редагування особистих даних (нікнейм, ПІБ, номер теоефону, пошта, адреса проживання).


## Endpoints

### Parts

- `GET /parts`

  - **Опис:** Отримання списку деталей з підтримкою пагінації, пошуку, динамічної фільтрації та сортування.
  - **Request method:** GET
  - **Query Parameters:**
    - `page`: номер сторінки (базово 1)
    - `limit`: кількість об'єктів на сторінці (базово 12)
    - `search`: пошук за назвою (наприклад, `rtx 4070`)
    - `filters`: список характеристик для фільтрації у форматі `key:value`, розділених комами (наприклад, `socket:AM5,power:750W,ram_type:DDR5,category:CPU`)
    - `min_price`: мінімальна ціна
    - `max_price`: максимальна ціна
    - `sort`: спосіб сортування (`price_asc`, `price_desc`, `rating_desc`, `name_asc`)
  - **Request body:** порожній
  - **Response Status:** `200 OK`
  - **Response Body (JSON):**
    ```json
    {
      "page": 1,
      "limit": 12,
      "total_items": 45,
      "total_pages": 4,
      "data": [
        {
          "id": 101,
          "title": "AMD Ryzen 7 7800X3D",
          "category": "CPU",
          "manufacturer": "AMD",
          "price": 17800.00,
          "rating": 4.9,
          "specs": {
            "socket": "AM5",
            "cores": 8,
            "threads": 16,
            "power": "120W"
          }
        }
      ]
    }
    ```

- `GET /parts/{id}`

  - **Опис:** Отримання повної інформації про конкретну деталь.
  - **Request method:** GET
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `404 Not Found`
  - **Response Body (JSON):**
    ```json
    {
      "id": 101,
      "title": "Intel Core i7-13700K",
      "category": "CPU",
      "manufacturer": {
        "id": 2,
        "name": "Intel"
      },
      "price": 16500.00,
      "rating": 4.9,
      "description": "Потужний процесор для ігрових систем та робочих станцій.",
      "specs": {
        "socket": "LGA1700",
        "cores": 16,
        "threads": 24,
        "base_clock_ghz": 3.4,
        "max_boost_ghz": 5.4,
        "tdp_watts": 125,
        "integrated_graphics": "Intel UHD Graphics 770"
      }
    }
    ```

### Compare parts

- `GET /parts/compare`

  - **Опис:** Отримання списку обраних товарів одного типу для порівняння їхніх характеристик.
  - **Request method:** GET
  - **Query Parameters:**
    - `ids`: список ID товарів через кому (наприклад, `101,102`)
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `400 Bad Request` (якщо передано товари з різних категорій)
  - **Response Body (JSON):**
    ```json
    {
      "category": "RAM",
      "compared_items": [
        {
          "id": 201,
          "title": "Kingston FURY DDR5 32GB (2x16GB)",
          "price": 5400.00,
          "specs": {
            "memory_capacity_gb": 32,
            "memory_type": "DDR5",
            "frequency_mhz": 6000,
            "cas_latency": "CL36"
          }
        },
        {
          "id": 202,
          "title": "Corsair Vengeance DDR5 32GB (2x16GB)",
          "price": 5800.00,
          "specs": {
            "memory_capacity_gb": 32,
            "memory_type": "DDR5",
            "frequency_mhz": 6400,
            "cas_latency": "CL32"
          }
        }
      ]
    }
    ```

### Builds

- `POST /builds`

  - **Опис:** Збереження нової конфігурації ПК (створеної у конструкторі).
  - **Request method:** POST
  - **Query Parameters:** відсутні
  - **Request body (JSON):**
    ```json
    {
      "title": "Ігрова збірка 2026",
      "parts": [
        { "part_id": 101, "quantity": 1 },
        { "part_id": 201, "quantity": 2 },
        { "part_id": 305, "quantity": 1 }
      ]
    }
    ```
  - **Response Status:** `201 Created`, `400 Bad Request` (помилка сумісності або некоректні дані)
  - **Response Body (JSON):**
    ```json
    {
      "id": 42,
      "title": "Ігрова збірка 2026",
      "total_price": 43800.00,
      "parts_count": 4,
      "created_at": "2026-10-13T16:00:00Z"
    }
    ```

- `GET /builds/{id}`

  - **Опис:** Отримання детальної інформації про збережену збірку.
  - **Request method:** GET
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `404 Not Found`
  - **Response Body (JSON):**
    ```json
    {
      "id": 42,
      "title": "Ігрова збірка 2026",
      "author": "pc_master_99",
      "total_price": 43800.00,
      "items": [
        {
          "part_id": 101,
          "title": "Intel Core i7-13700K",
          "category": "CPU",
          "quantity": 1,
          "unit_price": 16500.00
        },
        {
          "part_id": 201,
          "title": "Kingston FURY DDR5 32GB",
          "category": "RAM",
          "quantity": 2,
          "unit_price": 5400.00
        }
      ]
    }
    ```

- `DELETE /builds/{id}`

  - **Опис:** Видалення збереженої збірки користувача.
  - **Request method:** DELETE
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK` (або `204 No Content`), `401 Unauthorized`, `403 Forbidden` (якщо збірка належить іншому користувачу), `404 Not Found`
  - **Response Body (JSON):**
    ```json
    {
      "message": "Збірку успішно видалено",
      "deleted_build_id": 42
    }
    ```

### Cart

- `GET /cart`

  - **Опис:** Отримання вмісту кошика поточного користувача.
  - **Request method:** GET
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `401 Unauthorized`
  - **Response Body (JSON):**
    ```json
    {
      "items": [
        {
          "cart_item_id": 1,
          "part_id": 101,
          "title": "Intel Core i7-13700K",
          "quantity": 1,
          "price": 16500.00
        }
      ],
      "total_amount": 16500.00
    }
    ```

- `POST /orders`

  - **Опис:** Оформлення замовлення (перетворення кошика або збірки у замовлення).
  - **Request method:** POST
  - **Query Parameters:** відсутні
  - **Request body (JSON):**
    ```json
    {
      "source_type": "cart",
      "build_id": null
    }
    ```
  - **Response Status:** `201 Created`, `400 Bad Request`
  - **Response Body (JSON):**
    ```json
    {
      "order_id": 1005,
      "status": "created",
      "total_price": 16500.00
    }
    ```

- `DELETE /cart/items/{item_id}`

  - **Опис:** Видалення конкретного товару (або позиції) з кошика.
  - **Request method:** DELETE
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `401 Unauthorized`, `404 Not Found`
  - **Response Body (JSON):**
    ```json
    {
      "message": "Товар видалено з кошика",
      "cart_item_id": 1,
      "updated_total_amount": 0.00
    }
    ```

- `DELETE /cart`

  - **Опис:** Повне очищення кошика поточного користувача.
  - **Request method:** DELETE
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `401 Unauthorized`
  - **Response Body (JSON):**
    ```json
    {
      "message": "Кошик успішно очищено"
    }
    ```

### Admin Management

- `POST /parts`

  - **Опис:** Додавання нової запчастини до каталогу (тільки для Admin).
  - **Request method:** POST
  - **Query Parameters:** відсутні
  - **Request body (JSON):**
    ```json
    {
      "title": "NVIDIA GeForce RTX 4080 Super",
      "category": "GPU",
      "manufacturer_id": 1,
      "price": 42000.00,
      "specs": {
        "vram": "16GB",
        "interface": "PCIe 4.0"
      }
    }
    ```
  - **Response Status:** `201 Created`, `401 Unauthorized`, `403 Forbidden`
  - **Response Body (JSON):**
    ```json
    {
      "id": 105,
      "title": "NVIDIA GeForce RTX 4080 Super",
      "message": "Товар успішно додано"
    }
    ```

- `DELETE /parts/{id}`

  - **Опис:** Видалення запчастини з каталогу (тільки для Admin).
  - **Request method:** DELETE
  - **Query Parameters:** відсутні
  - **Request body:** порожній
  - **Response Status:** `200 OK`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`
  - **Response Body (JSON):**
    ```json
    {
      "message": "Товар успішно видалено"
    }
    ```

- `PATCH /admin/orders/{id}/status`

  - **Опис:** Зміна статусу замовлення клієнта (тільки для Admin).
  - **Request method:** PATCH
  - **Query Parameters:** відсутні
  - **Request body (JSON):**
    ```json
    {
      "status": "shipped"
    }
    ```
  - **Response Status:** `200 OK`, `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`
  - **Response Body (JSON):**
    ```json
    {
      "message": "Статус замовлення оновлено"
    }
    ```

# TODO
- подумати як зберігати ціни(одразу в БД чи брати актуальну інформацію)

- подумати над даними які необхідно зберігати в БД
