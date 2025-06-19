---
type: technical
status: completed
tags:
  - memgraph
  - docker
  - настройка
  - база-данных
related:
  - "[[00 - GraphRAG Memory System MOC]]"
  - "[[Конфигурация MCP Сервера]]"
  - "[[Визуализация Memgraph Lab]]"
---

# Настройка Базы Данных Memgraph

> **Полная настройка Memgraph в Docker для системы GraphRAG памяти**

## 🎯 Обзор

**Memgraph** - это высокопроизводительная граф база данных с встроенными стриминговыми коннекторами, идеально подходящая для систем долговременной памяти ИИ.

**Наша конфигурация:**
- **Docker контейнер** для изоляции и портабельности
- **Порт 7687** для Bolt протокола (стандарт Neo4j/Cypher)
- **MAGE** включен для продвинутых графовых алгоритмов
- **Schema info** активирована для метаданных

## 🚀 Установка и Запуск

### Команда Запуска
```bash
docker run -p 7687:7687 --name memgraph memgraph/memgraph-mage:latest --schema-info-enabled=true
```

### Параметры Конфигурации

| Параметр | Значение | Описание |
|----------|----------|----------|
| `-p 7687:7687` | Порт маппинг | Bolt протокол для подключений |
| `--name memgraph` | Имя контейнера | Для удобного управления |
| `memgraph/memgraph-mage:latest` | Docker образ | Включает MAGE алгоритмы |
| `--schema-info-enabled=true` | Флаг схемы | Метаданные структуры графа |

## 📊 Проверка Состояния

### Статус Контейнера
```bash
docker ps
# Ожидаемый результат:
# CONTAINER ID   IMAGE                          COMMAND                  CREATED         STATUS         PORTS                    NAMES
# 247eb32a2f80   memgraph/memgraph-mage:latest  "/usr/lib/memgraph/m…"   2 minutes ago   Up 2 minutes   0.0.0.0:7687->7687/tcp   memgraph
```

### Тестовое Подключение
```cypher
// Через MCP или напрямую
SHOW STORAGE INFO;
SHOW INDEX INFO;
```

## 🔧 Конфигурация для Нашего Случая

### Оптимизации для GraphRAG

**Включенные возможности:**
- **Schema info** - для автоматического обнаружения структуры
- **MAGE алгоритмы** - для анализа центральности, сообществ
- **Streaming** - для реального времени обновлений
- **Multi-version concurrency** - для одновременных операций

### Рекомендуемые Настройки Памяти
```bash
# Для продакшена (опционально)
docker run -p 7687:7687 \
  --name memgraph \
  -e MEMGRAPH_CONFIG="--memory-limit=8GB" \
  memgraph/memgraph-mage:latest \
  --schema-info-enabled=true
```

## 🏗️ Архитектура Подключений

### Сетевая Топология
```
Cursor (MCP Client)
    ↓ HTTP (port 8000)
MCP-Memgraph Server (Docker)
    ↓ Bolt (port 7687)  
Memgraph Database (Docker)
    ↓ HTTP (port 3000)
Memgraph Lab (Docker)
```

### Docker Сеть
- **host.docker.internal** - для межконтейнерного общения
- **localhost** - для внешних подключений
- **bridge network** - стандартная Docker сеть

## 📋 Управление Жизненным Циклом

### Запуск
```bash
docker start memgraph
```

### Остановка
```bash
docker stop memgraph
```

### Перезапуск
```bash
docker restart memgraph
```

### Логи
```bash
docker logs memgraph
```

### Очистка (ОСТОРОЖНО!)
```bash
docker rm memgraph  # Удаляет контейнер
docker rmi memgraph/memgraph-mage:latest  # Удаляет образ
```

## 🔍 Мониторинг и Диагностика

### Проверка Здоровья
```cypher
// Через MCP инструменты
SHOW STORAGE INFO;
SHOW CONFIG;
```

### Метрики Производительности
```cypher
SHOW STATS;
SHOW TRANSACTIONS;
```

### Информация о Схеме
```cypher
CALL schema.node_type_properties();
CALL schema.rel_type_properties();
```

## ⚠️ Важные Моменты

### Персистентность Данных
**ВНИМАНИЕ**: Текущая конфигурация НЕ сохраняет данные между перезапусками контейнера!

**Для продакшена добавить volume:**
```bash
docker run -p 7687:7687 \
  --name memgraph \
  -v memgraph_data:/var/lib/memgraph \
  memgraph/memgraph-mage:latest \
  --schema-info-enabled=true
```

### Безопасность
- **Нет аутентификации** в текущей настройке
- **Открытый порт** 7687 на localhost
- **Для продакшена** настроить SSL и пароли

### Производительность
- **Память** - по умолчанию использует доступную системную память
- **Параллелизм** - поддерживает множественные одновременные соединения
- **Индексирование** - создавать индексы для часто запрашиваемых свойств

## 🔗 Связанные Компоненты

- [[Конфигурация MCP Сервера]] - Настройка моста между Cursor и Memgraph
- [[Визуализация Memgraph Lab]] - Веб-интерфейс для исследования графа
- [[Схема Типов Узлов]] - Структура данных в нашем графе

---

## 📝 История Изменений

**2025-06-19**: ✅ Успешно установлен и запущен Memgraph в Docker  
**Следующий шаг**: Настройка MCP сервера для интеграции с Cursor