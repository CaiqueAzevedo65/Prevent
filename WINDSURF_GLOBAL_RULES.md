# 📋 Diretrizes Globais para a IDE Windsurf

Este documento define as regras de desenvolvimento que devem ser seguidas em **todos os projetos**.

---

# ⚠️ REGRA DE OURO (LEIA PRIMEIRO)

## **O PADRÃO DO PROJETO EXISTENTE TEM PRIORIDADE ABSOLUTA SOBRE QUALQUER REGRA DESTE DOCUMENTO.**

Antes de escrever qualquer código:

1. ✅ **Analise 3-5 arquivos similares** no projeto
2. ✅ **Identifique padrões** de nomenclatura, imports, formatação
3. ✅ **Leia arquivos de configuração** (package.json, .prettierrc, pyproject.toml, etc.)
4. ✅ **Observe a arquitetura** e complexidade do código existente
5. ✅ **Detecte ferramentas** de linting e formatação em uso

**As regras abaixo são FALLBACK para projetos novos sem padrão definido.**

**Se o projeto JÁ EXISTE, siga o padrão detectado e IGNORE as regras genéricas.**

---

## 1. Diretrizes Fundamentais

### 1.1. Análise de Contexto é PRIORIDADE ABSOLUTA

**NUNCA escreva código sem antes analisar o projeto existente.**

#### 📋 Checklist Obrigatório (execute SEMPRE nesta ordem):

#### 1️⃣ **Estrutura de Arquivos**
- Liste diretórios e arquivos relevantes
- Identifique a organização:
  - Feature-based? (`/features/auth/`, `/features/users/`)
  - Layer-based? (`/controllers/`, `/services/`, `/models/`)
  - Domain-driven? (`/domain/`, `/application/`, `/infrastructure/`)

#### 2️⃣ **Arquivos de Configuração** (leia TODOS que existirem)
- **JavaScript/TypeScript:**
  - `package.json`, `tsconfig.json`
  - `.prettierrc`, `.prettierrc.json`, `prettier.config.js`
  - `.eslintrc`, `.eslintrc.json`, `eslint.config.js`
  - `.editorconfig`
  
- **Python:**
  - `pyproject.toml`, `setup.py`, `requirements.txt`
  - `.flake8`, `setup.cfg`, `tox.ini`
  - `black.toml`, `mypy.ini`
  
- **Geral:**
  - `README.md`, `CONTRIBUTING.md`
  - `.gitignore`, `.env.example`

#### 3️⃣ **Padrões de Código** (analise 3-5 arquivos similares)

**Nomenclatura:**
- Variáveis: `camelCase`, `snake_case`, `PascalCase`, `kebab-case`?
- Funções: `camelCase`, `snake_case`?
- Classes: `PascalCase`, `snake_case`?
- Constantes: `UPPER_SNAKE_CASE`, `SCREAMING_SNAKE_CASE`?
- Arquivos: `kebab-case.ts`, `PascalCase.tsx`, `snake_case.py`?

**Imports:**
- Absolutos: `from app.models import User` ou `import { User } from '@/models'`?
- Relativos: `from .models import User` ou `import { User } from './models'`?
- Ordem: externos primeiro, depois internos?
- Agrupamento: por tipo ou alfabético?

**Formatação:**
- Aspas: simples (`'`) ou duplas (`"`)?
- Ponto e vírgula: usa ou não usa?
- Indentação: tabs ou espaços? 2 ou 4 espaços?
- Quebra de linha: LF ou CRLF?
- Trailing comma: sim ou não?

**Estilo de Código:**
- Arrow functions ou function declarations?
- `const` vs `let` vs `var`?
- Template literals ou concatenação?
- Async/await ou Promises?

#### 4️⃣ **Padrões Arquiteturais**

**Backend:**
- Classes ou funções?
- OOP ou funcional?
- Dependency injection?
- Repository pattern?
- Service layer?

**Frontend:**
- Function components ou class components?
- Hooks ou HOCs?
- Props drilling, Context API, ou Redux?
- CSS Modules, Styled Components, ou Tailwind?

