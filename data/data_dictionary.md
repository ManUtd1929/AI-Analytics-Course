# Data Dictionary — Northwind (data/raw/)

Словарь данных составлен на основе заголовков и структуры фактически скачанных CSV-файлов из `data/raw/` (источник: `neo4j-contrib/northwind-neo4j`).

Ключи и связи между таблицами помечены как **предполагаемые** — они выведены из соглашения об именовании колонок (`*ID`), без проверки уникальности и ссылочной целостности. Детальная валидация данных будет проведена отдельно на этапе анализа.

---

## categories.csv — категории товаров

- **Строк данных:** 8
- **Колонки:** `categoryID`, `categoryName`, `description`, `picture`
- **Предполагаемый ключ:** `categoryID` (PK)
- ⚠️ **Техническая колонка:** `picture` — бинарные данные (BLOB в виде hex-строки), не пригодна для анализа, рекомендуется исключать при загрузке.

## customers.csv — клиенты

- **Строк данных:** 91
- **Колонки:** `customerID`, `companyName`, `contactName`, `contactTitle`, `address`, `city`, `region`, `postalCode`, `country`, `phone`, `fax`
- **Предполагаемый ключ:** `customerID` (PK)

## employees.csv — сотрудники

- **Строк данных:** 9
- **Колонки:** `employeeID`, `lastName`, `firstName`, `title`, `titleOfCourtesy`, `birthDate`, `hireDate`, `address`, `city`, `region`, `postalCode`, `country`, `homePhone`, `extension`, `photo`, `notes`, `reportsTo`, `photoPath`
- **Предполагаемый ключ:** `employeeID` (PK); `reportsTo`, вероятно, самоссылающийся FK на `employeeID` (менеджер сотрудника)
- ⚠️ **Технические колонки:** `photo` — бинарные данные (BLOB в виде hex-строки); `photoPath` — путь/URL к файлу фото, аналитической ценности не несёт. Рекомендуется исключать обе при загрузке.

## employee-territories.csv — связь сотрудников и территорий (junction-таблица)

- **Строк данных:** 49
- **Колонки:** `employeeID`, `territoryID`
- **Предполагаемый ключ:** составной (`employeeID`, `territoryID`); оба поля — FK на `employees.employeeID` и `territories.territoryID` соответственно

## territories.csv — территории продаж

- **Строк данных:** 53
- **Колонки:** `territoryID`, `territoryDescription`, `regionID`
- **Предполагаемый ключ:** `territoryID` (PK)
- ⚠️ **Замечание по структуре:** `regionID`, по всей видимости, ссылается на справочник регионов, но соответствующий файл (`region.csv`) в `data/raw/` отсутствует — при необходимости эту таблицу нужно будет отдельно найти/добавить.

## suppliers.csv — поставщики

- **Строк данных:** 29
- **Колонки:** `supplierID`, `companyName`, `contactName`, `contactTitle`, `address`, `city`, `region`, `postalCode`, `country`, `phone`, `fax`, `homePage`
- **Предполагаемый ключ:** `supplierID` (PK)

## products.csv — товары

- **Строк данных:** 77
- **Колонки:** `productID`, `productName`, `supplierID`, `categoryID`, `quantityPerUnit`, `unitPrice`, `unitsInStock`, `unitsOnOrder`, `reorderLevel`, `discontinued`
- **Предполагаемый ключ:** `productID` (PK); FK `supplierID` → `suppliers.supplierID`, FK `categoryID` → `categories.categoryID`

## orders.csv — заказы (шапка заказа)

- **Строк данных:** 830
- **Колонки:** `orderID`, `customerID`, `employeeID`, `orderDate`, `requiredDate`, `shippedDate`, `shipVia`, `freight`, `shipName`, `shipAddress`, `shipCity`, `shipRegion`, `shipPostalCode`, `shipCountry`
- **Предполагаемый ключ:** `orderID` (PK); FK `customerID` → `customers.customerID`, FK `employeeID` → `employees.employeeID`
- ⚠️ **Замечание по структуре:** `shipVia`, по всей видимости, ссылается на справочник служб доставки (shippers), но соответствующий файл (`shippers.csv`) в `data/raw/` отсутствует.

## order-details.csv — позиции заказа

- **Строк данных:** 2155
- **Колонки:** `orderID`, `productID`, `unitPrice`, `quantity`, `discount`
- **Предполагаемый ключ:** составной (`orderID`, `productID`); FK `orderID` → `orders.orderID`, FK `productID` → `products.productID`
