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
docker container ls -a                  # Mostra todos os containers, inclusive os que já não estão mais em execução.
```
> *Nota: Ao executarmos o comando docker run --help, observamos que ao especificar a imagem, podemos enviar um comando para que esse container execute.*

```bash
docker run --help
> Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