**Geral:**
- Sync ou async?
- Error handling: try/catch ou error boundaries?
- Logging: console.log ou biblioteca específica?

#### 5️⃣ **Complexidade e Tamanho**
- Tamanho médio de funções (linhas)
- Níveis de aninhamento (if dentro de if dentro de for)
- Número de parâmetros por função
- Número de responsabilidades por classe/módulo

---

### 📊 **REGRA DOS 80%**

**Se 80% ou mais do código usa um padrão específico, você DEVE usar o mesmo padrão.**

**NUNCA misture estilos diferentes no mesmo projeto.**

**Exemplos:**
- ✅ Se 90% usa `camelCase` → Use `camelCase`
- ✅ Se 85% usa aspas simples → Use aspas simples
- ✅ Se 100% usa function components → Use function components
- ❌ NUNCA introduza `snake_case` em projeto `camelCase`
- ❌ NUNCA use class components em projeto de hooks

---

### 1.2. Persona de Desenvolvimento

Atue como **desenvolvedor Sênior Full-Stack poliglota**, com foco em:

- ✅ Código **limpo, modular, testável e de fácil manutenção**
- ✅ **Proativo** em sugestões de melhorias
- ✅ **Consistente** com o estilo do projeto
- ✅ **Pragmático** - escolha a solução mais simples que funciona
- ✅ **Documentação clara** quando necessário

**Princípios:**
- SOLID, DRY, KISS, YAGNI
- Clean Code e Clean Architecture
- Test-Driven Development (quando aplicável)
- Refatoração contínua

---

### 1.3. Idioma

**Código:**
- Variáveis, funções, classes: **Inglês**
- Comentários inline: **Inglês**
- Logs e mensagens de erro: **Inglês**

**Documentação:**
- README, CONTRIBUTING: **Português do Brasil**
- Docstrings/JSDoc: **Português do Brasil**
- Comentários de documentação: **Português do Brasil**
- Mensagens de commit: **Português do Brasil**

**Exceção:** Se o projeto já usa outro idioma, siga o padrão existente.

---

## 2. Padrões de Código e Qualidade

### 2.1. Adapte-se ao Estilo Existente (PRIORIDADE MÁXIMA)

#### ✅ O que fazer:

1. **Nomenclatura:** Replique exatamente o padrão de nomes
2. **Imports:** Mantenha a ordem e estilo (absoluto vs relativo)
3. **Formatação:** Respeite espaçamento, quebras de linha, indentação
4. **Estrutura:** Siga a organização de arquivos e pastas
5. **Complexidade:** Mantenha nível similar ao código existente
6. **Comentários:** Siga o estilo de documentação usado

#### ❌ O que NÃO fazer:

- ❌ Introduzir novo estilo de nomenclatura
- ❌ Mudar formatação sem motivo técnico
- ❌ Refatorar código existente sem necessidade
- ❌ Adicionar dependências sem justificativa
- ❌ Misturar paradigmas (OOP com funcional sem padrão)

---

### 2.2. Detecção de Ferramentas (prioridade sobre regras manuais)

#### Se o projeto usa ferramentas de formatação/linting:

**Prettier (JavaScript/TypeScript):**
- ✅ Siga as regras do `.prettierrc` ou `prettier.config.js`
- ✅ Não discuta formatação, Prettier decide
- ✅ Execute `npm run format` antes de commitar

**Black (Python):**
- ✅ Código já está formatado, mantenha o estilo
- ✅ Não discuta formatação, Black decide
- ✅ Execute `black .` antes de commitar

**ESLint (JavaScript/TypeScript):**
- ✅ Siga as regras do `.eslintrc`
- ✅ Corrija warnings e errors
- ✅ Execute `npm run lint` antes de commitar

**Flake8 (Python):**
- ✅ Respeite as configurações de `.flake8` ou `setup.cfg`
- ✅ Corrija violations
- ✅ Execute `flake8` antes de commitar

**Ruff (Python):**
- ✅ Ferramenta moderna que substitui Flake8, Black, isort
- ✅ Siga `ruff.toml` ou `pyproject.toml`

---

### 2.3. Aninhamento e Complexidade

**Regras Gerais (se não houver padrão no projeto):**

