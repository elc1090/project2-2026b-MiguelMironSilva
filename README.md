# Projeto: Aplicação com persistência de dados em backend

![Substitua a imagem ao lado por um GIF/WEBP animado mostrando seu projeto - GIF animado do projeto. Imagem temporária de Moho Animation https://moho.lostmarble.com/products/moho-pro-special-halls-head-college](./moho_follow_through2.gif)

## Acesso

- Render: https://project2-2026b-miguelmironsilva.onrender.com/
- Netlify: https://effervescent-cranachan-cb04c0.netlify.app/

## Desenvolvedor(a)
- Miguel Miron Silva
- Ciência da Computação, Universidade Federal de Santa Maria

## Proposta
Projeto de implementação de site interativo, com foco na implementação do backend, sobre pesquisa e avaliação de filmes, como parte da disciplina de Desenvolvimento de Software para Web (ELC1090), ministrada pela dra. Andrea Schwertner Charão (andrea@inf.ufsm.br), na Universidade Federal de Santa Maria (UFSM). Cada usuário poderá criar sua própria conta, fazer login e ter acesso somente aos filmes favoritos e assistidos. O sistema deverá manter as informações de cada conta, incluindo seus filmes favoritados e assistidos. Ao fazer login novamente, o usuário deverá encontrar seus filmes exatamente como os encontrou.

### Funcionalidades
Browsing de filmes - capa, título, sinopse, gênero, ano, elenco, episódios, nota externa;
Poder salvar e favoritar filmes.
Todos os dados de filmes salvos e favoritados são persistentes.
Dados obtidos de uma API pública externa;
Backend próprio;
Persistência: base de dados SQLite;
Dar notas aos títulos;

## Parceria/cliente/usuário
Modalidade (A | B): A
Parceria: GABRIEL DA SILVA FRANÇA, RAFAEL PENTEADO KLAUE

## Feedback/comentário da parceria/cliente/usuário

- [Rafael Penteado Klaue](https://jazzy-hummingbird-05a247.netlify.app/): App de busca de filmes que filtra especificamente para filmes de faroeste (*Westerns*). Estilização excelente, muito de acordo com o tema escolhido. O sistema de avaliações funciona, mas parece que não há nada compartilhado entre os vários usuários - não dá pra ver as avaliações postadas por outros usuários. Algumas vezes o busca por palavras chave não pega filmes específicos (por exemplo, quando busco por "madre" e "treasure" não se adquire nada, mas "sierra" funciona para achar o filme The Treasure of the Sierra Madre). No mais, um app que cumpre rigorosamente com todos os requisitos pedidos, com bastante estilo!

## Desenvolvimento

### Processo

**Dia 10/09/2026** 
Estudo do projeto de estudo do aluno Gabriel Maroneze (https://github.com/elc1090/project1-2026b-gabriel-maroneze) feito para o Trabalho 1 da mesma disciplina. Usando a API TMDB (The Movie Database) para buscar os detalhes de filmes, ela faz um deploy completo de várias funcionalidades à serem implementadas no projeto em um webapp HTML/JavaScript/CSS (https://elc1090.github.io/project1-2026b-gabriel-maroneze/), sendo derivada de outro webapp para busca de filmes (https://github.com/mmj030703/Movie-Search-App/), com implementação funcional também  (https://movie-search-app-weld.vercel.app/).

Principal inspiração: Usar a API TMDB para fazer o fetching dos filmes. Foi feita uma conta no TMDB e requisicionada uma chave de API para uso próprio.

**Dia 14/09/2026** 
Adicionada a espinha dorsal do backend. Foi criado uma versão minimamente viável de uma aplicação em FastAPI, que foi primeiro rodada localmente dentro de um ambiente virtual, usando o servidor web ASGI Uvicorn para testes locais. Após isso, foi feito um deploy de um web service no serviço de hosting Render com uma chave de API da TMDB, que por sua vez também foi testado (https://project2-2026b-miguelmironsilva.onrender.com/), e feitas as configurações básicas para se fazer o diálogo com um frontend futuro através da Fast API, com a adição de configurações, serviço de TMDB e roteador de filmes, e sua testagem.

**Backend:**

Subsequentemente, elaborou-se o resto do backend, usando-se a ASGI [Uvicorn](https://uvicorn.dev/) para rodar a aplicação FastAPI, possibilitando a checagem de erros e debugação do backend antes de se fazer um deploy completo no hospedeiro externo. Após a configuração de um ambiente virtual para se hospedar da maneira mais limpa possível o ASGI do Uvicorn e todas suas dependências, usando-se um loop estritamente do computador para o computador para se fazer a interface inicial (através da porta 127.0.0.1:8000).

Logo depois, foram adicionadas as "pontas" para os filmes, que extraem os dados necessários deles, através do **routers/movies.py**, para se fazer a busca pelos filmes, a extração dos seus detalhes e dos créditos do filme. Após isso, foram adicionados os autenticadores, os arquivos **auth.py** em services, schemas e routers, cuja responsabilidade é a implementação e validação das contas de usuários no backend da aplicação, incluindo a implementação dos JWTs (JSON Web Tokens) que determinam qual usuário está logado no momento. O arquivo *database.py* foi expandido para assimilar a  lógica necessária para incluir a sessão local da base de dados que está rodando.  Os arquivos de **user.py** foram incluidos com o propósito de adicionar a lógica de favoritar, marcar como visto e dar avaliações aos filmes, além de deletar esses filmes. Os arquivos na pasta **models**, implementados através do SQLAlchemy, são responsáveis por salvar os modelos de usuários (id, nome, e-mail, hash da senha, data de criação) e os estados dos filmes relacionado à ele (id, id do usuário, id do tmdb, favoritado, visto, avaliação do usuário). É importante ressaltar que foram usadas as livrarias PyJWT para codificação e decodificação de tokens JWT, e a livraria pwdlib para fazer um *hashing* das senhas dos usuários, garantindo sua segurança mesmo que a base de dados seja hackeada. A livraria python-multipart foi adicionada devido à requisitos da FastAPI em analisar solicitações de login codificadas como formulários.

A divisão do backend em *services*, *schemas*, *models* e *routers* deixa o backend legível e altamente modular. Todas as chamadas feitas via HTTP e suas exceções são lidadas através da camada **router**, os arquivos **schemas** descreve os dados que cruzam as camadas, o *models* descreve os dados dentro da própria base de dados, e a camada **services** faz o trabalho real de mudar dados, dialogar com o banco de dados do TMDB e outras tarefas em geral. Isso isola os componentes do backend, possibilitando mudar a API externa sem reescrever as rotas HTTP, mudar a implementação do banco de dados sem reescrever a API voltada para o frontend e validar os dados da API independentemente dos modelos de banco de dados.

**Frontend:**

O frontend do projeto passou por dois passos distintos - uma fase de testes onde foi conectado um frontend de exemplo com o backend para propósitos de teste, e uma subsequente refatoração e elaboração do backend do JavaScript puro para React. Primeiro, foram implementadas as funções de criação de usuário e login em um frontend de uma aplicação de exemplo (https://github.com/mmj030703/Movie-Search-App/), elaborada por Mayank Mahavir Jain. Depois dessas funcionalidades principais serem implementadas e testadas, sendo primeiro implementadas de forma "primitiva" no HTML e no JavaScript antes de se elaborar o CSS, também foram implementadas as funções de favoritar, marcar como visto e dar notas adicionais aos filmes no frontend, através do mesmo processo. Após outros ajustes estéticos ao CSS relacionados à implementação e coloração dos botões, foi implementada uma livraria pessoal do usuário, onde ele poderia ver quais filmes ele favoritou/viu/avaliou. Após isso, tomando como inspiração outro projeto elaborado em cima do projeto de exemplo anterior (https://elc1090.github.io/project1-2026b-gabriel-maroneze/), feito por Gabriel Maroneze, para expedir o processo de desenvolvimento, foi feita uma refatoração e migração da parte do Javascript para React.

O arquivo principal de script.js foi "quebrado" em aproximadamente onze arquivos diferentes - a lógica de busca de filmes entrou no arquivo **MovieSearch.jsx**, a representação dos cards dos filmes em **MovieCard.jsx**, a representação dos detalhes dos filmes em **MovieDetails.jsx**, a interface de usuário para favoritar/ver/avaliar filmes em **MovieControls.jsx** a lógica do painel de login e criação de novos usuários em **AuthPanel.jsx**, a livraria de filmes em **MyMovies.jsx**, a exibição do elenco em **CastList.jsx**, e as funções da API em **services/api.js** (com ajustes para se comunicar com a base de dados hospedada no serviço React). O CSS também foi subsequentemente estilizado para fazer a interface com o React, e o arquivo **index.html** foi deixado muito menor (de 354 para 24 linhas), deixando-se só uma *stylesheet* e um ponto de montagem para o React, com a aplicação inteira sendo gerada através da árvore de componentes do React. Após isso, só foram feitas algumas modificações superficiais no CSS.

Duas principais falhas no projeto do frontend. Um dos requisitos do projeto, a implementação do TypeScript em adição ao React, nunca foi feita. Isso poderia melhor a robustez e manutenibilidade do projeto, melhorando suas capacidades de recusar requisitos mal-formulados e deixar as interfaces entre componentes mais robustas. Porém, a necessidade de incluir manipuladores de eventos, propriedades de componentes, respostas de API, estado e, possivelmente, bibliotecas de terceiros no projeto fariam uma refatoração para TypeScript um componente bastante pesado. A outra falha é que, apesar da miniaturização drástica do HTML graças à transferência para React, a a modularização possível através do mesmo, o arquivo de CSS ficou enorme, contando com 1412 linhas no total, principalmente devido à primitivos de botões reusáveis e repetidas implementações em seletores de componentes. Porém, isso não é de grande urgência, dado que mudanças no CSS raramente "quebram" algo importante (com exceções) ou deixam o app inutilizável.

### Trechos de código

**Interface do FastAPI**

````
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.database import Base, engine
from app.models import User, UserMovie
from app.routers import movies, auth, user_movies

Base.metadata.create_all(bind=engine)

app = FastAPI(
    title="Webapp Filmes API",
    version="0.1.0",
)

allowed_origins = [
    "http://localhost:5173",
    "http://127.0.0.1:5173",
    "https://effervescent-cranachan-cb04c0.netlify.app",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=allowed_origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(movies.router)
app.include_router(auth.router)
app.include_router(user_movies.router)


@app.get("/")
def root():
    return {
        "message": "API Webapp Filmes está rodando"
    }


@app.get("/health")
def health():
    return {
        "status": "ok"
    }
````

## Config da API: ##

````
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    tmdb_api_key: str
    jwt_secret_key: str

    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
    )


settings = Settings()
````

## Manejamento do token de acesso após login: ##

````
const TOKEN_KEY = "access_token";

export const getToken = () => {
    return sessionStorage.getItem(TOKEN_KEY);
};

export const setToken = (token) => {
    sessionStorage.setItem(TOKEN_KEY, token);
};

export const clearToken = () => {
    sessionStorage.removeItem(TOKEN_KEY);
};
````

## Tecnologias

### Linguagens e afins
Backend
- Linguagem: [Python](https://docs.python.org/3/)
- Framework: [FastAPI](https://devdocs.io/fastapi/) 
- Banco de dados: [SQLite](https://devdocs.io/sqlite/) + [SQLAlchemy](https://docs.sqlalchemy.org/en/20/) 
- Hospedagem: [Render](https://render.com/)

Frontend
- [HTML5](https://devdocs.io/html/) + [CSS3](https://devdocs.io/css/) + [JavaScript/TypeScript](https://devdocs.io/javascript/) 
- Frameworks: [React.js](https://devdocs.io/react/) + [Vite](https://devdocs.io/vite/) 
- Hospedagem: [Netlify](https://app.netlify.com/)

### Ambiente de desenvolvimento
- [Git](https://git-scm.com/)
- [Sublime Text Editor](https://www.sublimetext.com/)
- IA usada para planejamento, tirada de dúvidas e debugging: [GPT-5.6 Luna](https://chatgpt.com/)

## Referências e créditos

Webapp para busca de filmes - projeto original
- https://github.com/mmj030703/Movie-Search-App/
- https://movie-search-app-weld.vercel.app/

Webapp para busca de filmes, com funcionalidades adicionais
- https://github.com/elc1090/project1-2026b-gabriel-maroneze
- https://elc1090.github.io/project1-2026b-gabriel-maroneze/

## E-mail para contato
 - misilva@inf.ufsm.br
 - miguelmironsilva@gmail.com


