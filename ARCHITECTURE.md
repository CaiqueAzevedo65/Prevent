# 🏗️ Arquitetura do Sistema Prevent

## 📋 Visão Geral

O Prevent é um sistema multiplataforma de monitoramento de desastres naturais composto por três camadas principais:

```
┌─────────────────────────────────────────────────────────┐
│                    CAMADA DE APRESENTAÇÃO                │
├──────────────────────────┬──────────────────────────────┤
│   Frontend Web (React)   │   Mobile (React Native)      │
│   - Vite + Bootstrap     │   - Expo + TailwindCSS       │
│   - Leaflet Maps         │   - React Native Maps        │
│   - Chart.js             │   - Push Notifications       │
└──────────────────────────┴──────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                    CAMADA DE API                         │
│                    FastAPI (Async)                       │
│   - JWT Authentication (OAuth2)                          │
│   - Swagger/OpenAPI (Automático)                         │
│   - CORS configurado                                     │
│   - WebSockets nativos                                   │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                  CAMADA DE NEGÓCIO                       │
│              Services & Business Logic                   │
│   ┌─────────┬──────────┬─────────┬──────────────────┐  │
│   │ Auth    │ Locations│ Disasters│ Alerts & Notif.  │  │
│   └─────────┴──────────┴─────────┴──────────────────┘  │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│                  CAMADA DE DADOS                         │
│   ┌──────────────┬──────────────┬──────────────────┐   │
│   │  PostgreSQL  │    Redis     │  APIs Externas   │   │
│   │  (asyncpg)   │   (Cache)    │  (httpx async)   │   │
│   └──────────────┴──────────────┴──────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## ⚡ Backend FastAPI - Estrutura Detalhada

### Estrutura de Diretórios

```
backend/
├── main.py                     # Entry point FastAPI
├── requirements.txt
├── .env.example
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── README.md
├── alembic.ini                 # Configuração Alembic
│
├── alembic/                    # Migrações de banco
│   ├── env.py
│   ├── script.py.mako
│   └── versions/
│       └── (arquivos de migração)
│
├── app/
│   ├── __init__.py
│   │
│   ├── core/                   # Configurações e dependências
│   │   ├── __init__.py
│   │   ├── config.py           # Settings (Pydantic BaseSettings)
│   │   ├── security.py         # JWT, hashing, OAuth2
│   │   ├── database.py         # SQLAlchemy async engine
│   │   └── deps.py             # Dependency injection
│   │
│   ├── models/                 # SQLAlchemy Models
│   │   ├── __init__.py
│   │   ├── base.py             # Base model com timestamps
│   │   ├── user.py             # User, UserProfile, UserPreferences
│   │   ├── location.py         # Estado, Cidade, Bairro, AreaDeRisco
│   │   ├── disaster.py         # TipoDesastre, Desastre, Historico
│   │   ├── alert.py            # Alerta, NivelRisco, AlertaUsuario
│   │   └── notification.py     # Notification, NotificationPreference
│   │
│   ├── schemas/                # Pydantic Schemas (validação)
│   │   ├── __init__.py
│   │   ├── user.py             # UserCreate, UserUpdate, UserResponse
│   │   ├── location.py         # EstadoSchema, CidadeSchema, etc.
│   │   ├── disaster.py         # Desastre schemas
│   │   ├── alert.py            # Alerta schemas
│   │   ├── notification.py     # Notification schemas
│   │   └── token.py            # Token, TokenPayload
│   │
│   ├── api/                    # Rotas da API
│   │   ├── __init__.py
│   │   ├── deps.py             # Dependencies (get_db, get_current_user)
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── api.py          # Router principal v1
│   │       └── endpoints/
│   │           ├── __init__.py
│   │           ├── auth.py     # Login, register, refresh
│   │           ├── users.py    # CRUD usuários
│   │           ├── locations.py    # Estados, cidades, bairros
│   │           ├── disasters.py    # Desastres e histórico
│   │           ├── alerts.py       # Alertas
│   │           └── notifications.py # Notificações
│   │
│   ├── crud/                   # CRUD operations
│   │   ├── __init__.py
│   │   ├── base.py             # CRUDBase genérico
│   │   ├── user.py             # CRUD para User
│   │   ├── location.py         # CRUD para localidades
│   │   ├── disaster.py         # CRUD para desastres
│   │   ├── alert.py            # CRUD para alertas
│   │   └── notification.py     # CRUD para notificações
│   │
│   ├── services/               # Lógica de negócio
│   │   ├── __init__.py
│   │   ├── auth.py             # Autenticação e autorização
│   │   ├── disaster.py         # Processamento de desastres
│   │   ├── alert.py            # Geração e envio de alertas
│   │   └── notification.py     # Envio de notificações
│   │
│   ├── external/               # Integração com APIs externas
│   │   ├── __init__.py
│   │   ├── base.py             # Cliente HTTP base (httpx)
│   │   ├── inmet.py            # API INMET
│   │   ├── cemaden.py          # API CEMADEN
│   │   └── inpe.py             # API INPE (queimadas)
│   │
│   ├── utils/                  # Funções utilitárias
│   │   ├── __init__.py
│   │   ├── cache.py            # Redis cache helpers
│   │   ├── email.py            # Envio de emails
│   │   ├── firebase.py         # Firebase Cloud Messaging
│   │   └── validators.py       # Validadores customizados
│   │
│   └── websockets/             # WebSocket handlers
│       ├── __init__.py
│       ├── manager.py          # Connection manager
│       └── alerts.py           # WebSocket para alertas
│
└── tests/                      # Testes
    ├── __init__.py
    ├── conftest.py             # Fixtures pytest
    ├── test_api/
    │   ├── test_auth.py
    │   ├── test_users.py
    │   ├── test_locations.py
    │   ├── test_disasters.py
    │   └── test_alerts.py
    ├── test_services/
    └── test_external/
