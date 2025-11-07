# Laravel E-commerce (Dockerizado)

![Laravel](https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)

Este é um projeto de e-commerce construído com Laravel 11, totalmente containerizado com Docker. O ambiente de desenvolvimento é gerenciado pelo Docker Compose e inclui Nginx, PHP 8.3, MySQL 8.0 e um servidor de desenvolvimento Vite para hot-reloading.

## 💻 Tecnologias Utilizadas

* **Backend:** Laravel 11
* **Frontend:** Vite (com hot-reloading)
* **Servidor Web:** Nginx
* **Aplicação:** PHP 8.3
* **Banco de Dados:** MySQL 8.0
* **Containerização:** Docker & Docker Compose

## 🚀 Instalação e Execução

Este projeto é projetado para ser executado 100% dentro do Docker. Você não precisa instalar PHP, Composer, Nginx ou MySQL na sua máquina local.

**Pré-requisitos:**
* **Docker**
* **Docker Compose**

### Passos para Instalação

1.  **Clonar o Repositório:**
    ```bash
    git clone [URL_DO_SEU_REPOSITORIO]
    cd Laravel_E-commerce
    ```
    *(Se você usou o script `setup-laravel-docker.sh`, pule este passo e apenas entre na pasta `cd Laravel_E-commerce`)*

2.  **Baixar o Laravel:**
    O `docker-compose.yml` inclui um serviço "composer" para esta exata finalidade.
    ```bash
    docker-compose run --rm composer create-project laravel/laravel . --prefer-dist "11.*"
    ```

3.  **Ajustar Permissões (Obrigatório no Linux):**
    O Docker cria os arquivos do Laravel como `root`. Precisamos dar a posse deles ao seu usuário local para evitar problemas de permissão.
    ```bash
    sudo chown -R $USER:$USER src/
    ```

4.  **Subir os Contêineres:**
    Este comando irá construir as imagens e iniciar todos os serviços (Nginx, App, DB, Vite) em segundo plano (`-d`).
    ```bash
    docker-compose up -d --build
    ```

5.  **Instalar Dependências NPM:**
    O serviço `vite` precisa das dependências do `node_modules` para rodar.
    ```bash
    docker-compose exec app npm install
    ```

6.  **Gerar a Chave da Aplicação:**
    O Laravel precisa de uma chave de encriptação para funcionar.
    ```bash
    docker-compose exec artisan key:generate
    ```

7.  **Rodar as Migrações:**
    Isso criará as tabelas do Laravel no banco de dados.
    ```bash
    docker-compose exec artisan migrate
    ```

8.  **Pronto!**
    Acesse a aplicação no seu navegador: **[http://localhost](http://localhost)**
    
    Você verá a página de boas-vindas do Laravel, e o Vite estará rodando com hot-reloading.

---

## 🛠️ Comandos Úteis do Docker

Como toda a aplicação roda no Docker, você usará `docker-compose` para interagir com ela.

* **Executar comandos Artisan:**
    ```bash
    docker-compose exec artisan [SEU_COMANDO_AQUI]
    
    # Exemplo:
    # docker-compose exec artisan make:model Produto
    # docker-compose exec artisan make:controller ProdutoController --resource
    ```

* **Executar o Composer:**
    (Use `run --rm` para criar um contêiner temporário que se auto-destrói)
    ```bash
    docker-compose run --rm composer [SEU_COMANDO_AQUI]
    
    # Exemplo:
    # docker-compose run --rm composer require laravel/breeze --dev
    ```

* **Executar o NPM/Vite:**
    (O Vite já está rodando com `npm run dev`)
    ```bash
    # Para instalar uma nova dependência JS:
    docker-compose exec app npm install [PACOTE]
    
    # Exemplo:
    # docker-compose exec app npm install -D tailwindcss
    ```

* **Parar a Aplicação:**
    (Para os serviços e remove os contêineres)
    ```bash
    docker-compose down
    ```

* **Ver Logs (em tempo real):**
    (Útil para debugar)
    ```bash
    docker-compose logs -f
    
    # Para ver logs de um serviço específico (ex: app ou vite):
    # docker-compose logs -f app
    # docker-compose logs -f vite
    ```

## 🐳 Visão Geral dos Serviços

| Serviço | Imagem | Porta Exposta | Descrição |
| :--- | :--- | :--- | :--- |
| `nginx` | `nginx:alpine` | `80:80` | Servidor web que atua como proxy reverso para o `app` (PHP) e `vite` (JS/CSS). |
| `app` | `php:8.3-fpm` | - | O contêiner principal que executa a aplicação Laravel via PHP-FPM. |
| `db` | `mysql:8.0` | `3306:3306` | O servidor de banco de dados MySQL. Os dados persistem em um volume `db-data`. |
| `vite` | `node:18-alpine` | `5173:5173` | Executa o servidor de desenvolvimento do Vite (`npm run dev`) para hot-reloading. |
| `composer`| `composer:latest` | - | Serviço utilitário para executar comandos `composer` sem instalá-lo localmente. |
| `artisan` | (build local) | - | Serviço utilitário para executar comandos `php artisan` com as mesmas configurações do `app`. |
