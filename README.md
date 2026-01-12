## Minecraft Server Infrastructure (Docker + Paper)

## Описание

Данный проект представляет собой устойчивую и воспроизводимую инфраструктуру Minecraft-сервера, развернутого на VPS с использованием Docker.

Цель проекта — не просто запустить сервер, а выстроить архитектуру с:
- разделением ответственности
- контролируемыми изменениями
- прозрачной структурой
- возможностью командной работы

Minecraft рассматривается как сервис, а не как отдельный jar-файл.

---

## Архитектурный подход

Инфраструктура разделена на логические слои:

1. Операционная система (Ubuntu 24.04)
2. Docker как среда выполнения
3. Контейнер Minecraft
4. Volume с игровыми данными
5. Контент (мир, плагины)
6. Пользовательские роли

Каждый слой имеет свою зону ответственности и не пересекается с другими без необходимости.

---

## Технологии

- OS: Ubuntu 24.04
- Docker
- Docker Compose (v1)
- Docker Image: `itzg/minecraft-server:java21`
- Minecraft Core: Paper 1.21.11
- Управление сессиями: tmux

---

## Структура проекта
/home/minecraft/minecraft-server
├── docker-compose.yml
├── data/
│ ├── world/
│ ├── plugins/
│ ├── logs/
│ ├── server.properties
│ └── paper*.yml
├── logs/
├── scripts/
└── CONTRACT.md


### Разделение ответственности

- `docker-compose.yml` — инфраструктура и конфигурация сервиса
- `data/` — игровая среда Minecraft
- `plugins/`, `world/` — зона работы с контентом
- jar-файлы ядра не редактируются вручную

---

## Пользовательские роли

### root
- управление системой
- Docker
- безопасность
- SSH

### minecraft
- владелец сервиса
- хранение данных
- запуск контейнеров

### world_designer
- работа с контентом:
  - `data/plugins`
  - `data/world`
- не имеет доступа к Docker и системным настройкам

---

## Docker Compose

`docker-compose.yml` является единственным источником истины для конфигурации сервера.

Пример рабочей конфигурации:

```yaml
version: "3.9"

services:
  minecraft:
    image: itzg/minecraft-server:java21
    container_name: minecraft
    restart: unless-stopped

    ports:
      - "25565:25565"

    environment:
      EULA: "TRUE"
      TYPE: PAPER
      VERSION: 1.21.11

    volumes:
      - ./data:/data
      - ./logs:/data/logs

    tty: true
    stdin_open: true




