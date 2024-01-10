Рекомендую ознайомитись з цією документацією:
https://fastapi.tiangolo.com/tutorial/sql-databases/

## Основні кроки використання SQLAlchemy в FastAPI для SQLite:

### Імпортування бібліотек:
 
   Спочатку вам потрібно імпортувати необхідні бібліотеки: `fastapi`, `sqlalchemy`, та `sqlalchemy.orm`.

### Створення об'єкта бази даних:
   Створіть об'єкт бази даних SQLAlchemy, який представлятиме вашу базу даних SQLite. Використовуйте URL-рядок для зазначення шляху до вашої бази даних.

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import DeclarativeBase
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(DATABASE_URL)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = DeclarativeBase()
```

### Створення моделей даних:
   Визначте класи, які представлятимуть ваші таблиці у базі даних. Вони мають успадковувати `Base` та мати атрибути, які відповідають стовпцям таблиць.

```python
from sqlalchemy import Column, Integer, String

class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    description = Column(String)
```

### Створення та налаштування міграцій (опціонально):
   Якщо вам потрібно змінити схему бази даних, ви можете використовувати міграції, наприклад, `alembic`, для оновлення структури таблиць.


### Створення залежності для сесії бази даних:

```python
from sqlalchemy.orm import Session

# Залежність для отримання сесії бази даних
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

Цей код створює функцію `get_db`, яка створює сесію бази даних SQLAlchemy та закриває її після використання. Використовуючи `yield`, ви забезпечуєте одноразовий доступ до сесії у ваших маршрутах.

### Використання залежності у маршрутах:

```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session

app = FastAPI()

# Приклад створення запису з використанням Depends
@app.post("/items/")
def create_item(item: Item, db: Session = Depends(get_db)):
    db_item = Item(**item.dict())
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item

# Приклад отримання запису за ідентифікатором з використанням Depends
@app.get("/items/{item_id}")
def read_item(item_id: int, db: Session = Depends(get_db)):
    db_item = db.query(Item).filter(Item.id == item_id).first()
    if db_item is None:
        raise HTTPException(status_code=404, detail="Item not found")
    return db_item
```

У цих маршрутах ми використовуємо аргумент `db` зі значенням за замовчуванням, яке отримуємо за допомогою `Depends(get_db)`. Кожен раз, коли запит виконується для цих маршрутів, FastAPI автоматично створює сесію бази даних і передає її як аргумент до функції маршруту. Після завершення обробки запиту, сесія закривається автоматично завдяки `finally` у функції `get_db`.

Такий підхід допомагає забезпечити правильне використання та закриття сесій бази даних у вашому FastAPI додатку, роблячи код більш чистим та безпечним.