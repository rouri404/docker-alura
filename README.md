<div align="center">

# [Docker: Criando e Gerenciando Containers](https://cursos.alura.com.br/course/docker-criando-gerenciando-containers/)

[![Curso](https://img.shields.io/badge/Alura-Docker-blue)](https://www.alura.com.br/course/docker-criando-gerenciando-containers)
[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)

Repositório criado para fins de estudo sobre **Docker**, curso realizado na plataforma **Alura**.

[Documentação Oficial](https://docs.docker.com/) • [Docker Hub](https://hub.docker.com/)

</div>

## Índice

- [Conceitos fundamentais](#conceitos-fundamentais)
- [Gerenciando imagens Docker](#gerenciando-imagens-docker)
- [Instalação do Docker](#instalar-docker-no-arch-linux-e-derivados)
- [Ciclo de vida dos containers](#ciclo-de-vida-dos-containers)
- [Mapeamento de portas](#mapeamento-de-portas)
- [Criando suas próprias imagens](#criando-suas-próprias-imagens-docker)
- [Enviando imagens para o Docker Hub](#enviando-imagens-para-o-docker-hub)
- [Persistência de dados](#tamanho-dos-containers-e-persistência-de-dados)
  - [Bind Mounts](#bind-mounts-persistindo-dados-com-diretórios-do-host)
  - [Volumes](#volumes-armazenamento-gerenciado-pelo-docker)
  - [tmpfs](#tmpfs-armazenamento-temporário-em-memória)
- [Resumo dos conceitos principais](#resumo-dos-conceitos-principais)
- [Boas práticas gerais](#boas-práticas-gerais)
- [Referência rápida de comandos](#referência-rápida-de-comandos)
- [Recursos adicionais](#recursos-adicionais)

---

#### Conceitos fundamentais

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

#### Gerenciando imagens *Docker*

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

#### Instalar Docker no Arch Linux e derivados

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

#### Ciclo de vida dos containers

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

#### Mapeamento de portas

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

#### Criando suas próprias imagens Docker

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

#### Enviando imagens para o Docker Hub

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

#### Tamanho dos containers e persistência de dados

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

#### Bind mounts: persistindo dados com diretórios do host

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

#### Volumes: armazenamento gerenciado pelo Docker

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

#### tmpfs: armazenamento temporário em memória

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

#### Resumo dos conceitos principais

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

---

#### Boas práticas gerais

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

---

#### Referência rápida de comandos

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

## Recursos adicionais

- **[Documentação oficial do Docker](https://docs.docker.com/)**
- **[Docker Hub](https://hub.docker.com/)** - Repositório de imagens
- **[Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)** - Referência completa do Dockerfile
- **[Docker Compose](https://docs.docker.com/compose/)** - Orquestração de múltiplos containers
- **[Best Practices](https://docs.docker.com/develop/dev-best-practices/)** - Melhores práticas oficiais