```

---

## 📊 Modelos de Dados (SQLAlchemy Models)

### Módulo: `models/location.py`

```python
from sqlalchemy import Column, Integer, String, Numeric, ForeignKey, JSON, Boolean
from sqlalchemy.orm import relationship
from app.models.base import BaseModel

# Estado
class Estado(BaseModel):
    __tablename__ = "estados"
    
    nome = Column(String(100), nullable=False)
    sigla = Column(String(2), unique=True, nullable=False, index=True)
    codigo_ibge = Column(String(2), unique=True, nullable=False)
    regiao = Column(String(20), nullable=False)  # Norte, Sul, etc.
    populacao = Column(Integer, nullable=True)
    area_km2 = Column(Numeric(12, 2), nullable=True)
    
# Cidade
class Cidade(models.Model):
    nome = models.CharField(max_length=200)
    estado = models.ForeignKey(Estado, on_delete=models.CASCADE)
    codigo_ibge = models.CharField(max_length=7, unique=True)
    latitude = models.DecimalField(max_digits=10, decimal_places=7)
    longitude = models.DecimalField(max_digits=10, decimal_places=7)
    populacao = models.IntegerField(null=True)
    
# Bairro
class Bairro(models.Model):
    nome = models.CharField(max_length=200)
    cidade = models.ForeignKey(Cidade, on_delete=models.CASCADE)
    latitude = models.DecimalField(max_digits=10, decimal_places=7, null=True)
    longitude = models.DecimalField(max_digits=10, decimal_places=7, null=True)
    
# Área de Risco
class AreaDeRisco(models.Model):
    bairro = models.ForeignKey(Bairro, on_delete=models.CASCADE)
    tipo_risco = models.CharField(max_length=50)  # enchente, deslizamento, etc.
    nivel_risco = models.CharField(max_length=20)  # baixo, médio, alto, muito alto
    descricao = models.TextField()
    coordenadas_poligono = models.JSONField()  # GeoJSON
    ativo = models.BooleanField(default=True)
```

### App: `disasters`

```python
# Tipo de Desastre
class TipoDesastre(models.Model):
    nome = models.CharField(max_length=100)  # Enchente, Deslizamento, etc.
    codigo = models.CharField(max_length=20, unique=True)
    descricao = models.TextField()
    cor_hex = models.CharField(max_length=7)  # Para UI
    icone = models.CharField(max_length=50)  # Nome do ícone
    
# Desastre
class Desastre(models.Model):
    tipo = models.ForeignKey(TipoDesastre, on_delete=models.PROTECT)
    cidade = models.ForeignKey(Cidade, on_delete=models.CASCADE)
    bairro = models.ForeignKey(Bairro, on_delete=models.CASCADE, null=True)
    titulo = models.CharField(max_length=200)
    descricao = models.TextField()
    data_ocorrencia = models.DateTimeField()
    data_fim = models.DateTimeField(null=True)
    gravidade = models.CharField(max_length=20)  # baixa, média, alta, crítica
    vitimas = models.IntegerField(default=0)
    desabrigados = models.IntegerField(default=0)
    status = models.CharField(max_length=20)  # ativo, controlado, finalizado
    fonte = models.CharField(max_length=100)  # defesa_civil, api_externa, etc.
    
