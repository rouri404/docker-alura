<center>

### [`Docker: criando e gerenciando containers`](https://www.alura.com.br/course/docker-criando-gerenciando-containers)

</center>

Repositório criado para fins de estudo sobre ***Docker***, curso realizado na plataforma *Alura*.

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

#### Comandos relevantes

```bash
docker ps                               # Mostra quais containers estão em execução no momento.
docker container ls                     # Exatamente a mesma coisa da anterior, só que mais verboso.
docker ps -a                            # Mostra todos os containers, inclusive os que já não estão mais em execução.
docker container ls -a                  # Exatamente a mesma coisa da anterior, só que mais verboso.
```
> *Nota: Ao executarmos o comando docker run --help, observamos que ao especificar a imagem, podemos enviar um comando para que esse container execute.*

```bash
docker run --help
> Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

---

#### Interrompendo e reiniciando os *containers*

Para interromper a execução dos *containers*, utilizaremos o comando `docker stop`, fornecendo a ele o **ID** ou o **nome** do *container*.

```bash
docker ps -a

> CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
> a1b2c3c4d5e6   hello-world   "/hello"   1 seconds ago   Exited (0) 1 seconds ago             hello_name
> b1c2d3d4e5f6   ubuntu        "bash"     1 hour ago      Exited (0) 1 hour ago                ubuntu_name
```

```bash
docker stop a1b2c3c4d5e6                  # Aqui foi interrompida a partir do ID
docker stop hello_name                    # Aqui foi interrompida a partir do nome
```

---

#### Interagindo com containers em execução

Para interagir com um *container* que está em execução, utilizamos o comando `docker exec`. Este comando permite executar comandos dentro do *container* de forma interativa.

```bash
docker exec -it <CONTAINER_ID> bash       # Acessa o terminal do container de forma interativa
docker exec -it <CONTAINER_ID> sh         # Alternativa ao bash para algumas imagens
```

**Parâmetros importantes:**
- `-i`: Mantém o STDIN aberto (interatividade)
- `-t`: Aloca um pseudo-TTY (terminal)
- `bash` ou `sh`: Comando a ser executado no *container*

**Exemplo prático:**
```bash
docker run -d ubuntu sleep 1d             # Cria um container em background
docker ps                                 # Lista containers para obter o ID
docker exec -it <CONTAINER_ID> bash       # Acessa o terminal do container
```

Dentro do *container*, você terá acesso completo ao sistema de arquivos isolado:
```bash
cd /home                                  # Navega para o diretório home
touch meu-arquivo.txt                     # Cria um arquivo
ls                                        # Lista arquivos criados
apt-get update                            # Atualiza pacotes (isolado do host)
```

---

#### Gerenciando o ciclo de vida dos *containers*

**Parando e reiniciando *containers*:**
```bash
docker stop <CONTAINER_ID>                # Para o container (aguarda 10 segundos)
docker stop -t=0 <CONTAINER_ID>           # Para o container instantaneamente
docker start <CONTAINER_ID>               # Reinicia um container parado
```

**Pausando e despausando *containers*:**
```bash
docker pause <CONTAINER_ID>               # Pausa o container (preserva estado)
docker unpause <CONTAINER_ID>             # Despausa o container
```

**Diferenças importantes:**
- `stop/start`: Mata todos os processos e reinicia do zero
- `pause/unpause`: Congela os processos mantendo o estado atual

**Removendo containers:**
```bash
docker rm <CONTAINER_ID>                  # Remove um container parado
docker rm -f <CONTAINER_ID>               # Remove um container forçadamente
```

---

#### Criando *containers* interativos

Para criar um *container* que já inicia em modo interativo:
```bash
docker run -it ubuntu bash                # Cria e acessa diretamente o terminal
```

**Características importantes:**
- O *container* será encerrado automaticamente quando você sair do terminal
- Todos os dados criados dentro do *container* são perdidos quando ele é removido
- Cada *container* possui seu próprio sistema de arquivos isolado

**Isolamento de containers:**
- Arquivos criados dentro de um *container* não aparecem no sistema host
- Cada *container* possui seu próprio namespace dos processos
- Modificações em um *container* não afetam outros containers ou o host

---

#### Executando *containers* em background

Para executar *containers* sem travar o terminal, utilizamos a flag `-d` (detached):

```bash
docker run -d ubuntu sleep 1d              # Executa em background
docker run -d <IMAGEM>                     # Executa qualquer imagem em background
```

**Vantagens do modo detached:**
- Terminal permanece disponível para outros comandos
- Container continua executando em segundo plano
- Ideal para aplicações que precisam ficar rodando

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

**Limpeza completa do sistema:**
```bash
# Remover todos os containers (parados e em execução)
docker container rm $(docker container ls -aq)

# Remover todas as imagens
docker rmi $(docker image ls -aq)

# Forçar remoção se houver conflitos
docker rmi $(docker image ls -aq) --force
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

**Limpeza inicial (opcional):**
```bash
# Remover todos os containers (forçado)
docker rm $(docker container ps -aq) --force
```

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

**Comparação rápida:**
- `-v host:container`: Atalho, menos verboso.
- `--mount type=bind,source=...,target=...`: Mais claro, valida melhor os caminhos.

**Boas práticas e cautelas:**
- Garanta que o diretório do host exista e tenha permissões adequadas.
- Evite acoplar sua imagem a caminhos específicos do host em produção.
- Para reduzir acoplamento e aumentar portabilidade, prefira Volumes gerenciados pelo Docker (ver próxima seção).

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
