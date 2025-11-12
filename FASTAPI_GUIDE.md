# ⚡ Guia FastAPI - Backend Prevent

## 🎯 Por que FastAPI?

- **Performance:** Até 3x mais rápido que Django (comparável a Node.js e Go)
- **Async nativo:** Suporte completo a operações assíncronas
- **Documentação automática:** Swagger UI e ReDoc out-of-the-box
- **Type hints:** Validação automática com Pydantic
- **Moderno:** Python 3.11+ com as melhores práticas
- **WebSockets nativos:** Para alertas em tempo real

---

## 📦 Dependências Principais (requirements.txt)

```txt
# Core
fastapi==0.104.1
uvicorn[standard]==0.24.0
python-multipart==0.0.6

# Database
sqlalchemy[asyncio]==2.0.23
asyncpg==0.29.0
alembic==1.12.1

# Authentication
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6

# Validation
pydantic==2.5.0
pydantic-settings==2.1.0
email-validator==2.1.0

# HTTP Client (APIs externas)
httpx==0.25.2

# Cache
redis==5.0.1
aioredis==2.0.1

# Background Tasks (opcional, FastAPI tem built-in)
celery==5.3.4  # Se precisar de tasks mais complexas

# Firebase
firebase-admin==6.3.0

# Utils
python-dateutil==2.8.2
python-dotenv==1.0.0

# Testing
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2  # Para TestClient
faker==20.1.0

# Development
black==23.12.0
ruff==0.1.8
```

---

## 🚀 Arquivo Principal (main.py)

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.core.config import settings
from app.api.v1.api import api_router

