# Vigah Admin - Dockerized Fullstack Application

Este repositório contém a estrutura de containers para uma aplicação fullstack (API Node.js, Frontend Vue.js) e um banco de dados PostgreSQL, tudo orquestrado com Docker Compose para facilitar o desenvolvimento e a implantação.

## Estrutura do Projeto
A estrutura de diretórios do projeto é organizada da seguinte forma:

```
. My project (vigah-admin)
|
├── docker/
│   ├── client/
│   │   ├── Dockerfile             # Dockerfile para o frontend Vue.js
│   │   └── nginx.conf             # Configuração Nginx para servir o frontend
│   |
|   ├── node/
│   |    └── Dockerfile             # Dockerfile para a API Node.js
|   |
|   ├── .env                           # Variáveis de ambiente para o Docker Compose
|   └── docker-compose.yml             # Orquestração dos serviços Docker
|
├── vigah-api/                     # Pasta contendo o código da sua API Node.js
│   ├── server.js                  # Ponto de entrada da API
│   ├── package.json
│   └── ... (outros arquivos da API)
|
└── vigah-client/                  # Pasta contendo o código do seu frontend Vue.js
    ├── package.json
    ├── public/
    ├── src/
    └── ... (outros arquivos do frontend)
```

## Pré-requisitos
Antes de iniciar, certifique-se de ter os seguintes softwares instalados em sua máquina:

Docker Desktop: Inclui o Docker Engine e o Docker Compose.

## Configuração Inicial

### Clone o Repositório:

```
git clone <URL_DO_SEU_REPOSITORIO>
cd vigah-admin
cp .env.example .env
```

### Variáveis de Ambiente (.env):

Preencha o arquivo .env com as seguintes variáveis. Altere os valores padrão para senhas seguras em produção!

```
# Nome do projeto (usado para prefixar nomes de containers)
PROJECT_NAME=my-project

# Variáveis do Banco de Dados PostgreSQL
POSTGRES_DB=mydb
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mypassword

# Portas da Aplicação
NODE_PORT=3000
VUE_PORT=8080

# URL do banco de dados para a API Node.js
# O nome 'db' é o nome do serviço do banco de dados no docker-compose.yml
DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}
```


## Como Rodar a Aplicação

Para iniciar a aplicação completa (API, Frontend e Banco de Dados), siga estes passos:

- Tenha as aplicações de api e client na raiz do projeto
- Navegue até a pasta docker
- Rode o comando para dar build nos containers

```
docker compose up -d --build
```

-d: Executa os contêineres em modo "detached" (em segundo plano).

--build: Força a reconstrução das imagens antes de iniciar os contêineres (útil quando você faz alterações nos Dockerfiles ou nas dependências).

## Acessar a Aplicação:

### Frontend (Vue.js):
Abra seu navegador e acesse: http://localhost:8080 (ou a porta definida em VUE_PORT no seu .env).

### API (Node.js):
Sua API estará acessível em: http://localhost:3000 (ou a porta definida em NODE_PORT no seu .env).

## Comandos Úteis do Docker Compose

Parar os contêineres (mantendo os volumes):
```
docker compose stop
```

Parar e remover os contêineres, redes e volumes (cuidado com os dados do banco!):
```
docker compose down
```

Para remover também os volumes nomeados (e, portanto, os dados do banco de dados db_data):
```
docker compose down -v
```

Visualizar os logs de todos os serviços:
```
docker compose logs -f
```

Para visualizar os logs de um serviço específico (ex: api):
```
docker compose logs -f api
```

Entrar no shell de um contêiner (ex: para a API Node.js):
```
docker compose exec api sh # Ou bash, dependendo da imagem
```

Reconstruir um serviço específico (ex: apenas a API):
```
docker compose up -d --no-deps --build api
```

## Considerações de Desenvolvimento
Sincronização de Código: Graças aos volumes montados (./vigah-api:/app e ./vigah-client:/app), as alterações que você fizer no seu código-fonte local (vigah-api/ e vigah-client/) serão refletidas instantaneamente dentro dos contêineres. Geralmente, as aplicações Node.js e Vue.js com modo de desenvolvimento têm "watchers" que recarregam automaticamente o servidor ou a página ao detectar mudanças.

Acesso à API pelo Frontend: No seu código frontend (Vue.js), quando você precisar fazer chamadas para a API, use a URL completa http://localhost:3000 (ou a porta que você configurou). Dentro da rede Docker, os serviços podem se comunicar usando seus nomes de serviço (ex: http://api:3000), mas do frontend rodando no seu navegador, você precisa do localhost. Se você implementou o proxy Nginx para a API no nginx.conf, seu frontend pode chamar /api/ e o Nginx fará o proxy.

Persistência de Dados: O volume nomeado db_data garante que os dados do seu banco de dados PostgreSQL persistam mesmo se o contêiner db for removido e recriado.