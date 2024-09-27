# Comandos Úteis

## Listar Containers em Execução:

Use o seguinte comando para listar todos os containers que estão em execução:

```bash
docker ps
```

## Listar Todos os Containers

Para listar todos os containers criados (incluindo os que não estão em execução), utilize o seguinte comando:

```bash
docker ps -a
```

## Rodar Container

Para rodar um container que foi parado, use:

```bash
docker start [ID DO CONTAINER]
```

## Parar container

Copie o respectivo ID do container que está na coluna `CONTAINER ID`, depois use:

```bash
docker stop [ID DO CONTAINER]
```
