### [`Docker: criando e gerenciando containers`](https://www.alura.com.br/course/docker-criando-gerenciando-containers)

Repositório criado para fins de estudo sobre Docker, curso realizado na plataforma *Alura*.

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
> *Nota: Caso use outra distribuição, como `Ubuntu/Debian` ou `Fedora` recomendo ver na [documentação oficial do Docker](https://docs.docker.com/engine/install/).*

---

#### Comandos relavates

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