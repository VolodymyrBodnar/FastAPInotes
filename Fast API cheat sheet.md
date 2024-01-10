
### Встановлення FastAPI
```bash
pip install fastapi
```

### Створення Додатку

Створення FastAPI додатку в файлі `main.py`:

```python
from fastapi import FastAPI

app = FastAPI()
```

### Додавання Шляхів (Endpoints)

```python
@app.get("/")
def read_root():
    return {"message": "Hello, World!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, query_param: str = None):
    return {"item_id": item_id, "query_param": query_param}
```

### Запуск Додатку

Запуск FastAPI додатку за допомогою команди:

```bash
uvicorn main:app --reload
```

або
```bash
python -m uvicorn main:app --reload

```
 для явного вказання інтерпретатора пайтон який використовувати

### Доступ до Документації

Після запуску, документація доступна за адресою:
`http://localhost:8000/docs`

### Залежності (Dependencies)

```python
from fastapi import Depends

def get_db():
    return "Database Connection"

@app.get("/items/")
def read_items(db: str = Depends(get_db)):
    return {"db_connection": db}
```
[[Dependency Injection]]
### Валідація Даних

Використання Pydantic моделей для валідації вхідних даних:

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/items/")
def create_item(item: Item):
    return item
```
[[Pydantic]]
### Відповіді та Статуси

```python
from fastapi import HTTPException

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id == 1:
        return {"item_id": item_id}
    else:
        raise HTTPException(status_code=404, detail="Item not found")
```

### Заголовки (Headers)

```python
from fastapi import Header

@app.get("/items/")
def read_items(user_agent: str = Header(None)):
    return {"User-Agent": user_agent}
```

### Cookie

```python
from fastapi import Cookie

@app.get("/items/")
def read_items(session_id: str = Cookie(None)):
    return {"session_id": session_id}
```

###  Background завдання

```python
from fastapi import BackgroundTasks

def send_email(background_tasks: BackgroundTasks, email: str, message: str):
    # Виконується асинхронно в фоновому режимі
    background_tasks.add_task(fake_send_email, email, message)

@app.post("/send-notification/{email}")
def send_notification(
    email: str, message: str, background_tasks: BackgroundTasks
):
    background_tasks.add_task(send_email, email, message)
    return {"message": "Message sent"}
```

### User-Defined Middleware

```python
from fastapi import FastAPI, Request, HTTPException

app = FastAPI()

@app.middleware("http")
async def custom_middleware(request: Request, call_next):
    # Виконується перед кожним запитом
    response = await call_next(request)
    # Виконується після обробки запиту
    return response

@app.get("/")
def read_root():
    return {"message": "Hello, World!"}
```

Детальніше що таке [[[Middleware]]]

Це лише декілька основних прикладів та понять в FastAPI. Додаток FastAPI може бути значно складнішим і має багато інших можливостей для веб-розробки. Для докладної інформації та документації відвідайте офіційний сайт FastAPI: https://fastapi.tiangolo.com/