app = FastAPI(
    title=settings.PROJECT_NAME,
    version=settings.VERSION,
    description="API de Monitoramento de Desastres Naturais",
    docs_url="/docs",  # Swagger UI
    redoc_url="/redoc",  # ReDoc
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Rotas
app.include_router(api_router, prefix=settings.API_V1_STR)

@app.get("/")
async def root():
    return {
        "message": "Prevent API",
        "version": settings.VERSION,
        "docs": "/docs",
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy"}
```

---

## ⚙️ Configurações (app/core/config.py)

```python
from pydantic_settings import BaseSettings
from typing import List

class Settings(BaseSettings):
    # API
    PROJECT_NAME: str = "Prevent API"
    VERSION: str = "1.0.0"
    API_V1_STR: str = "/api/v1"
    
    # Security
    SECRET_KEY: str
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60
    REFRESH_TOKEN_EXPIRE_DAYS: int = 7
    
    # Database
    DATABASE_URL: str
    
    # Redis
    REDIS_URL: str = "redis://localhost:6379"
    
    # CORS
    ALLOWED_ORIGINS: List[str] = [
        "http://localhost:3000",
        "http://localhost:5173",
    ]
    
    # External APIs
    INMET_API_KEY: str = ""
    CEMADEN_API_KEY: str = ""
    INPE_API_KEY: str = ""
    
    # Firebase
    FIREBASE_CREDENTIALS_PATH: str = ""
    
    class Config:
        env_file = ".env"
        case_sensitive = True

settings = Settings()
```

---

## 🗄️ Database (app/core/database.py)

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from app.core.config import settings

# Async engine
engine = create_async_engine(
    settings.DATABASE_URL,
    echo=True,  # Log SQL queries (desabilitar em produção)
    future=True,
)

# Async session
AsyncSessionLocal = sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False,
)

Base = declarative_base()

# Dependency
async def get_db():
    async with AsyncSessionLocal() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
        finally:
            await session.close()
```

---

## 🔐 Security (app/core/security.py)

```python
from datetime import datetime, timedelta
from typing import Optional
from jose import jwt
from passlib.context import CryptContext
from app.core.config import settings

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(
            minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES
        )
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(
        to_encode, settings.SECRET_KEY, algorithm=settings.ALGORITHM
    )
    return encoded_jwt
```

---

## 📋 Base Model (app/models/base.py)

```python
from datetime import datetime
from sqlalchemy import Column, Integer, DateTime
from app.core.database import Base

class BaseModel(Base):
    __abstract__ = True
    
    id = Column(Integer, primary_key=True, index=True)
    created_at = Column(DateTime, default=datetime.utcnow, nullable=False)
    updated_at = Column(
        DateTime,
        default=datetime.utcnow,
        onupdate=datetime.utcnow,
        nullable=False,
    )
```

---

## 📝 Exemplo de Model (app/models/location.py)

```python
from sqlalchemy import Column, String, Integer, Numeric, ForeignKey, JSON, Boolean
from sqlalchemy.orm import relationship
from app.models.base import BaseModel

class Estado(BaseModel):
    __tablename__ = "estados"
    
    nome = Column(String(100), nullable=False)
    sigla = Column(String(2), unique=True, nullable=False, index=True)
    codigo_ibge = Column(String(2), unique=True, nullable=False)
    regiao = Column(String(20), nullable=False)
    populacao = Column(Integer, nullable=True)
    area_km2 = Column(Numeric(12, 2), nullable=True)
    
    # Relacionamentos
    cidades = relationship("Cidade", back_populates="estado")

class Cidade(BaseModel):
    __tablename__ = "cidades"
    
    nome = Column(String(200), nullable=False)
    estado_id = Column(Integer, ForeignKey("estados.id"), nullable=False)
    codigo_ibge = Column(String(7), unique=True, nullable=False)
    latitude = Column(Numeric(10, 7), nullable=False)
    longitude = Column(Numeric(10, 7), nullable=False)
    populacao = Column(Integer, nullable=True)
    
    # Relacionamentos
    estado = relationship("Estado", back_populates="cidades")
    bairros = relationship("Bairro", back_populates="cidade")
```

---

## 🎨 Exemplo de Schema (app/schemas/location.py)

```python
from pydantic import BaseModel, Field
from typing import Optional, List
from datetime import datetime

# Base
class EstadoBase(BaseModel):
    nome: str = Field(..., min_length=1, max_length=100)
    sigla: str = Field(..., min_length=2, max_length=2)
    codigo_ibge: str = Field(..., min_length=2, max_length=2)
    regiao: str
    populacao: Optional[int] = None
    area_km2: Optional[float] = None

# Create (sem id, timestamps)
class EstadoCreate(EstadoBase):
    pass

# Update (todos campos opcionais)
class EstadoUpdate(BaseModel):
    nome: Optional[str] = None
    populacao: Optional[int] = None
    area_km2: Optional[float] = None

# Response (com id, timestamps)
class EstadoResponse(EstadoBase):
    id: int
    created_at: datetime
    updated_at: datetime
    
    class Config:
        from_attributes = True  # Pydantic v2 (antes era orm_mode)

# Com relacionamentos
class EstadoWithCidades(EstadoResponse):
    cidades: List["CidadeResponse"] = []
```

---

## 🛣️ Exemplo de Router (app/api/v1/endpoints/locations.py)

```python
from fastapi import APIRouter, Depends, HTTPException, Query
from sqlalchemy.ext.asyncio import AsyncSession
from typing import List
from app.api.deps import get_db
from app.crud import crud_estado
from app.schemas.location import EstadoResponse, EstadoCreate, EstadoUpdate

router = APIRouter()

@router.get("/estados", response_model=List[EstadoResponse])
async def list_estados(
    skip: int = Query(0, ge=0),
    limit: int = Query(100, ge=1, le=100),
    db: AsyncSession = Depends(get_db),
):
    """Lista todos os estados com paginação"""
    estados = await crud_estado.get_multi(db, skip=skip, limit=limit)
    return estados

@router.get("/estados/{estado_id}", response_model=EstadoResponse)
async def get_estado(
    estado_id: int,
    db: AsyncSession = Depends(get_db),
):
    """Retorna um estado específico"""
    estado = await crud_estado.get(db, id=estado_id)
    if not estado:
        raise HTTPException(status_code=404, detail="Estado não encontrado")
    return estado

@router.post("/estados", response_model=EstadoResponse, status_code=201)
async def create_estado(
    estado_in: EstadoCreate,
    db: AsyncSession = Depends(get_db),
):
    """Cria um novo estado"""
    estado = await crud_estado.create(db, obj_in=estado_in)
    return estado

@router.put("/estados/{estado_id}", response_model=EstadoResponse)
async def update_estado(
    estado_id: int,
    estado_in: EstadoUpdate,
    db: AsyncSession = Depends(get_db),
):
    """Atualiza um estado"""
    estado = await crud_estado.get(db, id=estado_id)
    if not estado:
        raise HTTPException(status_code=404, detail="Estado não encontrado")
    estado = await crud_estado.update(db, db_obj=estado, obj_in=estado_in)
    return estado

@router.delete("/estados/{estado_id}", status_code=204)
async def delete_estado(
    estado_id: int,
    db: AsyncSession = Depends(get_db),
):
    """Deleta um estado"""
    estado = await crud_estado.get(db, id=estado_id)
    if not estado:
        raise HTTPException(status_code=404, detail="Estado não encontrado")
    await crud_estado.remove(db, id=estado_id)
    return None
```

---

## 🔧 CRUD Base (app/crud/base.py)

```python
from typing import Generic, TypeVar, Type, Optional, List, Any
from pydantic import BaseModel
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.models.base import BaseModel as DBBaseModel

ModelType = TypeVar("ModelType", bound=DBBaseModel)
CreateSchemaType = TypeVar("CreateSchemaType", bound=BaseModel)
UpdateSchemaType = TypeVar("UpdateSchemaType", bound=BaseModel)

class CRUDBase(Generic[ModelType, CreateSchemaType, UpdateSchemaType]):
    def __init__(self, model: Type[ModelType]):
        self.model = model
    
    async def get(self, db: AsyncSession, id: int) -> Optional[ModelType]:
        result = await db.execute(select(self.model).where(self.model.id == id))
        return result.scalar_one_or_none()
    
    async def get_multi(
        self, db: AsyncSession, *, skip: int = 0, limit: int = 100
    ) -> List[ModelType]:
        result = await db.execute(select(self.model).offset(skip).limit(limit))
        return result.scalars().all()
    
    async def create(self, db: AsyncSession, *, obj_in: CreateSchemaType) -> ModelType:
        obj_data = obj_in.model_dump()
        db_obj = self.model(**obj_data)
        db.add(db_obj)
        await db.flush()
        await db.refresh(db_obj)
        return db_obj
    
    async def update(
        self,
        db: AsyncSession,
        *,
        db_obj: ModelType,
        obj_in: UpdateSchemaType | dict[str, Any]
    ) -> ModelType:
        if isinstance(obj_in, dict):
            update_data = obj_in
        else:
            update_data = obj_in.model_dump(exclude_unset=True)
        
        for field, value in update_data.items():
            setattr(db_obj, field, value)
        
        db.add(db_obj)
        await db.flush()
        await db.refresh(db_obj)
        return db_obj
    
    async def remove(self, db: AsyncSession, *, id: int) -> ModelType:
        obj = await self.get(db, id=id)
        await db.delete(obj)
        await db.flush()
        return obj
```

---

## 🧪 Exemplo de Teste (tests/test_api/test_locations.py)

```python
import pytest
from httpx import AsyncClient
from app.main import app

@pytest.mark.asyncio
async def test_list_estados():
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.get("/api/v1/estados")
    
    assert response.status_code == 200
    assert isinstance(response.json(), list)

@pytest.mark.asyncio
async def test_create_estado():
    estado_data = {
        "nome": "São Paulo",
        "sigla": "SP",
        "codigo_ibge": "35",
        "regiao": "Sudeste",
        "populacao": 46649132,
    }
    
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post("/api/v1/estados", json=estado_data)
    
    assert response.status_code == 201
    data = response.json()
    assert data["nome"] == "São Paulo"
    assert data["sigla"] == "SP"
    assert "id" in data
```

---

## 🔄 Migrações com Alembic

### Inicializar Alembic
```bash
alembic init alembic
```

### Criar migração
```bash
alembic revision --autogenerate -m "create estados table"
```

### Aplicar migrações
```bash
alembic upgrade head
```

### Reverter migração
```bash
alembic downgrade -1
```

---

## 🚀 Comandos para Rodar

### Desenvolvimento
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Produção
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4
```

### Com Gunicorn (recomendado para produção)
```bash
gunicorn main:app --workers 4 --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

---

## 📊 Endpoints Automáticos

- **Swagger UI:** `http://localhost:8000/docs`
- **ReDoc:** `http://localhost:8000/redoc`
- **OpenAPI JSON:** `http://localhost:8000/openapi.json`

---

## 🔥 Vantagens do FastAPI sobre Django

| Recurso | FastAPI | Django REST Framework |
|---------|---------|----------------------|
| **Performance** | ⚡ Muito rápida (async) | 🐢 Mais lenta (sync) |
| **Documentação** | ✅ Automática (Swagger) | ❌ Manual (drf-spectacular) |
| **Type Safety** | ✅ Pydantic nativo | ⚠️ Serializers manuais |
| **WebSockets** | ✅ Nativo | ❌ Precisa Channels |
| **Async/Await** | ✅ Nativo | ⚠️ Limitado |
| **Curva de Aprendizado** | 📈 Moderada | 📈 Mais íngreme |
| **Admin Panel** | ❌ Não tem | ✅ Django Admin |
| **ORM** | SQLAlchemy | Django ORM |

---

**Última atualização:** 11/11/2025  
**Versão:** 1.0.0