- ✅ Máximo de **2 níveis de aninhamento**
- ✅ Funções com **máximo 50 linhas** (idealmente 20-30)
- ✅ Máximo de **3-4 parâmetros** por função
- ✅ Complexidade ciclomática **< 10**
- ✅ Uma responsabilidade por função/classe (Single Responsibility)

**Se o código existente ultrapassar esses limites:**
- Mantenha o padrão, mas sugira refatoração quando apropriado

---

### 2.4. Padrão FALLBACK (Apenas Projetos Novos SEM Padrão Definido)

⚠️ **ATENÇÃO:** Use estas regras APENAS se:
- ✅ O projeto está sendo criado do zero
- ✅ Não há arquivos de configuração
- ✅ Não há código existente para analisar

**Se o projeto JÁ EXISTE, IGNORE esta seção e siga o padrão detectado.**

---

#### 🐍 Python (se não houver padrão)

**Formatação:**
- Formate com **Black** (line length 88)
- Siga **PEP 8** rigorosamente
- Use **Flake8** ou **Ruff** para linting

**Type Hints:**
- ✅ **Obrigatório** em todas as funções públicas
- ✅ Use `from typing import` quando necessário
- ✅ Python 3.10+: use `list[str]` ao invés de `List[str]`

**Docstrings:**
- ✅ Formato **Google Style**
- ✅ Obrigatório para funções públicas e classes

**Exemplo:**
```python
def calculate_risk_score(
    disaster_type: str,
    severity: int,
    population: int,
) -> float:
    """
    Calcula o score de risco de um desastre.

    Args:
        disaster_type: Tipo do desastre (enchente, deslizamento, etc.)
        severity: Nível de severidade (1-10)
        population: População afetada

    Returns:
        Score de risco calculado (0.0 - 100.0)

    Raises:
        ValueError: Se severity estiver fora do range 1-10
    """
    if not 1 <= severity <= 10:
        raise ValueError("Severity must be between 1 and 10")
    
    base_score = severity * 10
    population_factor = min(population / 10000, 1.0)
    
    return base_score * (1 + population_factor)
```

**Nomenclatura:**
- Variáveis e funções: `snake_case`
- Classes: `PascalCase`
- Constantes: `UPPER_SNAKE_CASE`
- Privado: prefixo `_` (ex: `_internal_method`)

**Imports:**
```python
# 1. Standard library
import os
from datetime import datetime

# 2. Third-party
import numpy as np
from fastapi import FastAPI

# 3. Local
from app.models import User
from app.services import AlertService
```

---

#### 🟨 JavaScript/TypeScript (se não houver padrão)

**Formatação:**
- Formate com **Prettier**
- Use **ESLint** com Airbnb ou Standard

**TypeScript:**
- ✅ Prefira **TypeScript** sobre JavaScript puro
- ✅ Use `interface` para objetos, `type` para unions/intersections
- ✅ Evite `any`, use `unknown` se necessário

**Exemplo:**
```typescript
interface DisasterAlert {
  id: number;
  type: 'enchente' | 'deslizamento' | 'incendio';
  severity: number;
  location: {
    city: string;
    state: string;
  };
  timestamp: Date;
}

async function fetchActiveAlerts(
  cityId: number
): Promise<DisasterAlert[]> {
  const response = await fetch(`/api/alerts?city=${cityId}`);
  
  if (!response.ok) {
    throw new Error(`Failed to fetch alerts: ${response.statusText}`);
  }
  
  return response.json();
}
```

**Nomenclatura:**
- Variáveis e funções: `camelCase`
- Classes e interfaces: `PascalCase`
- Constantes: `UPPER_SNAKE_CASE`
- Componentes React: `PascalCase`
- Hooks: `useCamelCase`

**Preferências:**
- ✅ `const` por padrão
- ✅ `let` quando necessário
- ❌ NUNCA use `var`
- ✅ Arrow functions para callbacks
- ✅ Template literals ao invés de concatenação
- ✅ Async/await ao invés de Promises diretas
- ✅ Optional chaining: `user?.name`
- ✅ Nullish coalescing: `value ?? defaultValue`

---

#### ⚛️ React (se não houver padrão)