# Histórico de Desastres
class HistoricoDesastre(models.Model):
    desastre = models.ForeignKey(Desastre, on_delete=models.CASCADE)
    data_atualizacao = models.DateTimeField(auto_now_add=True)
    status_anterior = models.CharField(max_length=20)
    status_novo = models.CharField(max_length=20)
    observacoes = models.TextField()
```

### App: `alerts`

```python
# Alerta
class Alerta(models.Model):
    tipo_desastre = models.ForeignKey(TipoDesastre, on_delete=models.PROTECT)
    cidade = models.ForeignKey(Cidade, on_delete=models.CASCADE)
    bairro = models.ForeignKey(Bairro, on_delete=models.CASCADE, null=True)
    titulo = models.CharField(max_length=200)
    mensagem = models.TextField()
    nivel_urgencia = models.CharField(max_length=20)  # baixo, médio, alto, crítico
    data_inicio = models.DateTimeField()
    data_fim = models.DateTimeField(null=True)
    ativo = models.BooleanField(default=True)
    fonte = models.CharField(max_length=100)
    
# Alerta de Usuário (relação many-to-many)
class AlertaUsuario(models.Model):
    alerta = models.ForeignKey(Alerta, on_delete=models.CASCADE)
    usuario = models.ForeignKey(User, on_delete=models.CASCADE)
    visualizado = models.BooleanField(default=False)
    data_visualizacao = models.DateTimeField(null=True)
    notificado = models.BooleanField(default=False)
```

### App: `users`

```python
# Perfil de Usuário
class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    telefone = models.CharField(max_length=20, null=True)
    cidade = models.ForeignKey(Cidade, on_delete=models.SET_NULL, null=True)
    bairro = models.ForeignKey(Bairro, on_delete=models.SET_NULL, null=True)
    foto_perfil = models.ImageField(upload_to='perfis/', null=True)
    
