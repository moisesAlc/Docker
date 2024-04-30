# Docker

## Exemplos Práticos de Volumes no Docker

### 1. Volumes

**Criação e Uso de um Volume**:
Você pode criar um volume usando o Docker CLI e então montá-lo em um container. Isso é útil para persistir dados de banco de dados, por exemplo.

```bash
# Criar um volume
docker volume create meu_volume

# Usar o volume em um container (exemplo com MySQL)
docker run -d --name meu_mysql -v meu_volume:/var/lib/mysql mysql
```

Esse comando cria um volume chamado `meu_volume` e depois inicia um container `mysql` usando este volume para armazenar os dados do banco de dados. Os dados persistirão entre as execuções do container.

#### Observações

- O volume `meu_volume` é **montado** especificamente no diretório `/var/lib/mysql` **dentro do container** `meu_mysql`. Isso significa que qualquer dado que o MySQL armazene nesse diretório será mantido no volume meu_volume, mesmo que o container seja parado, reiniciado, ou mesmo deletado. 

- ❗❗  Os dados fora dessa pasta (`/var/lib/mysql`) **NÃO SERÃO PERSISTIDOS APÓS A REMOÇÃO DO CONTAINER**, a menos que estejam localizados em outros volumes ou bind mounts definidos.

#### Utilização de múltiplos volumes:

    A utilização de múltiplos volumes em um único container no Docker é útil quando você deseja separar diferentes tipos de dados ou para melhorar a organização e a gestão dos dados que são gerados ou usados pelo container.

    Por exemplo, você pode querer ter um volume para dados de logs, outro para dados de banco de dados, e outro ainda para configurações persistentes. Cada um desses volumes pode ser montado em diferentes caminhos dentro do container.

##### Exemplo de Uso de Múltiplos Volumes

    Suponha que você esteja executando um container de banco de dados PostgreSQL e queira um volume para os dados do banco e outro para os logs:

```bash
docker volume create pg_data
docker volume create pg_logs

docker run -d \
    --name meu_postgres \
    -v pg_data:/var/lib/postgresql/data \
    -v pg_logs:/var/log/postgresql \
    postgres
```
Neste exemplo, pg_data é montado em /var/lib/postgresql/data para os dados do banco de dados, enquanto pg_logs é montado em /var/log/postgresql para os arquivos de log. Isso não só ajuda na organização dos dados, mas também facilita tarefas como backup e recuperação, pois você pode gerenciar os volumes de maneira independente.

### 2. Bind Mounts

**Criar e Usar um Bind Mount**:
Bind mounts são úteis para desenvolvimento, onde você pode querer editar seu código no host e ver as mudanças refletidas no container instantaneamente.

```bash
# Usar um bind mount para montar o diretório atual no /app dentro do container
docker run -d --name meu_app -v $(pwd):/app python python /app/meu_script.py
```

Este comando monta o diretório atual do host (`$(pwd)`) no diretório `/app` dentro do container que roda um script Python. Qualquer alteração feita nos arquivos do diretório atual será imediatamente refletida dentro do container.

### 3. tmpfs Mounts

**Criar e Usar um tmpfs Mount**: 
Os tmpfs mounts são usados quando você quer que os dados sejam escritos em memória (não persistidos no disco), o que é útil para dados temporários ou sensíveis.

```bash
# Criar um container com um tmpfs mount no /app
docker run -d --name meu_app_temp --tmpfs /app alpine
```

Este comando cria um container `alpine` com um diretório `/app` montado como tmpfs. Todos os dados escritos em `/app` serão armazenados na memória e não serão persistidos após o container ser parado, o que é ideal para processamento de dados temporários ou sensíveis.