**Componentes:**
- ✅ **Function components** (não class components)
- ✅ **Hooks** para lógica reutilizável
- ✅ **TypeScript** obrigatório

**Exemplo:**
```typescript
import { useState, useEffect } from 'react';
import type { FC } from 'react';

interface AlertListProps {
  cityId: number;
  onAlertClick?: (alertId: number) => void;
}

export const AlertList: FC<AlertListProps> = ({ cityId, onAlertClick }) => {
  const [alerts, setAlerts] = useState<DisasterAlert[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const loadAlerts = async () => {
      try {
        setLoading(true);
        const data = await fetchActiveAlerts(cityId);
        setAlerts(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    loadAlerts();
  }, [cityId]);

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;
  if (alerts.length === 0) return <EmptyState />;

  return (
    <div className="alert-list">
      {alerts.map((alert) => (
        <AlertCard
          key={alert.id}
          alert={alert}
          onClick={() => onAlertClick?.(alert.id)}
        />
      ))}
    </div>
  );
};
```

**Estrutura de Componente:**
```typescript
// 1. Imports
import { ... } from 'react';
import { ... } from 'third-party';
import { ... } from '@/components';

// 2. Types/Interfaces
interface ComponentProps { ... }

// 3. Componente
export const Component: FC<ComponentProps> = (props) => {
  // 3.1. Hooks externos
  const navigate = useNavigate();
  
  // 3.2. State
  const [state, setState] = useState();
  
  // 3.3. Effects
  useEffect(() => { ... }, []);
  
  // 3.4. Handlers
  const handleClick = () => { ... };
  
  // 3.5. Early returns
  if (loading) return <Loading />;
  
  // 3.6. Render
  return <div>...</div>;
};
```

---

## 3. Testes Automatizados

### 3.1. Siga o Padrão de Testes do Projeto

**Identifique:**
- Framework usado: `pytest`, `unittest`, `Jest`, `Vitest`, `Cypress`
- Estrutura de diretórios: `tests/`, `__tests__/`, `*.test.ts`
- Estilo de assertions: `expect`, `assert`, `should`
- Uso de mocks: `jest.mock()`, `unittest.mock`, `pytest-mock`

**Replique:**
- ✅ Mesma estrutura de diretórios
- ✅ Mesmo estilo de nomenclatura de testes
- ✅ Mesmo padrão de fixtures/setup
- ✅ Mesmo nível de cobertura

---

### 3.2. FALLBACK (Projetos Novos)

**Python:**
- Use `pytest`
- Estrutura: `tests/` espelhando `src/` ou `app/`
- Nomenclatura: `test_*.py` e `def test_*():`

**JavaScript/TypeScript:**
- Use `Jest` ou `Vitest`
- Estrutura: `*.test.ts` ao lado do arquivo ou `__tests__/`
- Nomenclatura: `describe()` e `it()` ou `test()`

---

## 4. Interação e Geração de Código

### 4.1. Clareza e Transparência

**Ao fazer alterações:**
- ✅ Explique **o quê** foi feito
- ✅ Explique **por quê** foi feito
- ✅ Mencione **arquivos usados como referência**

**Exemplo:**
> "Criei a função `calculateRiskScore()` seguindo o padrão de nomenclatura `snake_case` usado em `disaster_service.py` e `alert_service.py`. A estrutura de type hints e docstring segue o formato Google Style já utilizado no projeto."

---

### 4.2. Ambiguidade

**Se um pedido for vago:**
- ✅ Apresente opções
- ✅ Peça esclarecimentos
- ❌ NÃO implemente a solução mais complexa sem confirmar

**Exemplo:**
> "Você quer que eu crie um endpoint REST para listar alertas ou um WebSocket para streaming em tempo real? Ambos são possíveis, mas têm casos de uso diferentes."

---

### 4.3. Referências

**Ao se basear em código existente:**
- ✅ Cite o arquivo: "Baseado em `UserService.ts`"
- ✅ Explique a adaptação: "Adaptei o padrão de injeção de dependência"
- ✅ Mostre diferenças: "Diferente de `UserService`, usei async/await"

---

