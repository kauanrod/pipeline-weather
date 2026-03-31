<div align="center">

# 🌤️ Pipeline Weather - ETL para Dados Meteorológicos
# 🌤️ Pipeline Weather - ETL for Meteorological Data

[![Python](https://img.shields.io/badge/Python-3.14-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Apache Airflow](https://img.shields.io/badge/Apache%20Airflow-3.1.7-017CEE?logo=apache-airflow&logoColor=white)](https://airflow.apache.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Latest-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

Um pipeline ETL robusto e escalável para extração, transformação e carregamento de dados meteorológicos de São Paulo em tempo real.

A robust and scalable ETL pipeline for extraction, transformation and loading of real-time meteorological data from São Paulo.

[🇧🇷 Português](#portuguese) • [🇺🇸 English](#english)

</div>

---

<a name="portuguese"></a>

# 🇧🇷 Documentação em Português

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias](#tecnologias)
- [Arquitetura](#arquitetura)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Como Usar](#como-usar)
- [Pipeline ETL](#pipeline-etl)
- [Acessando as Interfaces](#acessando-as-interfaces)
- [Troubleshooting](#troubleshooting)
- [Próximos Passos](#próximos-passos)
- [Contribuições](#contribuições)

## 🎯 Sobre o Projeto

**Pipeline Weather** é uma solução completa de data engineering que automatiza a coleta e processamento de dados meteorológicos. O projeto foi desenvolvido para demonstrar boas práticas em:

- **Orquestração de Workflows**: Uso de Apache Airflow para agendamento automático
- **Arquitetura ETL**: Separação clara de Extract, Transform e Load
- **Containerização**: Deployment com Docker e Docker Compose
- **Processamento de Dados**: Transformações robustas com Pandas
- **Armazenamento**: Persistência em banco de dados PostgreSQL
- **Integração de APIs**: Consumo de dados externos em tempo real

### Caso de Uso

O pipeline coleta dados meteorológicos da cidade de São Paulo, Brasil a cada hora, realiza transformações estruturadas nos dados e os armazena em um banco de dados PostgreSQL para análises posteriores e geração de insights. Este é um exemplo perfeito de um sistema de produção em data engineering.

## 🛠️ Tecnologias

| Tecnologia | Versão | Propósito |
|-----------|--------|----------|
| **Apache Airflow** | 3.1.7 | Orquestração e agendamento de workflows |
| **Python** | 3.14 | Linguagem principal de desenvolvimento |
| **PostgreSQL** | 16 | Banco de dados relacional |
| **Redis** | 7.2-bookworm | Message broker para Celery (CeleryExecutor) |
| **Pandas** | 3.0.1+ | Processamento e transformação de dados |
| **SQLAlchemy** | 2.0.48+ | ORM para acesso ao banco de dados |
| **Requests** | 2.33.1+ | Cliente HTTP para consumir APIs |
| **python-dotenv** | 1.2.2+ | Gerenciamento de variáveis de ambiente |
| **psycopg2-binary** | 2.9.11+ | Driver PostgreSQL para Python |
| **Docker** | Latest | Containerização de aplicações |
| **Docker Compose** | Latest | Orquestração de containers |

### API Externa

- **OpenWeatherMap API**: Fornece os dados meteorológicos em tempo real (https://openweathermap.org/api)

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────────────────────┐
│                     AIRFLOW ORCHESTRATOR                │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   Scheduler  │  │   API Server │  │    Worker    │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│                          ↕                       ↕       │
└────────────┬────────────────────────────────────┬────────┘
             │                                    │
    ┌────────▼─────────────┐           ┌────────▼──────┐
    │   OpenWeatherMap API │           │  PostgreSQL   │
    │   (Dados Externos)   │           │  (Banco Dados)│
    └──────────────────────┘           └─────────────��─┘
             ▲                                    ▲
             │                                    │
    ┌────────┴───────────────────────────���────────┴────────┐
    │         PIPELINE ETL (Extract → Transform → Load)    │
    │                                                       │
    │  ┌──────────────┐  ┌──────────────┐  ┌────────────┐ │
    │  │   EXTRACT    │ → │  TRANSFORM   │ → │   LOAD    │ │
    │  │ weather_data │  │ + Normalize  │  │ weather_db│ │
    │  └──────────────┘  └──────────────┘  └────────────┘ │
    └───────────────────────────────────────────────────────┘
```

## 📋 Pré-requisitos

Antes de iniciar, certifique-se de ter instalado:

- **Docker** (versão 20.10+): [Instalar Docker](https://docs.docker.com/get-docker/)
- **Docker Compose** (versão 1.29+): Geralmente incluído com Docker Desktop
- **Git**: Para clonar o repositório
- **Espaço em disco**: Mínimo 10GB disponível
- **RAM**: Mínimo 4GB (recomendado 8GB)
- **CPUs**: Mínimo 2 cores (recomendado 4)

### Verificar Instalações

```bash
# Verificar Docker
docker --version

# Verificar Docker Compose
docker-compose --version

# Verificar Git
git --version
```

## 🚀 Instalação

### 1. Clonar o Repositório

```bash
git clone https://github.com/kauanrod/pipeline-weather.git
cd pipeline-weather
```

### 2. Configurar Variáveis de Ambiente

Crie um arquivo `.env` na pasta `config/`:

```bash
# config/.env
API_KEY='sua_chave_openweathermap'
database='weather_data'
user='seu_usuario_postgres'
password='sua_senha_postgres'
```

> **Nota Importante**: Uma chave de API de exemplo está incluída, mas é **fortemente recomendado** usar sua própria chave do [OpenWeatherMap](https://openweathermap.org/api). Crie uma conta gratuita para obter sua chave.

### 3. Iniciar os Containers

```bash
# Iniciar todos os serviços em background
docker-compose up -d

# Verificar status dos containers
docker-compose ps

# Ver logs em tempo real (Ctrl+C para parar)
docker-compose logs -f
```

**Esperados para ver**:
```
postgres is starting up
redis is healthy
airflow-init is starting...
airflow-scheduler is healthy
airflow-apiserver is healthy
airflow-worker is healthy
```

### 4. Aguardar Inicialização Completa

Aguarde cerca de 2-3 minutos para o Airflow se inicializar completamente:

```bash
# Monitorar status
docker-compose logs airflow-init

# Quando ver "All done", o Airflow está pronto
```

## ⚙️ Configuração

### Variáveis de Ambiente

Arquivo: `config/.env`

| Variável | Descrição | Exemplo | Obrigatória |
|----------|-----------|---------|-----------|
| `API_KEY` | Chave de API do OpenWeatherMap | `540cfeff789e4c...` | ✅ Sim |
| `database` | Nome do banco de dados PostgreSQL | `weather_data` | ✅ Sim |
| `user` | Usuário PostgreSQL | `zero` | ✅ Sim |
| `password` | Senha PostgreSQL | `123` | ✅ Sim |

### Obter Chave de API OpenWeatherMap

1. Acesse [OpenWeatherMap](https://openweathermap.org/api)
2. Clique em "Sign Up" e crie uma conta gratuita
3. Vá para "API Keys" no menu
4. Copie sua chave padrão
5. Atualize `config/.env` com sua chave
6. Reinicie: `docker-compose restart`

### Modificar Frequência de Execução

Para alterar a frequência de execução do pipeline, edite `dags/weather_dag.py`:

```python
@dag(
    dag_id='weather_pipeline',
    # ... outras configurações ...
    schedule='0 */1 * * *',  # ← Modifique esta linha
)
```

**Exemplos de Schedule (Cron)**:

| Schedule | Frequência |
|----------|-----------|
| `0 */1 * * *` | A cada hora |
| `0 0 * * *` | Diariamente à meia-noite |
| `0 0 * * 0` | Semanalmente (domingo) |
| `0 0 1 * *` | Mensalmente (1º dia) |
| `*/5 * * * *` | A cada 5 minutos |

Após editar, reinicie o scheduler:
```bash
docker-compose restart airflow-scheduler
```

## 📁 Estrutura do Projeto

```
pipeline-weather/
│
├── README.md                  # Este arquivo (bilíngue)
├── main.py                    # Script principal
├── pyproject.toml             # Dependências do projeto (uv/pip)
├── docker-compose.yaml        # Orquestração Docker Compose
├── .python-version            # Versão Python (3.14)
├── uv.lock                    # Lock file de dependências
├── .gitignore                 # Arquivos ignorados pelo Git
│
├── config/
│   └── .env                   # Variáveis de ambiente (⚠️ NÃO COMITAR)
│
├── dags/
│   └── weather_dag.py         # DAG do Airflow - Orquestra o pipeline
│
├── src/
│   ├── extract_data.py        # EXTRACT: Busca dados da API
│   ├── transform_data.py      # TRANSFORM: Normaliza e limpa dados
│   └── load_data.py           # LOAD: Insere dados no PostgreSQL
│
├── data/
│   ├── weather_data.json      # Dados brutos (gerado automaticamente)
│   └── temp_data.parquet      # Dados transformados (gerado automaticamente)
│
└── notebooks/                 # Jupyter notebooks (opcional para análises)
```

### Detalhamento dos Módulos

#### 📥 `src/extract_data.py` - Extração de Dados

Responsável por consumir a API OpenWeatherMap e salvar os dados brutos em JSON.

**Funcionalidades**:
- ✅ Requisição HTTP à API OpenWeatherMap
- ✅ Tratamento de erros (status code)
- ✅ Logging detalhado de cada passo
- ✅ Criação automática de diretórios

**Fluxo**:
```
URL da API → HTTP Request → Validação (status 200) → JSON File
                                ↓
                        logs/weather_data.json
```

**Entrada**: `url` (string com endpoint da API)  
**Saída**: `data/weather_data.json` (arquivo JSON)

#### 🔄 `src/transform_data.py` - Transformação de Dados

Realiza transformações estruturadas nos dados brutos, preparando-os para armazenamento.

**Funcionalidades**:
- ✅ Normalização de estruturas JSON aninhadas
- ✅ Renomeação de colunas para melhor semântica
- ✅ Conversão de timestamps Unix para datetime
- ✅ Conversão de timezone para America/Sao_Paulo
- ✅ Limpeza de colunas desnecessárias
- ✅ Validação de tipos de dados

**Exemplo de Transformação**:

```
ANTES (JSON aninhado):
{
  "coord": {"lon": -46.6333, "lat": -23.5505},
  "main": {"temp": 25.5, "humidity": 60},
  "dt": 1711900800,
  "sys": {"sunrise": 1711881600, "sunset": 1711927200}
}

DEPOIS (Normalizado):
latitude  | longitude  | temperature | humidity | datetime
-23.5505  | -46.6333   | 25.5        | 60       | 2026-03-31 12:00:00-03:00
```

**Entrada**: `data/weather_data.json`  
**Saída**: DataFrame estruturado + `data/temp_data.parquet`

#### 📤 `src/load_data.py` - Carregamento de Dados

Carrega os dados transformados no banco de dados PostgreSQL.

**Funcionalidades**:
- ✅ Conexão segura com PostgreSQL via SQLAlchemy
- ✅ Inserção de dados (append mode - não sobrescreve)
- ✅ Validação pós-carregamento
- ✅ Logging de quantidade de registros inseridos

**Fluxo**:
```
DataFrame → SQLAlchemy Engine → PostgreSQL
                                     ↓
                            Tabela: weather_data
```

**Entrada**: DataFrame transformado  
**Saída**: Tabela `weather_data` no PostgreSQL com dados persistidos

#### 🎯 `dags/weather_dag.py` - Orquestração

Define a DAG (Directed Acyclic Graph) do Apache Airflow que orquestra todo o pipeline.

**Tarefas (Tasks)**:

```
Task 1: extract()
├─ Chama: extract_weather_data(url)
├─ Saída: data/weather_data.json
└─ Status: ✅ Sucesso → próxima task

Task 2: transform()
├─ Chama: data_transformations()
├─ Salva: data/temp_data.parquet
└─ Status: ✅ Sucesso → próxima task

Task 3: load()
├─ Lê: data/temp_data.parquet
├─ Chama: load_weather_data('weather_data', df)
└─ Status: ✅ Sucesso → Pipeline completo!
```

**Dependências**:
```
extract() → transform() → load()
```

**Configuração da DAG**:
- **Owner**: airflow
- **Retries**: 2 (tenta novamente 2 vezes em caso de falha)
- **Retry Delay**: 5 minutos
- **Schedule**: A cada hora (padrão) - configurável
- **Start Date**: 31/03/2026
- **Tags**: weather, etl, sao_paulo

## 📊 Como Usar

### Acessar Airflow Web Interface

1. Abra seu navegador e vá para: `http://localhost:8080`
2. Faça login com:
   - **Usuário**: `airflow`
   - **Senha**: `airflow`

### Ativar e Executar a DAG

#### Método 1: Via Web UI (Recomendado para iniciantes)

1. **Procure por "weather_pipeline"** na lista de DAGs
2. **Clique no toggle** (switch) para ativar
3. **Clique no botão ▶️ Play** para executar manualmente
4. **Selecione "Trigger DAG"** na janela que abrir
5. **Monitorar execução** clicando na DAG e vendo o status das tasks

#### Método 2: Via Linha de Comando

```bash
# Executar a DAG manualmente
docker-compose exec airflow-scheduler \
  airflow dags test weather_pipeline 2026-03-31

# Listar todas as DAGs registradas
docker-compose exec airflow-scheduler \
  airflow dags list

# Ver status das execuções
docker-compose exec airflow-scheduler \
  airflow dags list-runs --dag-id weather_pipeline

# Ver logs de uma task específica
docker-compose exec airflow-scheduler \
  airflow tasks logs weather_pipeline extract 2026-03-31
```

### Monitorar Execução

```bash
# Ver logs em tempo real do scheduler
docker-compose logs -f airflow-scheduler

# Ver logs do worker (execução das tasks)
docker-compose logs -f airflow-worker

# Filtrar logs específicos
docker-compose logs airflow-worker | grep extract
docker-compose logs airflow-worker | grep transform
docker-compose logs airflow-worker | grep load
```

## 🔄 Pipeline ETL - Fluxo Detalhado

```
┌────────────────────────────────────────────────────────────┐
│                    WEATHER PIPELINE                        │
│                    ===============                         │
│                                                            │
│  STEP 1️⃣  EXTRACT (Extração)                             │
│  ├─ 🔗 Requisita API OpenWeatherMap                       │
│  ├─ ✅ Valida HTTP Status (200 = sucesso)                │
│  ├─ 📥 Recebe JSON com dados meteorológicos              │
│  └─ 💾 Salva em: data/weather_data.json                  │
│                       ↓                                    │
│                  ⏳ ~5 segundos                            │
│                       ↓                                    │
│  STEP 2️⃣  TRANSFORM (Transformação)                      │
│  ├─ 📂 Lê arquivo JSON                                    │
│  ├─ 🔍 Normaliza estrutura aninhada (coord, main, wind)  │
│  ├─ 📊 Extrai dados de clima (weather array)             │
│  ├─ ✏️  Renomeia colunas (temp → temperature)            │
│  ├─ 🕐 Converte timestamps Unix → datetime com TZ        │
│  ├─ 🗑️  Remove colunas desnecessárias                    │
│  ├─ ✨ Valida tipos de dados                             │
│  └─ 📦 Salva em: data/temp_data.parquet                  │
│                       ↓                                    │
│                  ⏳ ~3 segundos                            │
│                       ↓                                    │
│  STEP 3️⃣  LOAD (Carregamento)                            │
│  ├─ 📥 Lê dados de Parquet                                │
│  ├─ 🔌 Conecta ao PostgreSQL (SQLAlchemy)                │
│  ├─ 📥 Insere em tabela weather_data (append mode)       │
│  ├─ ✅ Valida inserção (SELECT COUNT)                    │
│  └─ 📊 Log: "X registros inseridos"                      │
│                       ↓                                    │
│                  ⏳ ~2 segundos                            │
│                       ↓                                    │
│              ✅ PIPELINE CONCLUÍDO COM SUCESSO             │
│                                                            │
│  ⏱️  TEMPO TOTAL: ~10 segundos                            │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Exemplo de Dados

**Dados Brutos (JSON)**:
```json
{
  "coord": {"lon": -46.6333, "lat": -23.5505},
  "weather": [{
    "id": 802,
    "main": "Clouds",
    "description": "scattered clouds",
    "icon": "03d"
  }],
  "main": {
    "temp": 25.48,
    "feels_like": 24.67,
    "temp_min": 22.15,
    "temp_max": 27.12,
    "pressure": 1013,
    "humidity": 62,
    "sea_level": 1013,
    "grnd_level": 924
  },
  "visibility": 10000,
  "wind": {
    "speed": 3.5,
    "deg": 180,
    "gust": 5.2
  },
  "clouds": {"all": 60},
  "dt": 1711900800,
  "sys": {
    "sunrise": 1711881600,
    "sunset": 1711927200,
    "country": "BR"
  },
  "name": "São Paulo"
}
```

**Dados Transformados (Database)**:

| city_name | latitude | longitude | temperature | feels_like | humidity | wind_speed | weather_main | weather_description | datetime |
|-----------|----------|-----------|-------------|-----------|----------|-----------|------------|---------------------|----------|
| São Paulo | -23.5505 | -46.6333 | 25.48 | 24.67 | 62 | 3.5 | Clouds | scattered clouds | 2026-03-31 12:00:00-03:00 |

### Campos Armazenados no Banco de Dados

| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `city_name` | VARCHAR | Nome da cidade | São Paulo |
| `latitude` | FLOAT | Latitude em graus | -23.5505 |
| `longitude` | FLOAT | Longitude em graus | -46.6333 |
| `temperature` | FLOAT | Temperatura atual (°C) | 25.48 |
| `feels_like` | FLOAT | Sensação térmica (°C) | 24.67 |
| `temp_min` | FLOAT | Temperatura mínima (°C) | 22.15 |
| `temp_max` | FLOAT | Temperatura máxima (°C) | 27.12 |
| `humidity` | INTEGER | Umidade (%) | 62 |
| `pressure` | INTEGER | Pressão atmosférica (hPa) | 1013 |
| `sea_level` | INTEGER | Pressão ao nível do mar (hPa) | 1013 |
| `grnd_level` | INTEGER | Pressão ao nível do solo (hPa) | 924 |
| `visibility` | INTEGER | Visibilidade (metros) | 10000 |
| `wind_speed` | FLOAT | Velocidade do vento (m/s) | 3.5 |
| `wind_deg` | INTEGER | Direção do vento (graus) | 180 |
| `wind_gust` | FLOAT | Rajadas de vento (m/s) | 5.2 |
| `clouds` | INTEGER | Cobertura de nuvens (%) | 60 |
| `weather_id` | INTEGER | ID da condição climática | 802 |
| `weather_main` | VARCHAR | Condição principal | Clouds, Rain, Clear |
| `weather_description` | VARCHAR | Descrição detalhada | scattered clouds |
| `weather_icon` | VARCHAR | Ícone da condição | 03d |
| `country` | VARCHAR | Código do país | BR |
| `datetime` | TIMESTAMP | Data/hora (timezone: SP) | 2026-03-31 12:00:00-03:00 |
| `sunrise` | TIMESTAMP | Hora do nascer do sol | 2026-03-31 06:00:00-03:00 |
| `sunset` | TIMESTAMP | Hora do pôr do sol | 2026-03-31 18:00:00-03:00 |

## 🌐 Acessando as Interfaces

### 1. Apache Airflow Web UI

**URL**: `http://localhost:8080`

**Credenciais**:
- Usuário: `airflow`
- Senha: `airflow`

**O que você pode fazer**:
- ✅ Visualizar DAGs ativas
- ✅ Monitorar execuções em tempo real com gráficos
- ✅ Verificar logs detalhados de cada task
- ✅ Triggerar execuções manuais
- ✅ Definir alertas de falhas
- ✅ Visualizar dependências entre tasks (graph view)
- ✅ Agendar backfills de dados

### 2. Flower (Celery Monitor) - Opcional

Para ativar a interface de monitoramento do Celery:

```bash
docker-compose --profile flower up -d
```

**URL**: `http://localhost:5555`

**Funcionalidades**:
- 📊 Monitorar workers em tempo real
- 📈 Ver histórico de tasks
- ⚙️ Gerenciar workers e queues

### 3. PostgreSQL - Conexão Direta

#### Via psql (dentro do container)

```bash
# Entrar no container PostgreSQL
docker-compose exec postgres psql -U airflow -d airflow

# Listar tabelas
\dt

# Ver schema da tabela weather_data
\d weather_data

# Contar registros
SELECT COUNT(*) FROM weather_data;

# Últimos 5 registros
SELECT city_name, temperature, humidity, datetime 
FROM weather_data 
ORDER BY datetime DESC 
LIMIT 5;

# Sair
\q
```

#### Via Cliente SQL Gráfico

Conecte usando um cliente como **DBeaver**, **pgAdmin**, ou **VS Code Extension**:

| Configuração | Valor |
|-------------|-------|
| **Host** | `localhost` |
| **Port** | `5432` |
| **Database** | `airflow` |
| **User** | `airflow` |
| **Password** | `airflow` |

#### Consultas SQL Úteis

```sql
-- Contar total de registros
SELECT COUNT(*) as total_registros FROM weather_data;

-- Temperatura média por dia
SELECT 
  DATE(datetime) as data,
  ROUND(AVG(temperature), 2) as temp_media,
  MAX(temperature) as temp_max,
  MIN(temperature) as temp_min
FROM weather_data
GROUP BY DATE(datetime)
ORDER BY data DESC;

-- Condição climática mais frequente
SELECT 
  weather_main,
  COUNT(*) as frequencia,
  ROUND(100.0 * COUNT(*) / (SELECT COUNT(*) FROM weather_data), 2) as percentual
FROM weather_data
GROUP BY weather_main
ORDER BY frequencia DESC;

-- Umidade média por hora
SELECT 
  EXTRACT(HOUR FROM datetime) as hora,
  ROUND(AVG(humidity), 1) as umidade_media
FROM weather_data
GROUP BY EXTRACT(HOUR FROM datetime)
ORDER BY hora;

-- Últimas 24 horas de dados
SELECT 
  city_name,
  temperature,
  humidity,
  weather_description,
  datetime
FROM weather_data
WHERE datetime >= NOW() - INTERVAL '24 hours'
ORDER BY datetime DESC;
```

## 🐛 Troubleshooting

### ❌ Problema: Containers não iniciam

**Mensagem de erro**: `error response from daemon`

**Soluções**:

```bash
# 1. Ver logs completos de erro
docker-compose logs

# 2. Parar todos os containers
docker-compose down

# 3. Remover volumes (⚠️ apaga dados!)
docker-compose down -v

# 4. Reiniciar do zero
docker system prune -a  # Remove tudo não utilizado
docker-compose up -d

# 5. Se ainda não funcionar, verificar espaço em disco
df -h
```

### ❌ Problema: "ModuleNotFoundError: No module named 'src'"

**Mensagem de erro**: `Traceback ... ImportError: No module named src`

**Solução**: O path de imports está configurado em `dags/weather_dag.py` (linha 6):

```python
sys.path.insert(0, '/opt/airflow/src')
```

Verifique se os arquivos existem:

```bash
docker-compose exec airflow-scheduler ls -la /opt/airflow/src/
```

Saída esperada:
```
-rw-r--r-- extract_data.py
-rw-r--r-- transform_data.py
-rw-r--r-- load_data.py
```

### ❌ Problema: Erro de conexão com PostgreSQL

**Mensagem de erro**: `could not translate host name "postgres" to address`

**Solução**:

```bash
# 1. Verificar se PostgreSQL está rodando
docker-compose ps postgres

# 2. Testar conexão
docker-compose exec postgres \
  psql -U airflow -d airflow -c "SELECT 1"

# 3. Ver logs do PostgreSQL
docker-compose logs postgres

# 4. Reiniciar PostgreSQL
docker-compose restart postgres

# 5. Verificar variáveis de ambiente
docker-compose exec airflow-scheduler env | grep POSTGRES
```

### ❌ Problema: "API key invalid" (401 Unauthorized)

**Mensagem de erro**: 
```
Error fetching data: 401
Invalid API key
```

**Solução**:

1. Verifique se a chave está correta em `config/.env`
2. A chave levou menos de 10 minutos para ativar?
3. Gere uma nova chave em [OpenWeatherMap](https://openweathermap.org/api)
4. Atualize `config/.env`
5. Reinicie: `docker-compose restart`

```bash
# Testar chave manualmente
curl "https://api.openweathermap.org/data/2.5/weather?q=Sao Paulo,BR&units=metric&appid=YOUR_API_KEY"
```

### ❌ Problema: Disco cheio ou muita memória usada

**Sintomas**: Container com exit code 137, lentidão geral

**Soluções**:

```bash
# Ver uso de espaço
docker system df

# Limpar containers parados
docker container prune -f

# Limpar images não utilizadas
docker image prune -a -f

# Limpar volumes não utilizados
docker volume prune -f

# Ver logs de uso de memória
docker stats
```

### ❌ Problema: DAG não aparece em "Available DAGs"

**Solução**:

```bash
# 1. Verificar se o arquivo está no lugar certo
docker-compose exec airflow-scheduler ls -la /opt/airflow/dags/

# 2. Forçar parse da DAG
docker-compose exec airflow-scheduler airflow dags list

# 3. Reiniciar scheduler
docker-compose restart airflow-scheduler

# 4. Ver logs de parsing
docker-compose logs airflow-scheduler | grep "Parsing"
```

### ❌ Problema: Task falha aleatoriamente

**Solução**:

1. Verifique logs: `docker-compose logs airflow-worker | grep ERROR`
2. Aumente retries em `dags/weather_dag.py`:
   ```python
   default_args={
       'retries': 5,  # Aumentar de 2 para 5
       'retry_delay': timedelta(minutes=10),
   }
   ```
3. Reinicie: `docker-compose restart airflow-scheduler`

---

## 📈 Próximos Passos

### Melhorias Planejadas

- [ ] **Múltiplas Cidades**: Expandir para 10+ cidades brasileiras
- [ ] **Dashboard com Grafana**: Visualizações em tempo real
- [ ] **Alertas Inteligentes**: Notificações de eventos climáticos extremos (SMS/Email)
- [ ] **Data Quality**: Validações automáticas e testes de qualidade
- [ ] **Backup Automático**: Estratégia de backup incremental do PostgreSQL
- [ ] **Monitoramento**: Integração com Prometheus + AlertManager
- [ ] **Data Lineage**: Rastreamento completo de transformações (dbt ou Apache Atlas)
- [ ] **Machine Learning**: Previsão de temperatura com ARIMA/Prophet
- [ ] **Data Lake**: Armazenamento em S3 ou MinIO para histórico completo
- [ ] **Testes Automáticos**: CI/CD com GitHub Actions

### Ideias de Análises com SQL

```sql
-- Previsão de dias chuvosos
SELECT 
  DATE(datetime) as data,
  CASE 
    WHEN weather_main LIKE '%Rain%' THEN 'Chuvoso'
    WHEN weather_main LIKE '%Cloud%' THEN 'Nublado'
    ELSE 'Ensolarado'
  END as condicao
FROM weather_data
WHERE DATE(datetime) >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY DATE(datetime);

-- Variação de temperatura durante o dia
WITH hourly_temps AS (
  SELECT 
    EXTRACT(HOUR FROM datetime) as hora,
    ROUND(AVG(temperature), 2) as temp_media
  FROM weather_data
  WHERE DATE(datetime) = CURRENT_DATE
  GROUP BY EXTRACT(HOUR FROM datetime)
)
SELECT * FROM hourly_temps ORDER BY hora;
```

## 🤝 Contribuições

Contribuições são bem-vindas! Para contribuir:

1. **Fork** o repositório
   ```bash
   git clone https://github.com/seu-usuario/pipeline-weather.git
   ```

2. **Crie uma branch** para sua feature
   ```bash
   git checkout -b feature/MinhaFeature
   ```

3. **Faça commits** descritivos
   ```bash
   git commit -m 'Add: Nova funcionalidade X'
   ```

4. **Push** para a branch
   ```bash
   git push origin feature/MinhaFeature
   ```

5. **Abra um Pull Request** com descrição detalhada

### Padrões de Código

- 🐍 Python 3.14+
- 📏 Máximo 88 caracteres por linha
- 📝 Docstrings em inglês
- 🧪 Testes unitários para funções novas

---

<a name="english"></a>

# 🇺🇸 English Documentation

## 📋 Table of Contents

- [About the Project](#about-the-project)
- [Technologies](#technologies)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Project Structure](#project-structure)
- [How to Use](#how-to-use)
- [ETL Pipeline](#etl-pipeline)
- [Accessing Interfaces](#accessing-interfaces)
- [Troubleshooting](#troubleshooting)
- [Next Steps](#next-steps)
- [Contributing](#contributing)

## 🎯 About the Project

**Pipeline Weather** is a complete data engineering solution that automates the collection and processing of meteorological data. The project was developed to demonstrate best practices in:

- **Workflow Orchestration**: Using Apache Airflow for automatic scheduling
- **ETL Architecture**: Clear separation of Extract, Transform and Load
- **Containerization**: Deployment with Docker and Docker Compose
- **Data Processing**: Robust transformations with Pandas
- **Storage**: Persistence in PostgreSQL database
- **API Integration**: Real-time consumption of external data

### Use Case

The pipeline collects meteorological data from the city of São Paulo, Brazil every hour, performs structured transformations on the data, and stores it in a PostgreSQL database for later analysis and insight generation. This is a perfect example of a production data engineering system.

## 🛠️ Technologies

| Technology | Version | Purpose |
|-----------|---------|---------|
| **Apache Airflow** | 3.1.7 | Workflow orchestration and scheduling |
| **Python** | 3.14 | Primary development language |
| **PostgreSQL** | 16 | Relational database |
| **Redis** | 7.2-bookworm | Message broker for Celery (CeleryExecutor) |
| **Pandas** | 3.0.1+ | Data processing and transformation |
| **SQLAlchemy** | 2.0.48+ | ORM for database access |
| **Requests** | 2.33.1+ | HTTP client for API consumption |
| **python-dotenv** | 1.2.2+ | Environment variables management |
| **psycopg2-binary** | 2.9.11+ | PostgreSQL driver for Python |
| **Docker** | Latest | Application containerization |
| **Docker Compose** | Latest | Container orchestration |

### External API

- **OpenWeatherMap API**: Provides real-time meteorological data (https://openweathermap.org/api)

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────��──────┐
│                     AIRFLOW ORCHESTRATOR                │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   Scheduler  │  │   API Server │  │    Worker    │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│                          ↕                       ↕       │
└────────────┬────────────────────────────────────┬────────┘
             │                                    │
    ┌────────▼─────────────┐           ┌────────▼──────┐
    │   OpenWeatherMap API │           │  PostgreSQL   │
    │   (External Data)    │           │  (Database)   │
    └──────────────────────┘           └───────────────┘
             ▲                                    ▲
             │                                    │
    ┌────────┴────────────────────────────────────┴────────┐
    │         PIPELINE ETL (Extract → Transform → Load)    │
    │                                                       │
    │  ┌──────────────┐  ┌──────────────┐  ┌────────────┐ │
    │  │   EXTRACT    │ → │  TRANSFORM   │ → │   LOAD    │ │
    │  │ weather_data │  │ + Normalize  │  │ weather_db│ │
    │  └──────────────┘  └──────────────┘  └────────────┘ │
    └─────────────────────────────────────��─────────────────┘
```

## 📋 Prerequisites

Before starting, make sure you have installed:

- **Docker** (version 20.10+): [Install Docker](https://docs.docker.com/get-docker/)
- **Docker Compose** (version 1.29+): Usually included with Docker Desktop
- **Git**: To clone the repository
- **Disk Space**: Minimum 10GB available
- **RAM**: Minimum 4GB (recommended 8GB)
- **CPUs**: Minimum 2 cores (recommended 4)

### Verify Installations

```bash
# Check Docker
docker --version

# Check Docker Compose
docker-compose --version

# Check Git
git --version
```

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/kauanrod/pipeline-weather.git
cd pipeline-weather
```

### 2. Configure Environment Variables

Create a `.env` file in the `config/` folder:

```bash
# config/.env
API_KEY='your_openweathermap_api_key'
database='weather_data'
user='your_postgres_user'
password='your_postgres_password'
```

> **Important Note**: A sample API key is included, but it's **strongly recommended** to use your own key from [OpenWeatherMap](https://openweathermap.org/api). Create a free account to get your key.

### 3. Start the Containers

```bash
# Start all services in background
docker-compose up -d

# Check container status
docker-compose ps

# View logs in real-time (Ctrl+C to stop)
docker-compose logs -f
```

**Expected to see**:
```
postgres is starting up
redis is healthy
airflow-init is starting...
airflow-scheduler is healthy
airflow-apiserver is healthy
airflow-worker is healthy
```

### 4. Wait for Complete Initialization

Wait approximately 2-3 minutes for Airflow to fully initialize:

```bash
# Monitor status
docker-compose logs airflow-init

# When you see "All done", Airflow is ready
```

## ⚙️ Configuration

### Environment Variables

File: `config/.env`

| Variable | Description | Example | Required |
|----------|-------------|---------|----------|
| `API_KEY` | OpenWeatherMap API Key | `540cfeff789e4c...` | ✅ Yes |
| `database` | PostgreSQL database name | `weather_data` | ✅ Yes |
| `user` | PostgreSQL username | `zero` | ✅ Yes |
| `password` | PostgreSQL password | `123` | ✅ Yes |

### Get OpenWeatherMap API Key

1. Visit [OpenWeatherMap](https://openweathermap.org/api)
2. Click "Sign Up" and create a free account
3. Go to "API Keys" in the menu
4. Copy your default key
5. Update `config/.env` with your key
6. Restart: `docker-compose restart`

### Modify Execution Frequency

To change the pipeline execution frequency, edit `dags/weather_dag.py`:

```python
@dag(
    dag_id='weather_pipeline',
    # ... other configurations ...
    schedule='0 */1 * * *',  # ← Modify this line
)
```

**Schedule Examples (Cron)**:

| Schedule | Frequency |
|----------|-----------|
| `0 */1 * * *` | Every hour |
| `0 0 * * *` | Daily at midnight |
| `0 0 * * 0` | Weekly (Sunday) |
| `0 0 1 * *` | Monthly (1st day) |
| `*/5 * * * *` | Every 5 minutes |

After editing, restart the scheduler:
```bash
docker-compose restart airflow-scheduler
```

## 📁 Project Structure

```
pipeline-weather/
│
├── README.md                  # This file (bilingual)
├── main.py                    # Main script
├── pyproject.toml             # Project dependencies (uv/pip)
├── docker-compose.yaml        # Docker Compose orchestration
├── .python-version            # Python version (3.14)
├── uv.lock                    # Dependency lock file
├── .gitignore                 # Git ignored files
│
├── config/
│   └── .env                   # Environment variables (⚠️ DO NOT COMMIT)
│
├── dags/
│   └── weather_dag.py         # Airflow DAG - Orchestrates the pipeline
│
├── src/
│   ├── extract_data.py        # EXTRACT: Fetches data from API
│   ├── transform_data.py      # TRANSFORM: Normalizes and cleans data
│   └── load_data.py           # LOAD: Inserts data into PostgreSQL
│
├── data/
│   ├── weather_data.json      # Raw data (auto-generated)
│   └── temp_data.parquet      # Transformed data (auto-generated)
│
└── notebooks/                 # Jupyter notebooks (optional for analysis)
```

### Module Details

#### 📥 `src/extract_data.py` - Data Extraction

Responsible for consuming the OpenWeatherMap API and saving raw data as JSON.

**Features**:
- ✅ HTTP request to OpenWeatherMap API
- ✅ Error handling (status code validation)
- ✅ Detailed logging at each step
- ✅ Automatic directory creation

**Flow**:
```
API URL → HTTP Request → Validation (status 200) → JSON File
                             ↓
                    data/weather_data.json
```

**Input**: `url` (string with API endpoint)  
**Output**: `data/weather_data.json` (JSON file)

#### 🔄 `src/transform_data.py` - Data Transformation

Performs structured transformations on raw data, preparing it for storage.

**Features**:
- ✅ Normalization of nested JSON structures
- ✅ Column renaming for better semantics
- ✅ Unix timestamp conversion to datetime
- ✅ Timezone conversion to America/Sao_Paulo
- ✅ Removal of unnecessary columns
- ✅ Data type validation

**Transformation Example**:

```
BEFORE (Nested JSON):
{
  "coord": {"lon": -46.6333, "lat": -23.5505},
  "main": {"temp": 25.5, "humidity": 60},
  "dt": 1711900800,
  "sys": {"sunrise": 1711881600, "sunset": 1711927200}
}

AFTER (Normalized):
latitude  | longitude  | temperature | humidity | datetime
-23.5505  | -46.6333   | 25.5        | 60       | 2026-03-31 12:00:00-03:00
```

**Input**: `data/weather_data.json`  
**Output**: Structured DataFrame + `data/temp_data.parquet`

#### 📤 `src/load_data.py` - Data Loading

Loads transformed data into the PostgreSQL database.

**Features**:
- ✅ Secure connection with PostgreSQL via SQLAlchemy
- ✅ Data insertion (append mode - no overwrites)
- ✅ Post-loading validation
- ✅ Logging of inserted record count

**Flow**:
```
DataFrame → SQLAlchemy Engine → PostgreSQL
                                     ↓
                            Table: weather_data
```

**Input**: Transformed DataFrame  
**Output**: `weather_data` table in PostgreSQL with persistent data

#### 🎯 `dags/weather_dag.py` - Orchestration

Defines the Apache Airflow DAG (Directed Acyclic Graph) that orchestrates the entire pipeline.

**Tasks**:

```
Task 1: extract()
├─ Calls: extract_weather_data(url)
├─ Output: data/weather_data.json
└─ Status: ✅ Success → next task

Task 2: transform()
├─ Calls: data_transformations()
├─ Saves: data/temp_data.parquet
└─ Status: ✅ Success → next task

Task 3: load()
├─ Reads: data/temp_data.parquet
├─ Calls: load_weather_data('weather_data', df)
└─ Status: ✅ Success → Pipeline complete!
```

**Dependencies**:
```
extract() → transform() → load()
```

**DAG Configuration**:
- **Owner**: airflow
- **Retries**: 2 (retries 2 times on failure)
- **Retry Delay**: 5 minutes
- **Schedule**: Every hour (default) - configurable
- **Start Date**: 03/31/2026
- **Tags**: weather, etl, sao_paulo

## 📊 How to Use

### Access Airflow Web Interface

1. Open your browser and go to: `http://localhost:8080`
2. Login with:
   - **Username**: `airflow`
   - **Password**: `airflow`

### Activate and Run the DAG

#### Method 1: Via Web UI (Recommended for beginners)

1. **Search for "weather_pipeline"** in the DAGs list
2. **Click the toggle** (switch) to activate it
3. **Click the ▶️ Play button** to run manually
4. **Select "Trigger DAG"** in the window that opens
5. **Monitor execution** by clicking the DAG and viewing task status

#### Method 2: Via Command Line

```bash
# Run the DAG manually
docker-compose exec airflow-scheduler \
  airflow dags test weather_pipeline 2026-03-31

# List all registered DAGs
docker-compose exec airflow-scheduler \
  airflow dags list

# View execution status
docker-compose exec airflow-scheduler \
  airflow dags list-runs --dag-id weather_pipeline

# View logs for a specific task
docker-compose exec airflow-scheduler \
  airflow tasks logs weather_pipeline extract 2026-03-31
```

### Monitor Execution

```bash
# View scheduler logs in real-time
docker-compose logs -f airflow-scheduler

# View worker logs (task execution)
docker-compose logs -f airflow-worker

# Filter specific logs
docker-compose logs airflow-worker | grep extract
docker-compose logs airflow-worker | grep transform
docker-compose logs airflow-worker | grep load
```

## 🔄 ETL Pipeline - Detailed Flow

```
┌───────────────────────────────────────────────────���────────┐
│                    WEATHER PIPELINE                        │
│                    ===============                         │
│                                                            │
│  STEP 1️⃣  EXTRACT (Data Extraction)                      │
│  ├─ 🔗 Request OpenWeatherMap API                         │
│  ├─ ✅ Validate HTTP Status (200 = success)              │
│  ├─ 📥 Receive JSON with meteorological data             │
│  └─ 💾 Save to: data/weather_data.json                   │
│                       ↓                                    │
│                  ⏳ ~5 seconds                             │
│                       ↓                                    │
│  STEP 2️⃣  TRANSFORM (Data Transformation)                │
│  ├─ 📂 Read JSON file                                     │
│  ├─ 🔍 Normalize nested structure (coord, main, wind)    │
│  ├─ 📊 Extract weather data (weather array)              │
│  ├─ ✏️  Rename columns (temp → temperature)              │
│  ├─ ���� Convert Unix timestamps → datetime with TZ        │
│  ├─ 🗑️  Remove unnecessary columns                       │
│  ├─ ✨ Validate data types                               │
│  └─ 📦 Save to: data/temp_data.parquet                   │
│                       ↓                                    │
│                  ⏳ ~3 seconds                             │
│                       ↓                                    │
│  STEP 3️⃣  LOAD (Data Loading)                            │
│  ├─ 📥 Read Parquet data                                  │
│  ├─ 🔌 Connect to PostgreSQL (SQLAlchemy)                │
│  ├─ 📥 Insert into weather_data table (append mode)      │
│  ├─ ✅ Validate insertion (SELECT COUNT)                 │
│  └─ 📊 Log: "X records inserted"                         │
│                       ↓                                    │
│                  ⏳ ~2 seconds                             │
│                       ↓                                    │
│              ✅ PIPELINE COMPLETED SUCCESSFULLY             │
│                                                            │
│  ⏱️  TOTAL TIME: ~10 seconds                              │
│                                                            │
└───────���────────────────────────────────────────────────────┘
```

### Example Data

**Raw Data (JSON)**:
```json
{
  "coord": {"lon": -46.6333, "lat": -23.5505},
  "weather": [{
    "id": 802,
    "main": "Clouds",
    "description": "scattered clouds",
    "icon": "03d"
  }],
  "main": {
    "temp": 25.48,
    "feels_like": 24.67,
    "temp_min": 22.15,
    "temp_max": 27.12,
    "pressure": 1013,
    "humidity": 62,
    "sea_level": 1013,
    "grnd_level": 924
  },
  "visibility": 10000,
  "wind": {
    "speed": 3.5,
    "deg": 180,
    "gust": 5.2
  },
  "clouds": {"all": 60},
  "dt": 1711900800,
  "sys": {
    "sunrise": 1711881600,
    "sunset": 1711927200,
    "country": "BR"
  },
  "name": "São Paulo"
}
```

**Transformed Data (Database)**:

| city_name | latitude | longitude | temperature | feels_like | humidity | wind_speed | weather_main | weather_description | datetime |
|-----------|----------|-----------|-------------|-----------|----------|-----------|------------|---------------------|----------|
| São Paulo | -23.5505 | -46.6333 | 25.48 | 24.67 | 62 | 3.5 | Clouds | scattered clouds | 2026-03-31 12:00:00-03:00 |

### Database Stored Fields

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `city_name` | VARCHAR | City name | São Paulo |
| `latitude` | FLOAT | Latitude in degrees | -23.5505 |
| `longitude` | FLOAT | Longitude in degrees | -46.6333 |
| `temperature` | FLOAT | Current temperature (°C) | 25.48 |
| `feels_like` | FLOAT | Feels like temperature (°C) | 24.67 |
| `temp_min` | FLOAT | Minimum temperature (°C) | 22.15 |
| `temp_max` | FLOAT | Maximum temperature (°C) | 27.12 |
| `humidity` | INTEGER | Humidity (%) | 62 |
| `pressure` | INTEGER | Atmospheric pressure (hPa) | 1013 |
| `sea_level` | INTEGER | Sea level pressure (hPa) | 1013 |
| `grnd_level` | INTEGER | Ground level pressure (hPa) | 924 |
| `visibility` | INTEGER | Visibility (meters) | 10000 |
| `wind_speed` | FLOAT | Wind speed (m/s) | 3.5 |
| `wind_deg` | INTEGER | Wind direction (degrees) | 180 |
| `wind_gust` | FLOAT | Wind gust (m/s) | 5.2 |
| `clouds` | INTEGER | Cloud coverage (%) | 60 |
| `weather_id` | INTEGER | Weather condition ID | 802 |
| `weather_main` | VARCHAR | Main condition | Clouds, Rain, Clear |
| `weather_description` | VARCHAR | Detailed description | scattered clouds |
| `weather_icon` | VARCHAR | Weather icon | 03d |
| `country` | VARCHAR | Country code | BR |
| `datetime` | TIMESTAMP | Date/time (SP timezone) | 2026-03-31 12:00:00-03:00 |
| `sunrise` | TIMESTAMP | Sunrise time | 2026-03-31 06:00:00-03:00 |
| `sunset` | TIMESTAMP | Sunset time | 2026-03-31 18:00:00-03:00 |

## 🌐 Accessing Interfaces

### 1. Apache Airflow Web UI

**URL**: `http://localhost:8080`

**Credentials**:
- Username: `airflow`
- Password: `airflow`

**What you can do**:
- ✅ View active DAGs
- ✅ Monitor real-time executions with charts
- ✅ Check detailed logs for each task
- ✅ Trigger manual executions
- ✅ Set failure alerts
- ✅ View task dependencies (graph view)
- ✅ Schedule data backfills

### 2. Flower (Celery Monitor) - Optional

To enable the Celery monitoring interface:

```bash
docker-compose --profile flower up -d
```

**URL**: `http://localhost:5555`

**Features**:
- 📊 Monitor workers in real-time
- 📈 View task history
- ⚙️ Manage workers and queues

### 3. PostgreSQL - Direct Connection

#### Via psql (inside container)

```bash
# Enter PostgreSQL container
docker-compose exec postgres psql -U airflow -d airflow

# List tables
\dt

# View weather_data table schema
\d weather_data

# Count records
SELECT COUNT(*) FROM weather_data;

# Last 5 records
SELECT city_name, temperature, humidity, datetime 
FROM weather_data 
ORDER BY datetime DESC 
LIMIT 5;

# Exit
\q
```

#### Via Graphical SQL Client

Connect using a client like **DBeaver**, **pgAdmin**, or **VS Code Extension**:

| Configuration | Value |
|-------------|-------|
| **Host** | `localhost` |
| **Port** | `5432` |
| **Database** | `airflow` |
| **User** | `airflow` |
| **Password** | `airflow` |

#### Useful SQL Queries

```sql
-- Count total records
SELECT COUNT(*) as total_records FROM weather_data;

-- Average temperature per day
SELECT 
  DATE(datetime) as date,
  ROUND(AVG(temperature), 2) as avg_temp,
  MAX(temperature) as max_temp,
  MIN(temperature) as min_temp
FROM weather_data
GROUP BY DATE(datetime)
ORDER BY date DESC;

-- Most frequent weather condition
SELECT 
  weather_main,
  COUNT(*) as frequency,
  ROUND(100.0 * COUNT(*) / (SELECT COUNT(*) FROM weather_data), 2) as percentage
FROM weather_data
GROUP BY weather_main
ORDER BY frequency DESC;

-- Average humidity per hour
SELECT 
  EXTRACT(HOUR FROM datetime) as hour,
  ROUND(AVG(humidity), 1) as avg_humidity
FROM weather_data
GROUP BY EXTRACT(HOUR FROM datetime)
ORDER BY hour;

-- Last 24 hours of data
SELECT 
  city_name,
  temperature,
  humidity,
  weather_description,
  datetime
FROM weather_data
WHERE datetime >= NOW() - INTERVAL '24 hours'
ORDER BY datetime DESC;
```

## 🐛 Troubleshooting

### ❌ Problem: Containers don't start

**Error message**: `error response from daemon`

**Solutions**:

```bash
# 1. View complete error logs
docker-compose logs

# 2. Stop all containers
docker-compose down

# 3. Remove volumes (⚠️ deletes data!)
docker-compose down -v

# 4. Restart from scratch
docker system prune -a  # Remove all unused
docker-compose up -d

# 5. If still failing, check disk space
df -h
```

### ❌ Problem: "ModuleNotFoundError: No module named 'src'"

**Error message**: `Traceback ... ImportError: No module named src`

**Solution**: The import path is configured in `dags/weather_dag.py` (line 6):

```python
sys.path.insert(0, '/opt/airflow/src')
```

Verify the files exist:

```bash
docker-compose exec airflow-scheduler ls -la /opt/airflow/src/
```

Expected output:
```
-rw-r--r-- extract_data.py
-rw-r--r-- transform_data.py
-rw-r--r-- load_data.py
```

### ❌ Problem: PostgreSQL connection error

**Error message**: `could not translate host name "postgres" to address`

**Solution**:

```bash
# 1. Check if PostgreSQL is running
docker-compose ps postgres

# 2. Test connection
docker-compose exec postgres \
  psql -U airflow -d airflow -c "SELECT 1"

# 3. View PostgreSQL logs
docker-compose logs postgres

# 4. Restart PostgreSQL
docker-compose restart postgres

# 5. Check environment variables
docker-compose exec airflow-scheduler env | grep POSTGRES
```

### ❌ Problem: "API key invalid" (401 Unauthorized)

**Error message**:
```
Error fetching data: 401
Invalid API key
```

**Solution**:

1. Check the key is correct in `config/.env`
2. Did the key take less than 10 minutes to activate?
3. Generate a new key at [OpenWeatherMap](https://openweathermap.org/api)
4. Update `config/.env`
5. Restart: `docker-compose restart`

```bash
# Test key manually
curl "https://api.openweathermap.org/data/2.5/weather?q=Sao Paulo,BR&units=metric&appid=YOUR_API_KEY"
```

### ❌ Problem: Disk full or high memory usage

**Symptoms**: Container exit code 137, general slowness

**Solutions**:

```bash
# View space usage
docker system df

# Clean stopped containers
docker container prune -f

# Clean unused images
docker image prune -a -f

# Clean unused volumes
docker volume prune -f

# View memory usage
docker stats
