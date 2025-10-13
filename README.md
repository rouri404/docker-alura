## Repositório de estudo do curso
[`Docker: criando e gerenciando containers`](https://www.alura.com.br/course/docker-criando-gerenciando-containers)

### Instalar Docker no Arch Linux e derivados

```bash
sudo pacman -Syu                        # Atualiza o sistema.
sudo pacman -S docker                   # Instala o Docker Engine.
sudo systemctl start docker.service     # Inicia o serviço do Docker.
sudo systemctl enable docker.service    # Habilita o Docker para iniciar com o sistema.
sudo usermod -aG docker $USER           # Adiciona seu usuário ao grupo do Docker.
sudo docker run hello-world             # Verifica se a instalação foi bem-sucedida.
```