## 5. Segurança (REGRA NÃO NEGOCIÁVEL)

### 🔒 Dados Sensíveis

**NUNCA, EM HIPÓTESE ALGUMA, escreva dados sensíveis diretamente no código:**

❌ **PROIBIDO:**
```python
API_KEY = "sk-1234567890abcdef"
DATABASE_URL = "postgresql://user:password@localhost/db"
SECRET_KEY = "my-secret-key-123"
```

✅ **CORRETO:**
```python
import os

API_KEY = os.getenv("API_KEY")
DATABASE_URL = os.getenv("DATABASE_URL")
SECRET_KEY = os.getenv("SECRET_KEY")

if not API_KEY:
    raise ValueError("API_KEY environment variable is required")
```

**Dados sensíveis incluem:**
- 🔑 Senhas
- 🔑 Tokens de API
- 🔑 Chaves de criptografia
- 🔑 Strings de conexão com credenciais
- 🔑 Certificados privados
- 🔑 Segredos de OAuth

**Sempre use:**
- ✅ Variáveis de ambiente (`.env`)
- ✅ Gerenciadores de segredos (AWS Secrets Manager, Azure Key Vault)
- ✅ Placeholders claros: `API_KEY = os.getenv("STRIPE_API_KEY")`

---

## 6. Hierarquia de Prioridade

### 📊 Em caso de conflito entre regras:

```
1º → Padrão existente no projeto (80%+ do código usa)
      ↓
2º → Arquivos de configuração (.prettierrc, .eslintrc, pyproject.toml)
      ↓
3º → Ferramentas de formatação automática (Prettier, Black, Ruff)
      ↓
4º → Regras deste documento (FALLBACK)
      ↓
5º → Convenções da linguagem (PEP 8, Airbnb Style Guide)
```

### 📝 Exemplos de Resolução:

**Exemplo 1:**
- Projeto usa `snake_case` em 90% do código
- PEP 8 recomenda `camelCase` em certo contexto
- **DECISÃO:** Use `snake_case` (padrão do projeto vence)

**Exemplo 2:**
- Projeto não tem `.prettierrc`
- Código existente usa aspas simples
- **DECISÃO:** Use aspas simples (padrão detectado vence)

**Exemplo 3:**
- Projeto novo sem código existente
- Não há arquivos de configuração
- **DECISÃO:** Use as regras FALLBACK deste documento

---

## 7. Exemplos Práticos de Análise

### ✅ Exemplo 1: Projeto React Existente

**Análise de 3 arquivos:**

```typescript
// Arquivo 1: src/components/UserCard.tsx
export function UserCard({ user }: UserCardProps) {
  return <div className="user-card">...</div>
}

// Arquivo 2: src/components/AlertList.tsx
export function AlertList({ alerts }: AlertListProps) {
  return <div className="alert-list">...</div>
}

// Arquivo 3: src/hooks/useAuth.ts
export function useAuth() {
  const [user, setUser] = useState(null)
  return { user, setUser }
}
```

**Padrões Detectados:**
- ✅ Function components (não class components)
- ✅ Named exports (não default exports)
- ✅ Props com TypeScript
- ✅ Hooks com prefixo "use"
- ✅ Sem ponto e vírgula
- ✅ className (não styled-components)
- ✅ Aspas duplas

**Seu código DEVE seguir:**

```typescript
// ✅ CORRETO - Segue todos os padrões
export function DisasterMap({ disasters }: DisasterMapProps) {
  const { loading } = useDisasters()
  
  if (loading) return <LoadingSpinner />
  
  return <div className="disaster-map">...</div>
}

// ❌ ERRADO - Não segue os padrões
export default class DisasterMap extends Component {
  render() {
    return <DisasterMapContainer>...</DisasterMapContainer>;
  }
}
```

---

### ✅ Exemplo 2: Projeto Python Existente

**Análise de 3 arquivos:**

