
## Огляд
Pydantic - це бібліотека для Python, яка допомагає валідувати та серіалізувати дані. Вона використовується для створення структур даних, що використовуються в Fast API для опису вхідних та вихідних даних API.

## Основні функції і переваги Pydantic
### 1. Валідація Даних
Pydantic дозволяє вам визначити моделі, які представляють структуру даних. Наприклад, для опису користувача ми можемо створити модель:

```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    username: str
    email: str
```

Ця модель визначає, що користувач має цільові поля та їхні типи даних.

### 2. Валідація та Серіалізація
Pydantic автоматично валідує дані, які ви передаєте в модель, та генерує значення за замовчуванням, якщо вони відсутні. Наприклад:

```python
user_data = {"id": 1, "username": "john_doe"}
user = User(**user_data)
```

видасть вам помилку:
```bash
Traceback (most recent call last):
  File "\main.py", line 9, in <module>
    user = User(**user_data)
           ^^^^^^^^^^^^^^^^^
  File "\Python\Python312\Lib\site-packages\pydantic\main.py", line 164, in __init__
    __pydantic_self__.__pydantic_validator__.validate_python(data, self_instance=__pydantic_self__)
pydantic_core._pydantic_core.ValidationError: 1 validation error for User
email
  Field required [type=missing, input_value={'id': 1, 'username': 'john_doe'}, input_type=dict]
    For further information visit https://errors.pydantic.dev/2.5/v/missing
```

зверніть увагу на 
```
pydantic_core._pydantic_core.ValidationError: 1 validation error for User
email
  Field required [type=missing, input_value={'id': 1, 'username': 'john_doe'}, input_type=dict]
```

Це допомагає уникнути некоректних даних і полегшує роботу з ними.

### 3. Зручність використання
Однією з основних переваг Pydantic є зручність використання. Ви можете використовувати моделі Pydantic в Fast API для визначення схем даних ваших API-ендпоінтів. Наприклад:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    id: int
    username: str
    email: str

@app.post("/create_user")
async def create_user(user: User):
    # Ви можете використовувати об'єкт user, як структуру для входу в API
    # Приймаємо дані, перевіряємо їх валідність та створюємо користувача
    return {"message": "Користувач створений", "user_data": user.dict()}
```

### 4. Використання Переваг Python
Pydantic використовує анотації типів Python та статичну перевірку типів, що робить ваш код більш надійним та читабельним. Типи даних, визначені в моделях Pydantic, можна використовувати для автоматичного створення документації.

### Серіалізація

За допомогою Pydantic ви можете легко серіалізувати дані:

```python
user_data = {"id": 1, "username": "john_doe", "email": "john@example.com"} user = User(**user_data) print(user.json())  # Серіалізуємо модель у JSON`
```
## Типи Даних в Pydantic

### Прості Типи

Pydantic підтримує прості типи даних, такі як int, str, float, bool тощо.
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    quantity: int
    in_stock: bool

```
`

### Складні Типи

Ви можете використовувати складні типи, такі як List, Dict, Tuple та інші:

```python
from pydantic import BaseModel
from typing import List, Dict, Tuple

class Order(BaseModel):
    items: List[Item]
    discounts: Dict[str, float]
    address: Tuple[str, str]

```
### Комбіновані Типи

Використовуйте комбіновані типи для складних структур даних:

```python
from pydantic import BaseModel
from typing import Union, Optional

class Event(BaseModel):
    event_type: str
    event_data: Union[Order, User] #  Order | User
	some_optional_data: Optional[str] # str | None , Union[str, None]
```

## Наслідування Моделей

Ви можете створювати більш складні моделі, успадковуючи їх від інших:
```python
class Employee(User):
    department: str
    role: str

```
Це дозволяє використовувати та розширювати вже існуючі моделі.

## Приклади Використання
### Валідація Даних
```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    username: str
    email: str

user_data = {"id": 1, "username": "john_doe"}
user = User(**user_data)
```

### Використання в Fast API
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    id: int
    username: str
    email: str

@app.post("/create_user")
async def create_user(user: User):
    return {"message": "Користувач створений", "user_data": user.dict()}
```

## приклад використання вкладених моделей в Pydantic:

```python
from pydantic import BaseModel

# Визначаємо модель "Адреса"
class Address(BaseModel):
    street: str
    city: str
    postal_code: str

# Визначаємо модель "Користувач"
class User(BaseModel):
    id: int
    username: str
    email: str
    address: Address  # Вкладена модель "Адреса"

# Створюємо об'єкт "Адреса"
address_data = {"street": "123 Main St", "city": "Sampleville", "postal_code": "12345"}
address = Address(**address_data)

# Створюємо об'єкт "Користувача" з вкладеною адресою
user_data = {"id": 1, "username": "john_doe", "email": "john@example.com", "address": address}
user = User(**user_data)

# Виводимо дані користувача з вкладеною адресою
print(user.json())
```

У цьому прикладі ми визначили дві моделі: "Адреса" та "Користувач". Поле "address" в моделі "Користувач" вказує на вкладену модель "Адреса". Під час створення об'єкта "Користувач" ми передаємо дані для вкладеної адреси.

Цей приклад ілюструє, як використовувати вкладені моделі в Pydantic для створення складних структур даних з багатьма полями.


## Поширені Проблеми та Недоліки Pydantic

Pydantic - потужний інструмент для валідації та серіалізації даних у Python. Однак, як і у будь-якої бібліотеки, є деякі поширені проблеми та недоліки, які варто враховувати при використанні Pydantic у вашому проекті.

### 1. Збільшена складність
Pydantic може бути корисним для валідації даних, але додавання ваших моделей та схем може призвести до збільшеної складності вашого коду. Це може бути особливо помітно у великих проектах, де потрібно визначати багато моделей.

### 2. Додаткові Завдання Обробки Помилок
Помилки в Pydantic можуть бути не завжди легко зрозуміти та обробляти. При валідації даних може виникнути багато видів помилок, і обробка їх може бути важливим завданням.

### 3. Додатковий Код
Інколи вам доведеться написати додатковий код для обробки додаткової логіки, яка не покрита Pydantic. Це може призвести до більшого обсягу коду та підвищення складності.