# Preferências de Usuário
class UserPreferences(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    notificacoes_push = models.BooleanField(default=True)
    notificacoes_email = models.BooleanField(default=True)
    notificacoes_sms = models.BooleanField(default=False)
    tipos_desastre_interesse = models.ManyToManyField(TipoDesastre)
    raio_alerta_km = models.IntegerField(default=50)  # Raio de interesse
    tema = models.CharField(max_length=20, default='claro')  # claro, escuro, sistema
```

---

## 🔌 Endpoints da API

### Base URL: `/api/v1/`

#### **Autenticação** (`/auth/`)
```
POST   /auth/register/          # Registro de novo usuário
POST   /auth/login/             # Login (retorna JWT)
POST   /auth/refresh/           # Refresh token
POST   /auth/logout/            # Logout
GET    /auth/me/                # Dados do usuário logado
PUT    /auth/me/                # Atualizar perfil
```

#### **Localidades** (`/locations/`)
```
GET    /estados/                # Lista todos os estados
GET    /estados/{id}/           # Detalhes de um estado
GET    /estados/{id}/cidades/   # Cidades de um estado

GET    /cidades/                # Lista cidades (com filtros)
GET    /cidades/{id}/           # Detalhes de uma cidade
GET    /cidades/{id}/bairros/   # Bairros de uma cidade
GET    /cidades/buscar/?q=      # Busca por nome

GET    /bairros/                # Lista bairros
GET    /bairros/{id}/           # Detalhes de um bairro
GET    /bairros/{id}/areas-risco/ # Áreas de risco do bairro
```

#### **Desastres** (`/disasters/`)
```
GET    /tipos/                  # Tipos de desastre
GET    /desastres/              # Lista desastres (filtros: cidade, tipo, data)
GET    /desastres/{id}/         # Detalhes de um desastre
GET    /desastres/ativos/       # Desastres ativos
GET    /historico/              # Histórico de desastres
GET    /estatisticas/           # Estatísticas gerais
```

#### **Alertas** (`/alerts/`)
```
GET    /alertas/                # Lista alertas (filtros: cidade, ativo)
GET    /alertas/{id}/           # Detalhes de um alerta
GET    /alertas/ativos/         # Alertas ativos
GET    /alertas/meus/           # Alertas do usuário logado
POST   /alertas/{id}/visualizar/ # Marcar como visualizado
```

#### **Notificações** (`/notifications/`)
```
GET    /notificacoes/           # Lista notificações do usuário
GET    /notificacoes/{id}/      # Detalhes de uma notificação
POST   /notificacoes/{id}/ler/  # Marcar como lida
GET    /preferencias/           # Preferências de notificação
PUT    /preferencias/           # Atualizar preferências
POST   /dispositivos/           # Registrar dispositivo (FCM token)
```

---

## 🔐 Autenticação e Segurança

### JWT (JSON Web Tokens)
- **Access Token:** Válido por 1 hora
- **Refresh Token:** Válido por 7 dias
- Armazenamento seguro no cliente (HttpOnly cookies ou AsyncStorage)

### Permissões
- **Público:** Acesso a dados de localidades e desastres
- **Autenticado:** Acesso a alertas personalizados e notificações
- **Admin:** Acesso ao Django Admin e criação de alertas

### CORS
- Configurado para aceitar requisições do frontend web e mobile
- Headers permitidos: Authorization, Content-Type

---

## 🔄 Integração com APIs Externas

### INMET (Instituto Nacional de Meteorologia)
- **Endpoint:** `https://apitempo.inmet.gov.br/`
- **Dados:** Previsão do tempo, precipitação, temperatura
- **Frequência:** Atualização a cada 1 hora (Celery task)

### CEMADEN (Centro Nacional de Monitoramento e Alertas)
- **Endpoint:** `http://www.cemaden.gov.br/`
- **Dados:** Alertas de desastres, pluviometria
- **Frequência:** Atualização a cada 30 minutos

### INPE (Instituto Nacional de Pesquisas Espaciais)
- **Endpoint:** `https://queimadas.dgi.inpe.br/`
- **Dados:** Focos de incêndio
- **Frequência:** Atualização a cada 3 horas

### Cache Strategy
- Redis para cache de respostas de APIs externas
- TTL: 1 hora para dados meteorológicos, 30 minutos para alertas

---

## 📦 Dependências Principais (requirements.txt)

```txt
# Core
Django==5.0.0
djangorestframework==3.14.0
djangorestframework-simplejwt==5.3.0

# Database
psycopg2-binary==2.9.9
dj-database-url==2.1.0

# Cache & Tasks
redis==5.0.1
celery==5.3.4
django-redis==5.4.0

# API Documentation
drf-spectacular==0.27.0

# CORS
django-cors-headers==4.3.1

# Environment
python-decouple==3.8

# External APIs
requests==2.31.0
httpx==0.25.2

# Notifications
firebase-admin==6.3.0

# Testing
pytest==7.4.3
pytest-django==4.7.0
pytest-cov==4.1.0
factory-boy==3.3.0

# Utils
python-dateutil==2.8.2
Pillow==10.1.0
```

---

## 🐳 Docker Setup

### docker-compose.yml
```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: prevent_db
      POSTGRES_USER: prevent_user
      POSTGRES_PASSWORD: prevent_pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  backend:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    depends_on:
      - db
      - redis
    env_file:
      - .env

  celery:
    build: .
    command: celery -A prevent worker -l info
    volumes:
      - .:/app
    depends_on:
      - db
      - redis
    env_file:
      - .env

volumes:
  postgres_data:
```

---

## 🧪 Testes

### Estrutura de Testes
- **Unitários:** Testes de models, serializers, utils
- **Integração:** Testes de endpoints da API
- **Cobertura mínima:** 80%

### Comandos
```bash
# Rodar todos os testes
pytest

# Com cobertura
pytest --cov=apps --cov-report=html

# Testes específicos
pytest apps/users/tests/
```

---

## 📈 Monitoramento e Logs

### Sentry
- Error tracking em produção
- Alertas para erros críticos

### Logs
- Logs estruturados (JSON)
- Níveis: DEBUG, INFO, WARNING, ERROR, CRITICAL
- Rotação de logs diária

### Métricas
- Tempo de resposta de endpoints
- Taxa de erro
- Uso de cache
- Requisições por minuto

---

## 🚀 Deploy

### Checklist de Deploy
- [ ] Configurar variáveis de ambiente
- [ ] Migrar banco de dados
- [ ] Coletar arquivos estáticos
- [ ] Configurar SSL/HTTPS
- [ ] Configurar domínio
- [ ] Setup de Celery workers
- [ ] Configurar backups automáticos
- [ ] Configurar monitoramento

### Plataformas Recomendadas
- **Railway:** Fácil setup, $10-30/mês
- **Render:** Tier gratuito disponível
- **AWS/GCP:** Mais controle, requer mais configuração

---

**Última atualização:** 11/11/2025