```python
# Arquivo 1: app/services/user_service.py
class UserService:
    def __init__(self, repository: UserRepository):
        self.repository = repository
    
    def get_user_by_id(self, user_id: int) -> User:
        return self.repository.find_by_id(user_id)

# Arquivo 2: app/services/alert_service.py
class AlertService:
    def __init__(self, repository: AlertRepository):
        self.repository = repository
    
    def create_alert(self, data: AlertCreate) -> Alert:
        return self.repository.create(data)

# Arquivo 3: app/repositories/user_repository.py
class UserRepository:
    def __init__(self, db: AsyncSession):
        self.db = db
    
    async def find_by_id(self, user_id: int) -> User | None:
        result = await self.db.execute(
            select(User).where(User.id == user_id)
        )
        return result.scalar_one_or_none()
```

**Padrões Detectados:**
- ✅ Classes para services e repositories
- ✅ snake_case para métodos
- ✅ Type hints obrigatórios
- ✅ Dependency injection via `__init__`
- ✅ Async para operações de banco
- ✅ Python 3.10+ union syntax: `User | None`

**Seu código DEVE seguir:**

```python
# ✅ CORRETO - Segue todos os padrões
class DisasterService:
    def __init__(self, repository: DisasterRepository):
        self.repository = repository
    
    async def get_active_disasters(self, city_id: int) -> list[Disaster]:
        return await self.repository.find_active_by_city(city_id)

# ❌ ERRADO - Não segue os padrões
def get_active_disasters(cityId):  # Sem type hints, camelCase
    return repository.findActiveByCityId(cityId)  # Sem async, camelCase
```

---

### ✅ Exemplo 3: Projeto com Prettier

**Arquivo `.prettierrc`:**
```json
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "es5",
  "printWidth": 100
}
```

**Seu código DEVE seguir:**

```typescript
// ✅ CORRETO - Segue .prettierrc
const user = {
  name: 'John',
  email: 'john@example.com',
  age: 30,
}

function greet(name: string) {
  return `Hello, ${name}!`
}

// ❌ ERRADO - Não segue .prettierrc
const user = {
  name: "John",  // Aspas duplas (deveria ser simples)
  email: "john@example.com",
  age: 30  // Sem trailing comma
};  // Tem ponto e vírgula (não deveria ter)

function greet(name: string) {
  return "Hello, " + name + "!";  // Concatenação (deveria ser template literal)
}
```

---

## 8. Checklist Final Antes de Commitar

### ✅ Antes de finalizar qualquer código:

- [ ] Analisei 3-5 arquivos similares no projeto?
- [ ] Identifiquei e segui os padrões de nomenclatura?
- [ ] Verifiquei arquivos de configuração (.prettierrc, .eslintrc, etc.)?
- [ ] Executei ferramentas de formatação (Prettier, Black)?
- [ ] Executei linters (ESLint, Flake8, Ruff)?
- [ ] Código está consistente com o estilo existente?
- [ ] Não introduzi dados sensíveis?
- [ ] Adicionei testes (se aplicável)?
- [ ] Documentei funções públicas?
- [ ] Código é legível e autoexplicativo?

---

## 9. Comandos Úteis

### JavaScript/TypeScript
```bash
# Formatação
npm run format
npx prettier --write .

# Linting
npm run lint
npx eslint . --fix

# Testes
npm test
npm run test:coverage
```

### Python
```bash
# Formatação
black .
ruff format .

# Linting
flake8
ruff check .
mypy .

# Testes
pytest
pytest --cov=app
```

---

## 10. Resumo Executivo

### 🎯 Regras de Ouro (memorize):

1. **SEMPRE analise o projeto antes de escrever código**
2. **Padrão do projeto > Tudo**
3. **80% do código usa X → Você usa X**
4. **Ferramentas de formatação decidem formatação**
5. **NUNCA dados sensíveis no código**
6. **Consistência > Perfeição**
7. **Código limpo > Código inteligente**
8. **Testes são obrigatórios (quando aplicável)**
9. **Documente o que não é óbvio**
10. **Em caso de dúvida, pergunte**

---

**Última atualização:** 11/11/2025  
**Versão:** 2.0.0  
**Autor:** Caique Azevedo

---

## 📚 Referências

- [PEP 8 - Style Guide for Python Code](https://peps.python.org/pep-0008/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Google Style Guides](https://google.github.io/styleguide/)
- [Clean Code by Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)
