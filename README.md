<div align="center">

# Electra ⚡🐇

**A from-scratch Raft consensus implementation with a live, breakable visualization, written in Go.**
*Elections you can actually watch happen.*

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPWVjZjA1ZTQ3c2U4d2MxOHA5bzE5YXZrNjBpanhzZW42anIyM3Z2azZlbjkwbWlrYSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/CufLv1T7gIPC/giphy.gif" alt="lightning" width="2000"/>
</div>

<br/><br/>

[![Go Version](https://img.shields.io/badge/Go-1.22%2B-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://go.dev)
[![License: MIT](https://img.shields.io/badge/license-MIT-00ADD8?style=for-the-badge)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-passing-00ADD8?style=for-the-badge)](#-testing--тестирование--pruebas)
[![Status](https://img.shields.io/badge/status-active-00ADD8?style=for-the-badge)](#)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-00ADD8?style=for-the-badge)](#-contributing--вклад--contribuciones)

<br/>

### 🌍 Choose your language / Выберите язык / Elige tu idioma

<a href="#-english"><img src="https://img.shields.io/badge/🇬🇧-English-00ADD8?style=for-the-badge"/></a>
<a href="#-русский"><img src="https://img.shields.io/badge/🇷🇺-Русский-00ADD8?style=for-the-badge"/></a>
<a href="#-español"><img src="https://img.shields.io/badge/🇪🇸-Español-00ADD8?style=for-the-badge"/></a>

</div>

---

<a id="-english"></a>
## 🇬🇧 English

<div align="center">
<img src="https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExNG80MW12cGkwYjQzZTBzdjB6cDc3dXZneHRwcTF4NHJwNnAwdDF4ciZlcD12MV9naWZzX3NlYXJjaCZjdD1n/rnkSmbEKJQ73W/giphy.gif" alt="Cute rabbit" width="220"/>
</div>

### 📖 Overview

**Electra** is a from-scratch implementation of the **Raft consensus algorithm** — leader election, log replication, and safety guarantees, exactly as described in the [original Raft paper](https://raft.github.io/raft.pdf). What makes it different from "yet another Raft library" is the **live web visualization**: you spin up a simulated cluster, watch leader election happen in real time, and — this is the part people actually remember from a demo — **you can break it yourself**. Kill the leader mid-session. Partition the network into two halves. Watch the cluster detect it, hold a new election, and heal.

This is not a wrapper around `hashicorp/raft`. Every part — election timeouts, term handling, log matching, commit index advancement — is implemented from the paper, with the reasoning documented in [`docs/design.md`](docs/design.md).

### 🏗 Architecture & Engineering Highlights

| Feature | Why it matters |
|---|---|
| ⚡ **Raft core, built from the paper** | Leader election, log replication, and the safety property proofs — not a black-box dependency |
| ⚡ **In-process cluster simulation** | N simulated nodes in a single binary, with an injectable network layer for controlled partition testing |
| ⚡ **Live web visualization (SSE/WebSocket)** | Real-time view of node state, current term, leader, and per-node log — the demo that sells itself in an interview |
| ⚡ **Chaos controls in the UI** | Kill the leader, partition the cluster, restore the network — from buttons, not from a debugger |
| ⚡ **Deterministic test harness** | Simulated clock for election-timeout tests — no `time.Sleep`-based flakiness in CI |

### 📁 Project Layout

```
.
├── cmd/
│   └── server/            # Cluster simulation + visualization server entrypoint
├── internal/
│   ├── raft/
│   │   ├── node.go        # Node state machine: follower / candidate / leader
│   │   ├── election.go    # Leader election & term handling
│   │   ├── log.go         # Log replication & commit index advancement
│   │   └── rpc.go         # RequestVote / AppendEntries RPC definitions
│   ├── network/
│   │   └── simnet.go      # In-process network with injectable partitions & latency
│   └── viz/
│       ├── hub.go         # Broadcasts cluster state to connected browsers
│       └── static/        # Frontend: cluster map, term/log timeline, chaos controls
├── docs/
│   └── design.md          # Algorithm write-up: election safety, log matching property
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Quick Start

```bash
git clone https://github.com/Inke-chip/Electra.git
cd Electra
go mod tidy

# start a 5-node simulated cluster with the visualization server
go run ./cmd/server -nodes 5 -port 8080

# open the visualization
open http://localhost:8080
```

From the UI you can kill the current leader, split the cluster into two network partitions, or restore full connectivity — and watch term numbers, votes, and log replication update live.

### 🧪 Testing

```bash
go test ./...
go test -race ./internal/raft/...      # concurrency safety
go test ./internal/raft/... -run TestElectionSafety -v
go test ./internal/raft/... -run TestLogMatching -v
```

### 📊 Benchmarks

| Scenario | Cluster size | Metric | Result |
|---|---|---|---|
| Leader election (cold start) | 5 nodes | Time to first leader | `TODO` |
| Leader election (after leader kill) | 5 nodes | Time to new leader | `TODO` |
| Log replication throughput | 5 nodes | Entries/sec committed | `TODO` |
| Recovery after partition heal | 5 nodes (2/3 split) | Time to re-converge | `TODO` |

### 🎯 Design Decisions (short version)

- **Simulated network, not real sockets** — lets tests inject exact partition scenarios deterministically instead of hoping a `time.Sleep` lines up right; real TCP transport is a documented future step, not a shortcut hidden from the reader.
- **No log compaction / snapshotting yet** — Raft's snapshotting mechanism is its own substantial piece of the paper; leaving it out is called out explicitly in `docs/design.md` rather than silently omitted.
- **Randomized election timeouts** — implemented exactly as the paper specifies, because this is the detail that actually prevents split-vote livelock, and it's worth explaining why in an interview rather than just citing "the paper said so."

Full reasoning, the state diagram, and the safety argument: [`docs/design.md`](docs/design.md).

### 🤝 Contributing

Issues and PRs are welcome — especially real-network transport implementations and additional chaos scenarios for the UI.

### 📄 License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for details.

<div align="right"><a href="#electra-">↑ back to top</a></div>

---

<a id="-русский"></a>
## 🇷🇺 Русский

<div align="center">
<img src="https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExbTRjd3N1Nm0yMHQ4aDBrMDE4aXVsdmFtMWJhZ3p3dmhtMGMzbGNqaSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/Xb6J4XCrn1G08vnyvh/giphy.gif" alt="Прыгающий кролик" width="2000"/>
</div>

### 📖 Обзор

**Electra** — реализация **алгоритма консенсуса Raft** с нуля: лидер-электион, репликация лога и гарантии безопасности — точно по [оригинальной статье Raft](https://raft.github.io/raft.pdf). Отличие от "ещё одной Raft-библиотеки" — **живая веб-визуализация**: поднимаешь симулированный кластер, наблюдаешь выборы лидера в реальном времени, и — это то, что реально запоминается на демо — **можешь ломать его сама**. Убить лидера посреди сессии. Разорвать сеть на две половины. Смотреть, как кластер это обнаруживает, проводит новые выборы и восстанавливается.

Это не обёртка над `hashicorp/raft`. Каждая часть — election timeout, обработка term, log matching, продвижение commit index — реализована по статье, с обоснованием в [`docs/design.md`](docs/design.md).

### 🏗 Архитектура и инженерные особенности

| Фича | Почему это важно |
|---|---|
| ⚡ **Raft-ядро по статье** | Выборы лидера, репликация лога и доказательства свойств безопасности — не чёрный ящик зависимости |
| ⚡ **Симуляция кластера в одном процессе** | N симулированных нод в одном бинарнике, с инжектируемым сетевым слоем для контролируемого тестирования партиций |
| ⚡ **Живая веб-визуализация (SSE/WebSocket)** | Состояние нод, текущий term, лидер и лог каждой ноды в реальном времени — демо, которое продаёт себя само на собеседовании |
| ⚡ **Chaos-контролы в интерфейсе** | Убить лидера, разорвать кластер на части, восстановить сеть — кнопками, а не через дебаггер |
| ⚡ **Детерминированный тестовый харнесс** | Симулированные часы для тестов election timeout — никакого флейки на `time.Sleep` в CI |

### 📁 Структура проекта

```
.
├── cmd/
│   └── server/            # Точка входа: симуляция кластера + сервер визуализации
├── internal/
│   ├── raft/
│   │   ├── node.go        # Состояние ноды: follower / candidate / leader
│   │   ├── election.go    # Выборы лидера и обработка term
│   │   ├── log.go         # Репликация лога и продвижение commit index
│   │   └── rpc.go         # Определения RequestVote / AppendEntries RPC
│   ├── network/
│   │   └── simnet.go      # Внутрипроцессная сеть с инжектируемыми партициями и задержками
│   └── viz/
│       ├── hub.go         # Рассылка состояния кластера подключённым браузерам
│       └── static/        # Фронтенд: карта кластера, таймлайн term/log, chaos-контролы
├── docs/
│   └── design.md          # Разбор алгоритма: election safety, log matching property
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Быстрый запуск

```bash
git clone https://github.com/Inke-chip/electra.git
cd electra
go mod tidy

# запустить симулированный кластер из 5 нод с сервером визуализации
go run ./cmd/server -nodes 5 -port 8080

# открыть визуализацию
open http://localhost:8080
```

Из интерфейса можно убить текущего лидера, разбить кластер на две сетевые партиции или восстановить связность — и вживую смотреть, как обновляются term, голоса и репликация лога.

### 🧪 Тестирование

```bash
go test ./...
go test -race ./internal/raft/...      # потокобезопасность
go test ./internal/raft/... -run TestElectionSafety -v
go test ./internal/raft/... -run TestLogMatching -v
```

### 📊 Бенчмарки

| Сценарий | Размер кластера | Метрика | Результат |
|---|---|---|---|
| Выборы лидера (холодный старт) | 5 нод | Время до первого лидера | `TODO` |
| Выборы лидера (после убийства лидера) | 5 нод | Время до нового лидера | `TODO` |
| Пропускная способность репликации лога | 5 нод | Записей/сек закоммичено | `TODO` |
| Восстановление после разрыва сети | 5 нод (разбиение 2/3) | Время до повторной сходимости | `TODO` |

### 🎯 Ключевые архитектурные решения (кратко)

- **Симулированная сеть вместо реальных сокетов** — позволяет тестам инжектить точные сценарии партиций детерминированно, вместо того чтобы надеяться, что `time.Sleep` совпадёт по времени; реальный TCP-транспорт — задокументированный следующий шаг, а не спрятанное упрощение.
- **Пока нет log compaction / snapshotting** — механизм снапшотов в Raft — это отдельная существенная часть статьи; его отсутствие явно проговорено в `docs/design.md`, а не тихо пропущено.
- **Рандомизированные election timeout** — реализовано ровно как в статье, потому что именно эта деталь предотвращает livelock от split vote, и это стоит объяснить на собеседовании, а не просто сослаться на "так написано в статье".

Полное обоснование, диаграмма состояний и аргументация безопасности — в [`docs/design.md`](docs/design.md).

### 🤝 Вклад в проект

Issues и Pull Request'ы приветствуются — особенно реализация транспорта по настоящей сети и новые chaos-сценарии для интерфейса.

### 📄 Лицензия

Проект распространяется под лицензией MIT. Подробности в файле [`LICENSE`](LICENSE).

<div align="right"><a href="#electra-">↑ наверх</a></div>

---

<a id="-español"></a>
## 🇪🇸 Español

<div align="center">
<img src="https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExbTRjd3N1Nm0yMHQ4aDBrMDE4aXVsdmFtMWJhZ3p3dmhtMGMzbGNqaSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/yeLsunxmHkDuLgf3eU/200.gif" alt="Conejo blanco tierno" width="2000"/>
</div>

### 📖 Descripción general

**Electra** es una implementación desde cero del **algoritmo de consenso Raft** — elección de líder, replicación de log y garantías de seguridad, tal como se describe en el [paper original de Raft](https://raft.github.io/raft.pdf). Lo que la diferencia de "otra librería Raft más" es la **visualización web en vivo**: levantas un clúster simulado, observas la elección de líder en tiempo real y — esto es lo que la gente realmente recuerda de una demo — **puedes romperlo tú misma**. Matar al líder a mitad de sesión. Partir la red en dos mitades. Ver cómo el clúster lo detecta, celebra una nueva elección y se recupera.

No es un wrapper sobre `hashicorp/raft`. Cada parte — election timeout, manejo de terms, log matching, avance del commit index — está implementada a partir del paper, con el razonamiento documentado en [`docs/design.md`](docs/design.md).

### 🏗 Arquitectura y aspectos técnicos destacados

| Característica | Por qué importa |
|---|---|
| ⚡ **Núcleo Raft basado en el paper** | Elección de líder, replicación de log y las pruebas de las propiedades de seguridad — no una dependencia de caja negra |
| ⚡ **Simulación de clúster en un solo proceso** | N nodos simulados en un único binario, con una capa de red inyectable para pruebas controladas de particiones |
| ⚡ **Visualización web en vivo (SSE/WebSocket)** | Vista en tiempo real del estado de cada nodo, term actual, líder y log — la demo que se vende sola en una entrevista |
| ⚡ **Controles de caos en la interfaz** | Matar al líder, particionar el clúster, restaurar la red — con botones, no con un depurador |
| ⚡ **Entorno de pruebas determinista** | Reloj simulado para las pruebas de election timeout — sin flakiness basado en `time.Sleep` en CI |

### 📁 Estructura del proyecto

```
.
├── cmd/
│   └── server/            # Punto de entrada: simulación de clúster + servidor de visualización
├── internal/
│   ├── raft/
│   │   ├── node.go        # Estado del nodo: follower / candidate / leader
│   │   ├── election.go    # Elección de líder y manejo de terms
│   │   ├── log.go         # Replicación de log y avance del commit index
│   │   └── rpc.go         # Definiciones de RPC RequestVote / AppendEntries
│   ├── network/
│   │   └── simnet.go      # Red interna al proceso con particiones y latencia inyectables
│   └── viz/
│       ├── hub.go         # Difunde el estado del clúster a los navegadores conectados
│       └── static/        # Frontend: mapa del clúster, línea de tiempo de term/log, controles de caos
├── docs/
│   └── design.md          # Explicación del algoritmo: election safety, log matching property
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Inicio rápido

```bash
git clone https://github.com/Inke-chip/electra.git
cd electra
go mod tidy

# levantar un clúster simulado de 5 nodos con el servidor de visualización
go run ./cmd/server -nodes 5 -port 8080

# abrir la visualización
open http://localhost:8080
```

Desde la interfaz puedes matar al líder actual, dividir el clúster en dos particiones de red o restaurar la conectividad completa — y ver en vivo cómo se actualizan los terms, los votos y la replicación del log.

### 🧪 Pruebas

```bash
go test ./...
go test -race ./internal/raft/...      # seguridad de concurrencia
go test ./internal/raft/... -run TestElectionSafety -v
go test ./internal/raft/... -run TestLogMatching -v
```

### 📊 Benchmarks

| Escenario | Tamaño del clúster | Métrica | Resultado |
|---|---|---|---|
| Elección de líder (arranque en frío) | 5 nodos | Tiempo hasta el primer líder | `TODO` |
| Elección de líder (tras matar al líder) | 5 nodos | Tiempo hasta el nuevo líder | `TODO` |
| Throughput de replicación de log | 5 nodos | Entradas/seg confirmadas | `TODO` |
| Recuperación tras sanar la partición | 5 nodos (división 2/3) | Tiempo de reconvergencia | `TODO` |

### 🎯 Decisiones de diseño (versión corta)

- **Red simulada en lugar de sockets reales** — permite que las pruebas inyecten escenarios de partición exactos de forma determinista, en vez de confiar en que un `time.Sleep` coincida justo; un transporte TCP real es un paso futuro documentado, no un atajo escondido.
- **Todavía sin log compaction / snapshotting** — el mecanismo de snapshots de Raft es en sí mismo una parte sustancial del paper; su ausencia se menciona explícitamente en `docs/design.md` en lugar de omitirse en silencio.
- **Election timeouts aleatorizados** — implementado exactamente como especifica el paper, porque es el detalle que realmente evita el livelock por voto dividido, y vale la pena explicarlo en una entrevista en vez de solo citar "así lo dice el paper".

Razonamiento completo, el diagrama de estados y el argumento de seguridad: [`docs/design.md`](docs/design.md).

### 🤝 Contribuciones

Los issues y pull requests son bienvenidos — especialmente implementaciones de transporte por red real y nuevos escenarios de caos para la interfaz.

### 📄 Licencia

Distribuido bajo la licencia MIT. Ver [`LICENSE`](LICENSE) para más detalles.

<div align="right"><a href="#electra-">↑ arriba</a></div>

---

<div align="center">

⚡ Made with Go, elections, and a lot of rabbits 🐇

</div>
