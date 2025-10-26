<div align="center">

# Docker: Criando e Gerenciando Containers

[![Curso](https://img.shields.io/badge/Alura-Docker-blue)](https://www.alura.com.br/course/docker-criando-gerenciando-containers)
[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Guia completo de Docker: da instalação à orquestração com Docker Compose**

[Documentação Oficial](https://docs.docker.com/) • [Docker Hub](https://hub.docker.com/) • [Curso na Alura](https://www.alura.com.br/course/docker-criando-gerenciando-containers)

</div>

---

## 📋 Sobre o Projeto

Este repositório contém um guia completo e prático sobre **Docker**, desenvolvido durante o curso da **Alura**. O material abrange desde conceitos fundamentais até tópicos avançados como redes, volumes e Docker Compose.

**O que você vai aprender:**
- Conceitos fundamentais de containers e imagens
- Gerenciamento completo do ciclo de vida de containers
- Criação de imagens customizadas com Dockerfile
- Persistência de dados com volumes e bind mounts
- Comunicação entre containers usando redes Docker
- Coordenação de múltiplos containers com Docker Compose

**Pré-requisitos:**
- Sistema operacional Linux, Windows ou macOS
- Conhecimentos básicos de terminal/linha de comando
- Docker instalado (guia de instalação incluído)

---

## 📋 Índice

### 📘 Fundamentos
- [Conceitos fundamentais](#-conceitos-fundamentais)
- [Gerenciando imagens Docker](#-gerenciando-imagens-docker)
- [Instalação do Docker](#-instalação-do-docker)

### 🐳 Trabalhando com Containers
- [Ciclo de vida dos containers](#-ciclo-de-vida-dos-containers)
- [Mapeamento de portas](#-mapeamento-de-portas)

### 📦 Imagens Customizadas
- [Criando suas próprias imagens](#-criando-suas-próprias-imagens-docker)
- [Enviando imagens para o Docker Hub](#-enviando-imagens-para-o-docker-hub)

### 💾 Persistência de Dados
- [Introdução à persistência](#-persistência-de-dados)
- [Bind Mounts](#bind-mounts-persistindo-dados-com-diretórios-do-host)
- [Volumes](#volumes-armazenamento-gerenciado-pelo-docker)
- [tmpfs](#tmpfs-armazenamento-temporário-em-memória)

### 🌐 Redes e Comunicação
- [Redes Docker](#-redes-docker-comunicação-entre-containers)
- [Exemplo prático: Aplicação + Banco](#comunicação-entre-containers-na-prática-aplicação--banco-de-dados)

### 🐝 Docker Compose
- [Introdução ao Docker Compose](#-docker-compose-coordenando-múltiplos-containers)
- [Instalando o Docker Compose](#instalando-o-docker-compose)

### 📚 Referência
- [Resumo dos conceitos](#resumo-dos-conceitos-principais)
- [Boas práticas](#boas-práticas-gerais)
- [Referência rápida de comandos](#referência-rápida-de-comandos)
- [Recursos adicionais](#recursos-adicionais)

---

## 📖 Conceitos fundamentais

**[Docker Hub](https://hub.docker.com/):**
- Repositório central de imagens Docker
- Milhares de imagens oficiais e da comunidade
- Fonte principal para baixar e compartilhar imagens

**Imagens vs Containers:**
- **Imagens**: Templates estáticos que definem o ambiente
- **Containers**: Instâncias em execução baseadas em imagens
- Uma imagem pode gerar múltiplos containers

**Isolamento de containers:**
- Cada container possui seu próprio sistema de arquivos
- Namespaces isolam processos, rede e recursos
- Modificações em um container não afetam o host ou outros containers

---

## 📊 Gerenciando imagens Docker

**O que são imagens:**
- Conjunto de camadas empilhadas que formam um template
- Cada camada possui um ID único e é independente
- Imagens são imutáveis (read-only) após criação
- Base para criar containers

**Comandos essenciais para imagens:**
```bash
docker images                                # Lista todas as imagens baixadas
docker image ls                              # Comando alternativo
docker pull <IMAGEM>                         # Baixa uma imagem específica
docker inspect <IMAGE_ID>                    # Mostra informações detalhadas
docker history <IMAGE_ID>                    # Mostra as camadas da imagem
docker rmi <IMAGE_ID>                        # Remove uma imagem
```

**Exemplo prático:**
```bash
docker pull ubuntu                           # Baixa a imagem Ubuntu
docker images                                # Verifica imagens disponíveis
docker history ubuntu                        # Mostra camadas da imagem
```

**Estrutura de uma imagem:**
- **Camadas**: Cada comando no Dockerfile gera uma camada
- **Reutilização**: Camadas são compartilhadas entre imagens
- **Eficiência**: Docker baixa apenas camadas que não existem
- **Tamanho**: Mostrado na coluna SIZE do comando `docker images`

**Como imagens viram containers:**
- Container = Imagem + Camada de escrita (read-write)
- Camada adicional é temporária e leve
- Múltiplos containers podem usar a mesma imagem
- Dados são perdidos quando container é removido

**Por que containers são leves:**
- Reutilizam camadas da imagem base
- Apenas uma camada fina de escrita é adicionada
- Compartilhamento de recursos entre containers
- Otimização de espaço e performance

---

## ⚙️ Instalação do Docker

### Arch Linux e derivados

```bash
sudo pacman -Syu                        # Atualiza o sistema.
sudo pacman -S docker                   # Instala o Docker Engine.
sudo systemctl start docker.service     # Inicia o serviço do Docker.
sudo systemctl enable docker.service    # Habilita o Docker para iniciar com o sistema.
sudo usermod -aG docker $USER           # Adiciona seu usuário ao grupo do Docker (necessário reiniciar a máquina para validar).
sudo docker run hello-world             # Verifica se a instalação foi bem-sucedida.
```
> *Nota: Caso use outra distribuição, como `Ubuntu`, `Debian` ou `Fedora`, recomendo ver na [documentação oficial do *Docker*](https://docs.docker.com/engine/install/).*

---

## 🔄 Ciclo de vida dos containers

**Comandos essenciais para gerenciar containers:**

```bash
# Criar e executar containers
docker run <IMAGEM>                        # Cria e executa um novo container
docker run -d <IMAGEM>                     # Executa em background (detached)
docker run -it <IMAGEM> bash               # Executa em modo interativo

# Gerenciar estado dos containers
docker start <CONTAINER_ID>                # Inicia um container parado
docker stop <CONTAINER_ID>                 # Para um container em execução
docker stop -t=0 <CONTAINER_ID>            # Para instantaneamente
docker pause <CONTAINER_ID>                # Pausa um container (preserva estado)
docker unpause <CONTAINER_ID>              # Despausa um container pausado

# Interagir com containers em execução
docker exec -it <CONTAINER_ID> bash        # Acessa terminal do container
docker exec <CONTAINER_ID> <COMANDO>       # Executa comando no container

# Remover containers
docker rm <CONTAINER_ID>                   # Remove container parado
docker rm -f <CONTAINER_ID>                # Remove container forçadamente
```

**Estados dos containers:**
- **Running**: Container em execução
- **Paused**: Container pausado (processos congelados)
- **Stopped**: Container parado (processos finalizados)
- **Created**: Container criado mas não iniciado

---

**Listando containers:**
```bash
docker ps                               # Lista containers em execução
docker ps -a                            # Lista todos os containers (incluindo parados)
docker container ls                     # Comando alternativo para docker ps
docker container ls -a                  # Comando alternativo para docker ps -a
```

**Nomeando containers:**
```bash
docker run --name meu-container ubuntu  # Cria container com nome personalizado
docker stop meu-container               # Para container pelo nome
docker start meu-container              # Inicia container pelo nome
```

> *Nota: Containers podem ser referenciados pelo ID ou nome em qualquer comando.*

---

## 🔌 Mapeamento de portas

Por padrão, containers ficam isolados da rede do host. Para acessar aplicações web, é necessário mapear portas do container para o host.

**Conceito fundamental:**
- Containers possuem suas próprias interfaces de rede
- Portas internas do container não são acessíveis externamente
- Mapeamento permite acesso externo às aplicações

**Mapeamento automático (flag -P):**
```bash
docker run -d -P dockersamples/static-site            # Mapeia automaticamente para portas aleatórias
docker port <CONTAINER_ID>                            # Mostra o mapeamento de portas
```
*Resultado: `0.0.0.0:49154->80/tcp` (porta 80 do container → porta 49154 do host)*

**Mapeamento específico (flag -p):**
```bash
docker run -d -p 8080:80 dockersamples/static-site    # Host:Container
docker run -d -p 3000:3000 <IMAGEM>                   # Mesma porta host e container
docker run -d -p 80:80 -p 443:443 <IMAGEM>            # Múltiplas portas
```

**Exemplo prático completo:**
```bash
# 1. Executa aplicação web em background
docker run -d -p 8080:80 dockersamples/static-site

# 2. Verifica se está rodando
docker ps

# 3. Acessa no navegador
# http://localhost:8080

# 4. Remove quando não precisar mais
docker rm -f <CONTAINER_ID>
```

**Comandos úteis para gerenciar portas:**
```bash
docker ps                                             # Mostra mapeamento na coluna PORTS
docker port <CONTAINER_ID>                            # Lista todas as portas mapeadas
docker logs <CONTAINER_ID>                            # Visualiza logs da aplicação
```

**Comparação das flags:**
- `-P`: Mapeamento automático para portas aleatórias (ex: 49154->80)
- `-p`: Controle total sobre mapeamento (ex: 8080:80)
- Sem flag: Container inacessível externamente

---

## 🛠️ Criando suas próprias imagens Docker

**Fluxo de criação:**
1. Definir um arquivo `Dockerfile`
2. Criar a imagem a partir do `Dockerfile`
3. Executar um container com `docker run`

**Dockerfile - Instruções principais:**

```dockerfile
FROM node:14                    # Define imagem base
WORKDIR /app-node               # Define diretório de trabalho
ARG PORT_BUILD=6000             # Variável para construção da imagem
ENV PORT=$PORT_BUILD            # Variável de ambiente para o container
EXPOSE $PORT_BUILD              # Documenta porta exposta
COPY . .                        # Copia arquivos do host para a imagem
RUN npm install                 # Executa comandos durante a criação da imagem
ENTRYPOINT npm start            # Define comando executado quando container iniciar
```

**Instruções do Dockerfile:**
- `FROM`: Define a imagem base (ex: `FROM node:14`)
- `WORKDIR`: Define o diretório de trabalho padrão
- `ARG`: Define variável usada apenas durante a construção da imagem
- `ENV`: Define variável de ambiente disponível dentro do container
- `EXPOSE`: Documenta qual porta a aplicação usa (não mapeia automaticamente)
- `COPY`: Copia arquivos/diretórios do host para a imagem
- `RUN`: Executa comandos durante a construção da imagem
- `ENTRYPOINT`: Define o comando executado quando o container iniciar

**Criando uma imagem:**
```bash
# Criar imagem a partir do Dockerfile no diretório atual
docker build -t nome-usuario/nome-app:versao .

# Exemplo prático
docker build -t gabricoto/app-node:1.0 .

# Criando versões diferentes
docker build -t gabricoto/app-node:1.1 .    # Com EXPOSE
docker build -t gabricoto/app-node:1.2 .    # Com variáveis de ambiente
```

**Executando container da imagem criada:**
```bash
# Executar em background com mapeamento de porta
docker run -d -p 8081:3000 gabricoto/app-node:1.0

# Executar sem mapeamento (apenas documentação da porta)
docker run -d gabricoto/app-node:1.1

# Executar com porta customizada
docker run -d -p 9090:6000 gabricoto/app-node:1.2

# Verificar se está funcionando
docker ps
```

**Comandos úteis para imagens:**
```bash
docker images                   # Lista imagens criadas
docker history <IMAGE_ID>       # Mostra camadas da imagem
docker inspect <IMAGE_ID>       # Informações detalhadas da imagem
docker stop $(docker container ls -q)  # Para todos os containers em execução
```

**Conceitos avançados:**

**Documentando portas com EXPOSE:**
- `EXPOSE 3000`: Documenta que a aplicação usa a porta 3000
- Não faz mapeamento automático, apenas documenta
- Aparece na coluna PORTS do `docker ps`
- Facilita para outros desenvolvedores saberem qual porta mapear

**Variáveis de ambiente:**
- `ARG`: Usada apenas durante a construção da imagem (build time)
- `ENV`: Disponível dentro do container em execução (runtime)
- Permite parametrização e configuração flexível

**Exemplo de aplicação Node.js com porta variável:**
```javascript
// index.js
app.listen(process.env.PORT, ()=>{
    console.log(`Server is listening on port ${process.env.PORT}`)
})
```

**Vantagens de criar suas próprias imagens:**
- Não depender totalmente de imagens de terceiros
- Customizar ambiente específico para sua aplicação
- Incluir dependências e configurações necessárias
- Facilitar deploy e distribuição da aplicação

**Exemplo de estrutura de projeto:**
```
meu-projeto/
├── Dockerfile
├── package.json
├── index.js
└── outros arquivos...
```

---

## ☁️ Enviando imagens para o Docker Hub

**Preparação:**
1. Criar conta no [Docker Hub](https://hub.docker.com/)
2. Fazer login via terminal
3. Criar tag com seu nome de usuário
4. Fazer push da imagem

**Autenticação no terminal:**
```bash
docker login -u seu-usuario-dockerhub
# Digite sua senha quando solicitado
```

**Criando tags para Docker Hub:**
```bash
# Copiar imagem local para tag do Docker Hub
docker tag gabricoto/app-node:1.0 seu-usuario/app-node:1.0

# Exemplo prático
docker tag gabricoto/app-node:1.0 aluradocker/app-node:1.0
docker tag gabricoto/app-node:1.2 aluradocker/app-node:1.2
```

**Enviando imagens:**
```bash
# Push de uma versão específica
docker push aluradocker/app-node:1.0

# Push de múltiplas versões
docker push aluradocker/app-node:1.2
```

> *Nota: Esse repositório está disponível no Docker Hub acessando por [aqui](https://hub.docker.com/r/gabricoto/app-node).*

**Comandos úteis:**
```bash
docker images                   # Verificar imagens locais
docker tag <origem> <destino>   # Criar nova tag da imagem
docker push <repositorio:tag>   # Enviar para Docker Hub
```

**Conceitos importantes:**

**Nomenclatura no Docker Hub:**
- Formato: `usuario-ou-organizacao/nome-da-imagem:tag`
- Só é possível fazer push para repositórios do seu usuário
- Exemplo: `gabricoto/app-node:1.0`

**Otimização de camadas:**
- Docker Hub reutiliza camadas já existentes
- Apenas camadas modificadas são enviadas
- Reduz tempo de upload e espaço de armazenamento

**Versionamento:**
- Use tags semânticas (1.0, 1.1, 1.2, latest)
- Diferentes versões podem coexistir no mesmo repositório
- Facilita controle de versões e rollbacks

**Fluxo completo de exemplo:**
```bash
# 1. Construir imagem
docker build -t gabricoto/app-node:1.0 .

# 2. Testar localmente
docker run -d -p 8080:3000 gabricoto/app-node:1.0

# 3. Fazer login no Docker Hub
docker login -u gabricoto

# 4. Criar tag para Docker Hub (se necessário)
docker tag gabricoto/app-node:1.0 gabricoto/app-node:1.0

# 5. Enviar para Docker Hub
docker push gabricoto/app-node:1.0
```

---

## 💾 Persistência de dados

### Tamanho dos containers

**Comandos de limpeza do sistema:**
```bash
# Remover todos os containers parados
docker container prune

# Remover todos os containers (forçado)
docker container rm $(docker container ls -aq) --force

# Remover imagens não utilizadas
docker image prune

# Remover todas as imagens
docker rmi $(docker image ls -aq) --force

# Limpeza completa (containers, imagens, volumes, redes)
docker system prune -a
```

**Verificando tamanho dos containers:**
```bash
docker ps -s                    # Mostra tamanho dos containers em execução
docker container ls -s          # Comando alternativo
```

**Colunas de tamanho:**
- **SIZE**: Tamanho da camada read-write (dados temporários do container)
- **Virtual Size**: Tamanho da imagem + camada read-write

**Como funciona o tamanho:**

**Container recém-criado:**
```bash
docker run -it ubuntu bash
docker ps -s
# SIZE: 0B (virtual 72.8MB)
# Apenas a imagem base, sem dados adicionais
```

**Container após modificações:**
```bash
# Dentro do container:
apt-get update

# Em outro terminal:
docker ps -s
# SIZE: 16.2MB (virtual 89MB)
# Dados foram adicionados à camada read-write
```

**Estrutura das camadas:**
```bash
docker history ubuntu           # Mostra camadas da imagem
```

**Conceitos importantes:**

**Camadas dos containers:**
- **Imagem**: Camadas read-only empilhadas
- **Container**: Imagem + camada read-write temporária
- **Isolamento**: Cada container tem sua própria camada read-write
- **Temporariedade**: Dados são perdidos quando container é removido

**Problema da persistência:**
- Dados criados dentro do container são temporários
- Remoção do container = perda de dados
- Novos containers começam "limpos"
- Necessidade de soluções para persistir dados

**Soluções para persistência de dados:**

**1. Bind Mount:**
- Vincula diretório do host ao container
- Ponte entre sistema de arquivos do host e container
- Dados ficam no host e sobrevivem à remoção do container

**2. Volume:**
- Gerenciado pelo Docker
- Armazenamento otimizado para containers
- Independente do ciclo de vida do container

**3. tmpfs Mount:**
- Armazenamento temporário na memória
- Dados não persistem após reinicialização
- Útil para dados sensíveis temporários

**Exemplo prático de tamanhos:**
```bash
# Terminal 1: Criar container
docker run -it ubuntu bash

# Terminal 2: Verificar tamanho inicial
docker ps -s
# SIZE: 0B (virtual 72.8MB)

# Terminal 1: Fazer modificações
apt-get update
echo "dados" > arquivo.txt

# Terminal 2: Verificar novo tamanho
docker ps -s
# SIZE: ~32MB (virtual ~105MB)

# Sair e criar novo container
exit
docker run -it ubuntu bash

# Terminal 2: Verificar - novo container = tamanho zerado
docker ps -s
# SIZE: 0B (virtual 72.8MB)
```

---

### Bind mounts: persistindo dados com diretórios do host

**Criando um bind mount com -v:**
```bash
# Sintaxe: -v <caminho_no_host>:<caminho_no_container>
mkdir -p ~/volume-docker

docker run -it -v $HOME/volume-docker:/app ubuntu bash
# Dentro do container
cd /app
ls
touch arquivo-qualquer.txt
```
- Tudo que for criado em `/app` aparecerá no diretório `$HOME/volume-docker` do host.
- Ao sair (`exit`) e criar um novo container com o mesmo `-v`, os arquivos continuam lá (persistência).

**Criando um bind mount com --mount (recomendado):**
```bash
# Sintaxe: --mount type=bind,source=<host>,target=<container>
docker run -it \
  --mount type=bind,source=$HOME/volume-docker,target=/app \
  ubuntu bash
```
- `--mount` é mais explícito/semântico.
- Se o caminho no host não existir, o Docker acusa o erro (bom para evitar typos).

**Comparação entre -v e --mount:**

| Característica | -v | --mount |
|----------------|----|---------|
| **Sintaxe** | Compacta | Explícita |
| **Validação** | Cria diretório se não existir | Erro se diretório não existir |
| **Legibilidade** | Menos clara | Mais clara |
| **Recomendação** | Desenvolvimento rápido | Produção e scripts |

**Boas práticas:**
- Garanta que o diretório do host exista e tenha permissões adequadas
- Evite caminhos absolutos específicos em ambientes de produção
- Use variáveis de ambiente para tornar os caminhos configuráveis
- Prefira volumes gerenciados pelo Docker para maior portabilidade
- Bind mounts são ideais para desenvolvimento local e hot-reload

---

### Volumes: armazenamento gerenciado pelo Docker

**Por que usar volumes?**
- Área gerenciada pelo Docker dentro do sistema de arquivos
- Mais seguro que bind mounts (evita alterações indesejadas)
- Recomendado para ambientes produtivos
- Independente da estrutura de pastas do host

**Comandos básicos de volumes:**
```bash
docker volume ls                    # Lista volumes existentes
docker volume create meu-volume     # Cria um novo volume
docker volume inspect meu-volume    # Inspeciona detalhes do volume
docker volume prune                 # Remove volumes não utilizados
docker volume rm meu-volume         # Remove volume específico
```

**Criando e usando volumes com -v:**
```bash
# 1. Criar o volume
docker volume create meu-volume

# 2. Usar o volume em um container
docker run -it -v meu-volume:/app ubuntu bash

# Dentro do container:
cd /app
touch um-arquivo-qualquer
exit

# 3. Testar persistência
docker run -it -v meu-volume:/app ubuntu bash
cd /app && ls  # arquivo continua lá!
```

**Usando volumes com --mount (recomendado):**
```bash
# Volume existente
docker run -it --mount source=meu-volume,target=/app ubuntu bash

# Volume será criado automaticamente se não existir
docker run -it --mount source=meu-novo-volume,target=/app ubuntu bash
```

**Onde ficam os arquivos dos volumes:**
- Localização: `/var/lib/docker/volumes/<nome-do-volume>/_data`
- Acesso (como superusuário):
```bash
sudo su
cd /var/lib/docker/volumes/meu-volume/_data
ls  # seus arquivos estarão aqui
```

**Vantagens dos volumes vs bind mounts:**

| Volumes | Bind Mounts |
|---------|-------------|
| Gerenciados pelo Docker | Dependem do filesystem do host |
| Criados automaticamente | Requer caminhos específicos |
| Comandos Docker para gestão | Gestão manual do host |
| Mais seguros | Acesso direto ao sistema |
| Recomendado para produção | Útil para desenvolvimento |

**Comandos de gerenciamento:**
```bash
docker volume                       # Mostra todos os comandos disponíveis
docker volume ls                    # Lista volumes
docker volume inspect <nome>        # Detalhes do volume
docker volume prune                 # Remove volumes órfãos
docker volume rm <nome>             # Remove volume específico
```

**Exemplo prático completo:**
```bash
# 1. Verificar volumes existentes
docker volume ls

# 2. Criar aplicação com volume
docker run -d --name app-com-volume \
  --mount source=dados-app,target=/dados \
  ubuntu sleep 1d

# 3. Adicionar dados
docker exec app-com-volume touch /dados/arquivo-importante.txt

# 4. Remover container
docker rm -f app-com-volume

# 5. Criar novo container - dados persistem!
docker run -it --mount source=dados-app,target=/dados ubuntu bash
ls /dados  # arquivo-importante.txt ainda está lá
```

**Características importantes:**
- **Criação automática**: Volumes são criados automaticamente se não existirem
- **Portabilidade**: Funcionam igual em diferentes sistemas operacionais
- **Performance**: Otimizados para containers
- **Backup**: Mais fácil de fazer backup que bind mounts

---

### tmpfs: armazenamento temporário em memória

**O que é tmpfs?**
- Terceiro tipo de persistência disponível no Docker
- Armazena dados diretamente na memória do host
- **Limitação**: Funciona apenas em hosts Linux
- Dados são completamente temporários e voláteis

**Como funciona:**
- Dados são escritos na memória RAM, não no disco
- Não utiliza a camada read-write do container
- Tudo é perdido quando o container para
- Ideal para dados sensíveis que não devem persistir

**Criando tmpfs com --tmpfs:**
```bash
# Criar container com tmpfs
docker run -it --tmpfs=/app ubuntu bash

# Dentro do container:
cd /app
ls  # pasta "app" aparece destacada (temporária)
touch um-arquivo-qualquer
ls app  # arquivo está lá

# Sair e criar novo container
exit
docker run -it --tmpfs=/app ubuntu bash
ls app  # vazio! dados foram perdidos
```

**Criando tmpfs com --mount (recomendado):**
```bash
# Sintaxe: --mount type=tmpfs,destination=<caminho>
docker run -it --mount type=tmpfs,destination=/app ubuntu bash

# Dentro do container:
cd /app
touch um-arquivo-qualquer
ls  # arquivo existe

# Sair e criar novo container
exit
docker run -it --mount type=tmpfs,destination=/app ubuntu bash
ls app  # vazio novamente
```

**Quando usar tmpfs:**
- **Dados sensíveis**: Senhas, tokens, chaves temporárias
- **Segurança**: Informações que não devem ser gravadas em disco
- **Performance**: Operações que precisam de alta velocidade de I/O
- **Temporariedade**: Dados que só fazem sentido durante a execução

**Características importantes:**
- **Volátil**: Dados são perdidos ao parar o container
- **Memória RAM**: Escrita direta na memória do host
- **Sem persistência**: Nenhum dado sobrevive entre execuções
- **Linux only**: Não funciona em Windows ou macOS

**Comparação dos três tipos de persistência:**

| Tipo | Localização | Persistência | Uso recomendado |
|------|-------------|--------------|-----------------|
| **Volume** | Gerenciado pelo Docker | Sim | Produção, dados importantes |
| **Bind Mount** | Sistema de arquivos do host | Sim | Desenvolvimento, compartilhamento |
| **tmpfs** | Memória RAM do host | Não | Dados temporários e sensíveis |

**Exemplo prático - dados sensíveis:**
```bash
# Container com arquivo de senha temporário
docker run -it --tmpfs=/secrets ubuntu bash

# Dentro do container:
echo "senha-super-secreta" > /secrets/password.txt
cat /secrets/password.txt  # senha está lá

# Sair do container
exit

# Criar novo container
docker run -it --tmpfs=/secrets ubuntu bash
ls /secrets  # vazio! senha não foi persistida (segurança)
```

**Vantagens do tmpfs:**
- **Segurança**: Dados sensíveis não ficam gravados em disco
- **Performance**: Leitura/escrita em memória é muito mais rápida
- **Limpeza automática**: Não deixa rastros após a execução

**Desvantagens do tmpfs:**
- **Volatilidade**: Perda total de dados ao parar o container
- **Limitação de memória**: Consome RAM do host
- **Linux only**: Não portável para outros sistemas operacionais

---

## 🌐 Redes Docker: comunicação entre containers

**Por que redes são importantes?**
- Containers são isolados por padrão através de namespaces
- Sistemas complexos precisam de comunicação entre containers
- Aplicações back-end precisam se conectar a bancos de dados
- Microserviços precisam se comunicar entre si

**Rede padrão: bridge**

Quando criamos um container sem especificar rede, o Docker automaticamente o conecta à rede `bridge`:

```bash
# Criar container sem especificar rede
docker run -it ubuntu bash

# Em outro terminal, inspecionar o container
docker ps
docker inspect <CONTAINER_ID>
```

Na saída do `inspect`, na seção `Networks`, você verá que o container está conectado à rede `bridge`.

**Listando redes Docker:**
```bash
docker network ls
```

**Redes padrão do Docker:**

| Nome | Driver | Escopo | Descrição |
|------|--------|--------|-----------|
| **bridge** | bridge | local | Rede padrão para containers |
| **host** | host | local | Remove isolamento de rede (usa rede do host) |
| **none** | null | local | Container sem interface de rede |

**Inspecionando redes:**
```bash
# Ver detalhes de uma rede específica
docker network inspect bridge

# Ver configurações de rede de um container
docker inspect <CONTAINER_ID> | grep -A 20 Networks
```

**Comunicação entre containers na mesma rede:**

Containers na mesma rede podem se comunicar via IP:

```bash
# Terminal 1: Criar primeiro container
docker run -it --name container1 ubuntu bash

# Terminal 2: Criar segundo container
docker run -it --name container2 ubuntu bash

# Terminal 3: Verificar IPs dos containers
docker inspect container1 | grep IPAddress
docker inspect container2 | grep IPAddress

# Dentro do container2, instalar ping
apt-get update
apt-get install iputils-ping -y

# Testar comunicação via IP
ping <IP_DO_CONTAINER1>  # Exemplo: ping 172.17.0.2
```

**Problemas da comunicação via IP:**
- **Instabilidade**: IPs podem mudar quando containers são recriados
- **Não confiável**: Containers reiniciados podem receber IPs diferentes
- **Difícil manutenção**: Hardcoding de IPs é uma má prática
- **Falta de DNS**: Não há resolução de nomes por padrão na rede bridge

**Solução: Criar redes customizadas (User-Defined Bridge)**

Para estabelecer comunicação estável entre containers, precisamos de dois elementos:
1. **Nomes definidos** para os containers (não usar nomes aleatórios)
2. **Rede customizada** criada por nós (não usar a bridge padrão)

**Por que não basta apenas nomear os containers?**
- Nomes definidos com `--name` são mais estáveis que IPs
- Mas a rede bridge padrão **não oferece resolução DNS automática**
- Precisamos criar nossa própria rede bridge (user-defined bridge)

> ** Importante:** Apenas nomear containers com `--name` não é suficiente! A rede bridge padrão não resolve nomes automaticamente. Você **deve** criar uma rede customizada para ter resolução DNS funcionando.

**Passo a passo completo:**

```bash
# 1. Limpar containers existentes (opcional)
docker container rm $(docker container ps -aq) --force

# 2. Criar uma rede bridge customizada
docker network create --driver bridge minha-bridge

# 3. Verificar que a rede foi criada
docker network ls
# Agora você verá: bridge (padrão), host, none, e minha-bridge

# 4. Criar primeiro container com nome e rede definidos
docker run -it --name ubuntu1 --network minha-bridge ubuntu bash

# 5. Em outro terminal, criar segundo container na mesma rede
docker run -d --name pong --network minha-bridge ubuntu sleep 1d

# 6. Verificar que ambos estão na mesma rede customizada
docker inspect ubuntu1 | grep -A 10 Networks
docker inspect pong | grep -A 10 Networks

# 7. Dentro do container ubuntu1, instalar ping
apt-get update
apt-get install iputils-ping -y

# 8. Testar comunicação via hostname (não via IP!)
ping pong  # Funciona! Resolve automaticamente para o IP do container
```

**O que acontece nos bastidores:**
- A rede `minha-bridge` provê **resolução automática de DNS**
- Quando você faz `ping pong`, o Docker resolve para o IP correto (ex: 172.19.0.3)
- Mesmo que o IP mude, o nome `pong` sempre funcionará
- Isso é exclusivo de **user-defined bridges** (não funciona na bridge padrão)

**Exemplo prático com nomes descritivos:**
```bash
# Criar rede para aplicação
docker network create --driver bridge app-network

# Container do banco de dados
docker run -d \
  --name postgres-db \
  --network app-network \
  -e POSTGRES_PASSWORD=senha123 \
  postgres

# Container da aplicação
docker run -d \
  --name backend-api \
  --network app-network \
  -p 8080:8080 \
  minha-api

# A aplicação se conecta ao banco usando: postgres-db:5432
# Não precisa saber o IP, apenas o nome!
```

**Comandos essenciais de rede:**
```bash
docker network ls                          # Lista todas as redes
docker network create <nome>               # Cria uma nova rede
docker network inspect <nome>              # Inspeciona detalhes da rede
docker network connect <rede> <container>  # Conecta container a uma rede
docker network disconnect <rede> <container> # Desconecta container de uma rede
docker network rm <nome>                   # Remove uma rede
docker network prune                       # Remove redes não utilizadas
```

**Criando redes com drivers específicos:**
```bash
# Rede bridge customizada (padrão)
docker network create --driver bridge minha-rede

# Rede com subnet específica
docker network create --subnet=192.168.0.0/16 minha-rede-custom
```

**Conectando containers existentes a redes:**
```bash
# Criar container sem rede específica
docker run -d --name app nginx

# Conectar a uma rede existente
docker network connect minha-rede app

# Desconectar de uma rede
docker network disconnect bridge app
```

**Exemplo prático: Aplicação com banco de dados**
```bash
# 1. Criar rede para a aplicação
docker network create app-network

# 2. Criar container do banco de dados
docker run -d \
  --name postgres-db \
  --network app-network \
  -e POSTGRES_PASSWORD=senha123 \
  postgres

# 3. Criar container da aplicação
docker run -d \
  --name minha-app \
  --network app-network \
  -p 8080:8080 \
  minha-imagem

# 4. A aplicação pode se conectar ao banco usando: postgres-db:5432
```

**Vantagens de redes customizadas:**
- **Resolução DNS automática**: Containers se comunicam por nome
- **Isolamento**: Apenas containers na mesma rede podem se comunicar
- **Segurança**: Melhor controle sobre quem se comunica com quem
- **Flexibilidade**: Containers podem estar em múltiplas redes
- **Estabilidade**: Nomes não mudam, mesmo se IPs mudarem

**Diferenças entre rede bridge padrão e customizada:**

| Característica | Bridge Padrão | Bridge Customizada |
|----------------|---------------|-------------------|
| **Resolução DNS** | Não | Sim (por nome do container) |
| **Isolamento** | Todos os containers | Apenas containers conectados |
| **Comunicação** | Apenas via IP | Via nome ou IP |
| **Recomendação** | Testes rápidos | Produção e aplicações reais |

**Boas práticas com redes:**
- Sempre use redes customizadas em produção
- Nomeie containers de forma descritiva para facilitar DNS
- Crie redes separadas para diferentes aplicações
- Use `--network` ao criar containers, não conecte depois
- Documente as redes utilizadas no seu projeto

**Entendendo os tipos de rede do Docker:**

**1. Bridge (padrão):**
- Rede padrão quando você não especifica `--network`
- Containers podem se comunicar via IP
- **Não oferece resolução DNS automática**
- Adequada apenas para testes rápidos

**2. User-Defined Bridge (recomendada):**
- Criada com `docker network create --driver bridge`
- **Oferece resolução DNS automática** entre containers
- Melhor isolamento e controle
- Recomendada para produção

**3. None (driver null):**
- Container **sem nenhuma interface de rede**
- Completamente isolado em termos de rede
- Não consegue realizar operações que envolvam rede
- Máxima segurança para processamento local

**Quando usar:**
- Processamento de dados que não requer rede
- Tarefas batch isoladas
- Máxima segurança (sem acesso externo)
- Testes de aplicações sem dependências de rede

**Exemplo prático:**
```bash
# Criar container sem rede
docker run -d --network none ubuntu sleep 1d

# Inspecionar o container
docker ps
docker inspect <CONTAINER_ID>
# Na seção Networks, você verá que está usando "none"

# Testar dentro do container
docker exec -it <CONTAINER_ID> bash
ping google.com  # Não funciona! Sem interface de rede
curl google.com  # Não funciona! Sem conectividade
```

**Características da rede none:**
- Sem interface de rede
- Sem conectividade externa
- Sem comunicação com outros containers
- Isolamento total
- Máxima segurança

**4. Host:**
- Remove **todo o isolamento de rede** entre container e host
- Container usa **diretamente a interface de rede do host**
- **Não precisa mapear portas** (acessa portas do host diretamente)
- Útil para performance máxima, mas menos seguro

**Quando usar:**
- Performance crítica de rede
- Aplicações que precisam acessar muitas portas
- Ferramentas de monitoramento de rede
- Ambientes onde mapeamento de portas é limitante

**Exemplo prático:**
```bash
# Verificar redes disponíveis
docker network ls

# Executar aplicação na rede host
docker run -d --network host aluradocker/app-node:1.0

# Inspecionar o container
docker inspect <CONTAINER_ID>
# Na seção Networks, você verá que está usando "host"

# Acessar a aplicação SEM mapeamento de portas
# Se a aplicação roda na porta 3000, acesse:
# http://localhost:3000
# Funciona! Mesmo sem usar -p 3000:3000
```

**O que acontece com a rede host:**
- Container compartilha **a mesma interface de rede do host**
- Aplicação na porta 3000 do container = porta 3000 do host
- **Não há isolamento de rede**
- Se a porta já estiver em uso no host, haverá **conflito**

**Exemplo de conflito de portas:**
```bash
# Se você já tem algo rodando na porta 3000 do host
# E tentar executar:
docker run -d --network host aluradocker/app-node:1.0
# O container não conseguirá iniciar corretamente
# Erro: porta 3000 já está em uso!
```

**Vantagens e desvantagens:**

| Aspecto | None | Host |
|---------|------|------|
| **Isolamento** | Total | Nenhum |
| **Performance** | N/A | Máxima |
| **Segurança** | Máxima | Mínima |
| **Mapeamento de portas** | N/A | Não necessário |
| **Uso recomendado** | Processamento isolado | Performance crítica |
| **Conflitos** | Nenhum | Possível (portas) |

**Resumo comparativo dos 4 tipos de rede:**

| Tipo de Rede | Quando Usar | Resolução DNS | Isolamento | Mapeamento de Portas |
|--------------|-------------|---------------|------------|---------------------|
| **Bridge (padrão)** | Testes rápidos | Não | Médio | Sim |
| **User-Defined Bridge** | Produção, aplicações reais | Sim | Alto | Sim |
| **Host** | Performance crítica | N/A | Nenhum | Não |
| **None** | Processamento isolado | N/A | Total | N/A |

**Decisão rápida: Qual rede usar?**

```
Precisa de comunicação entre containers?
├─ Sim → Use User-Defined Bridge (--network minha-rede)
│         • Resolução DNS automática
│         • Isolamento entre aplicações
│         • Recomendado para produção
│
├─ Não, mas precisa de acesso externo → Use Bridge padrão
│         • Apenas para testes rápidos
│         • Comunicação só via IP
│
├─ Precisa de máxima performance de rede? → Use Host
│         • Sem isolamento
│         • Cuidado com conflitos de porta
│
└─ Não precisa de rede? → Use None
          • Máximo isolamento
          • Máxima segurança
```

**Referências oficiais:**
- A documentação oficial do Docker recomenda usar **user-defined bridges** para produção
- [Docker Bridge Networks](https://docs.docker.com/network/bridge/)
- [Docker Network Drivers](https://docs.docker.com/network/drivers/)

---

### Comunicação entre containers na prática: Aplicação + Banco de dados

**Cenário real:**
Vamos criar uma aplicação completa com:
- **Back-end**: Aplicação Node.js (alura-books)
- **Banco de dados**: MongoDB
- **Comunicação**: Via rede customizada usando hostnames

**Preparação - Baixar as imagens:**
```bash
# Baixar imagem do MongoDB (versão específica)
docker pull mongo:4.4.6

# Baixar imagem da aplicação
docker pull aluradocker/alura-books:1.0

# Verificar imagens baixadas
docker images
```

**Passo 1: Criar a rede customizada**
```bash
# Verificar redes existentes
docker network ls

# Criar rede bridge customizada (se ainda não existir)
docker network create --driver bridge minha-bridge
```

**Passo 2: Executar o container do banco de dados**
```bash
docker run -d \
  --network minha-bridge \
  --name meu-mongo \
  mongo:4.4.6
```

**Por que o nome `meu-mongo` é importante?**
- A aplicação `alura-books` está configurada para buscar o banco pelo hostname `meu-mongo`
- No arquivo de configuração da aplicação, o host do banco está definido como `meu-mongo`
- Por isso, o nome do container **deve ser exatamente** `meu-mongo`

**Passo 3: Executar o container da aplicação**
```bash
docker run -d \
  --network minha-bridge \
  --name alurabooks \
  -p 3000:3000 \
  aluradocker/alura-books:1.0
```

**Detalhes importantes:**
- `--network minha-bridge`: Mesma rede do banco (obrigatório para comunicação)
- `--name alurabooks`: Nome do container (pode ser qualquer um)
- `-p 3000:3000`: Mapeamento de porta (necessário porque não estamos usando rede host)

**Passo 4: Testar a aplicação**
```bash
# Verificar containers em execução
docker ps

# Acessar no navegador:
# http://localhost:3000

# Popular o banco de dados (endpoint especial)
# http://localhost:3000/seed

# Atualizar a página para ver os dados carregados
```

**Como funciona a comunicação:**
```
┌─────────────────────┐         ┌──────────────────────┐
│   alurabooks        │         │     meu-mongo        │
│   (aplicação)       │────────▶│   (MongoDB)          │
│   porta: 3000       │         │   porta: 27017       │
└─────────────────────┘         └──────────────────────┘
         │                               │
         └───────────────────────────────┘
              minha-bridge (rede)
              Resolução DNS automática
```

**Testando a persistência:**
```bash
# Parar o container do MongoDB
docker stop meu-mongo

# Acessar http://localhost:3000
# Os dados desaparecem (sem conexão com banco)

# Reiniciar o MongoDB
docker start meu-mongo

# Popular novamente: http://localhost:3000/seed
# Atualizar a página - dados voltam!
```

**Fluxo completo de comandos:**
```bash
# 1. Preparação
docker pull mongo:4.4.6
docker pull aluradocker/alura-books:1.0
docker network create --driver bridge minha-bridge

# 2. Iniciar banco de dados
docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6

# 3. Iniciar aplicação
docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0

# 4. Verificar
docker ps

# 5. Acessar no navegador
# http://localhost:3000/seed (popular banco)
# http://localhost:3000 (ver dados)
```

**Verificando a comunicação:**
```bash
# Entrar no container da aplicação
docker exec -it alurabooks bash

# Dentro do container, testar conectividade
ping meu-mongo  # Resolve para o IP do container MongoDB
# Funciona! DNS automático da rede customizada

# Sair do container
exit
```

**Pontos importantes deste exemplo:**

1. **Mesma rede é obrigatória**
   - Ambos containers devem estar na mesma rede customizada
   - Sem isso, não há comunicação entre eles

2. **Nome do banco é crítico**
   - A aplicação busca pelo hostname `meu-mongo`
   - O nome do container deve corresponder ao configurado na aplicação

3. **Mapeamento de portas**
   - Necessário para acessar a aplicação externamente
   - O banco não precisa de mapeamento (comunicação interna)

4. **Ordem de inicialização**
   - Banco deve estar rodando antes da aplicação
   - Aplicação precisa do banco disponível para conectar

**Limitações da abordagem manual:**

- Precisamos iniciar containers manualmente (um por um)
- Ordem de inicialização deve ser respeitada
- Configurações podem ser esquecidas
- Difícil de gerenciar múltiplos containers
- Não é prático para ambientes de produção

> **Nota:** Em ambientes reais, usamos ferramentas como **Docker Compose** para orquestrar múltiplos containers automaticamente, definindo toda a configuração em um arquivo YAML.

---

## 🐝 Docker Compose: coordenando múltiplos containers

**O problema da abordagem manual:**

Quando trabalhamos com múltiplos containers, a abordagem manual apresenta diversos desafios:

```bash
# Precisamos executar cada container manualmente
docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6
docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0

# Para parar, precisamos parar cada um individualmente
docker stop meu-mongo
docker stop alurabooks

# Para remover, o mesmo processo
docker rm meu-mongo
docker rm alurabooks
```

**Problemas:**
- Comandos longos e complexos
- Ordem de execução deve ser respeitada
- Difícil de gerenciar quando a aplicação cresce
- Propenso a erros (esquecer parâmetros, nomes, portas)
- Não é escalável para ambientes com muitos containers

**A solução: Docker Compose**

Docker Compose é uma ferramenta de **coordenação de containers** (diferente de orquestração) que permite:
- Definir múltiplos containers em um único arquivo YAML
- Gerenciar todo o ambiente com comandos simples
- Garantir ordem de inicialização
- Facilitar compartilhamento de configurações

**Coordenação vs Orquestração:**
- **Coordenação (Compose)**: Gerencia containers em um único host
- **Orquestração (Kubernetes, Swarm)**: Gerencia containers em múltiplos hosts/clusters

**Como funciona:**
- Arquivo `docker-compose.yml` define toda a configuração
- Versão do Compose, serviços, redes, volumes
- Estrutura baseada em indentação (YAML)
- Um único comando para subir/parar toda a aplicação

---

### Instalando o Docker Compose

**No Windows:**
- Docker Compose já vem instalado com o Docker Desktop
- Não é necessário instalação adicional
- Teste: `docker-compose --version`

**No Linux:**

**Verificar se já está instalado:**
```bash
docker-compose --version
# Se não estiver instalado, o comando não será reconhecido
```

**Instalação via documentação oficial:**

**Passo 1: Baixar o binário**
```bash
# Baixar a versão mais recente (substitua pela versão atual se necessário)
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

**Passo 2: Tornar executável**
```bash
# Dar permissão de execução
sudo chmod +x /usr/local/bin/docker-compose
```

**Passo 3: Verificar instalação**
```bash
# Abrir novo terminal e testar
docker-compose --version
# Saída esperada: docker-compose version 1.29.2, build...
```

**Comandos básicos do Docker Compose:**
```bash
docker-compose --version              # Verifica versão instalada
docker-compose --help                 # Lista todos os comandos disponíveis
docker-compose up                     # Sobe todos os containers
docker-compose down                   # Para e remove todos os containers
docker-compose ps                     # Lista containers do compose
docker-compose logs                   # Mostra logs de todos os containers
```

**Instalação alternativa (não recomendada):**
```bash
# Via apt (pode instalar versão desatualizada)
sudo apt install docker-compose

# Via snap
sudo snap install docker-compose
```

> **Recomendação:** Sempre use a instalação via documentação oficial para obter a versão mais recente e estável.

**Links úteis:**
- [Documentação oficial - Install Docker Compose](https://docs.docker.com/compose/install/)
- [Releases do Docker Compose no GitHub](https://github.com/docker/compose/releases)

**Próximos passos:**

Agora que temos o Docker Compose instalado, vamos transformar nossa aplicação manual (alura-books + MongoDB) em um arquivo `docker-compose.yml`, simplificando todo o processo de gerenciamento.

---

## 📝 Resumo dos conceitos principais

**[Docker Hub](https://hub.docker.com/):**
- Repositório central de imagens Docker
- Fonte principal para baixar imagens oficiais e da comunidade

**Ciclo de vida dos containers:**
- `docker start`: Inicia container parado
- `docker stop`: Para container em execução
- `docker pause`: Pausa container (preserva estado)
- `docker unpause`: Despausa container pausado
- `docker rm`: Remove o container

**Mapeamento de portas:**
- `-P`: Mapeamento automático para portas aleatórias
- `-p`: Mapeamento específico (host:container)
- Essencial para acessar aplicações web externamente

**Isolamento:**
- Containers são completamente isolados do host
- Cada container possui seu próprio sistema de arquivos
- Modificações não afetam o host ou outros containers

**Persistência de dados:**
- **Volumes**: Solução recomendada, gerenciada pelo Docker
- **Bind Mounts**: Vincula diretórios do host ao container
- **tmpfs**: Armazenamento temporário em memória (Linux only)

**Redes Docker:**
- **bridge**: Rede padrão, comunicação via IP
- **Redes customizadas**: Resolução DNS automática por nome
- **Isolamento**: Containers só se comunicam na mesma rede
- **Comandos**: `docker network create/ls/inspect/connect`

---

## ✅ Boas práticas gerais

**Segurança:**
- Não execute containers como root quando possível
- Use imagens oficiais e verificadas do Docker Hub
- Mantenha imagens atualizadas para evitar vulnerabilidades
- Não armazene dados sensíveis em imagens

**Performance:**
- Use `.dockerignore` para excluir arquivos desnecessários
- Minimize o número de camadas no Dockerfile
- Agrupe comandos `RUN` quando possível
- Use volumes para dados que mudam frequentemente

**Organização:**
- Nomeie containers e volumes de forma descritiva
- Use tags semânticas para versionamento de imagens
- Documente portas expostas com `EXPOSE`
- Mantenha Dockerfiles simples e legíveis

**Manutenção:**
- Execute `docker system prune` periodicamente
- Remova containers e imagens não utilizados
- Monitore o uso de espaço em disco
- Faça backup de volumes importantes

**Desenvolvimento:**
- Use bind mounts para hot-reload durante desenvolvimento
- Separe ambientes de desenvolvimento e produção
- Utilize variáveis de ambiente para configurações
- Teste imagens localmente antes de fazer push

**Redes:**
- Sempre use redes customizadas em produção
- Evite comunicação via IP, prefira nomes de containers
- Crie redes separadas para isolar diferentes aplicações
- Use `--network` ao criar containers para melhor organização
- Documente as dependências de rede entre containers

---

## ⚡ Referência rápida de comandos

**Imagens:**
```bash
docker images                          # Listar imagens
docker pull <imagem>                   # Baixar imagem
docker build -t <nome:tag> .           # Construir imagem
docker rmi <image_id>                  # Remover imagem
docker tag <origem> <destino>          # Criar tag
docker push <repositorio:tag>          # Enviar para Docker Hub
```

**Containers:**
```bash
docker ps                              # Listar containers ativos
docker ps -a                           # Listar todos os containers
docker run -d -p 8080:80 <imagem>      # Criar e executar container
docker start <container_id>            # Iniciar container
docker stop <container_id>             # Parar container
docker restart <container_id>          # Reiniciar container
docker rm <container_id>               # Remover container
docker exec -it <container_id> bash    # Acessar terminal do container
docker logs <container_id>             # Ver logs do container
```

**Volumes:**
```bash
docker volume ls                       # Listar volumes
docker volume create <nome>            # Criar volume
docker volume inspect <nome>           # Inspecionar volume
docker volume rm <nome>                # Remover volume
docker volume prune                    # Remover volumes não utilizados
```

**Redes:**
```bash
docker network ls                      # Listar redes
docker network create <nome>           # Criar rede
docker network inspect <nome>          # Inspecionar rede
docker network connect <rede> <container> # Conectar container à rede
docker network disconnect <rede> <container> # Desconectar container
docker network rm <nome>               # Remover rede
docker network prune                   # Remover redes não utilizadas
```

**Limpeza:**
```bash
docker container prune                 # Remover containers parados
docker image prune                     # Remover imagens não utilizadas
docker volume prune                    # Remover volumes não utilizados
docker system prune -a                 # Limpeza completa do sistema
```

**Inspeção:**
```bash
docker inspect <container/image_id>    # Informações detalhadas
docker history <image_id>              # Histórico de camadas
docker stats                           # Estatísticas de uso em tempo real
docker top <container_id>              # Processos em execução no container
```

---

## 🔗 Recursos adicionais

**Documentação oficial:**
- [Docker Documentation](https://docs.docker.com/) - Documentação completa
- [Docker Hub](https://hub.docker.com/) - Repositório de imagens
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/) - Referência do Dockerfile
- [Docker Compose](https://docs.docker.com/compose/) - Guia do Docker Compose
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/) - Melhores práticas

**Tutoriais e cursos:**
- [Docker Get Started](https://docs.docker.com/get-started/) - Tutorial oficial
- [Play with Docker](https://labs.play-with-docker.com/) - Ambiente de testes online
- [Docker Curriculum](https://docker-curriculum.com/) - Tutorial interativo

**Comunidade:**
- [Docker Community Forums](https://forums.docker.com/)
- [Stack Overflow - Docker](https://stackoverflow.com/questions/tagged/docker)
- [Reddit - r/docker](https://www.reddit.com/r/docker/)

---

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

---

## 👤 Autor

**Gabriel**
- GitHub: [@rouri404](https://github.com/rouri404)
- Docker Hub: [gabricoto](https://hub.docker.com/u/gabricoto)

---

## ⭐ Agradecimentos

- [Alura](https://www.alura.com.br/) - Pela excelente plataforma de ensino
- Comunidade Docker - Pela documentação e suporte
- Todos que contribuíram com feedback e melhorias

---

<div align="center">

**[⬆ Voltar ao topo](#docker-criando-e-gerenciando-containers)**

Feito com 💙 por [Gabriel](https://github.com/rouri404)

</div>
