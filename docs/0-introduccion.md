# 0. Introducción

En esta práctica montamos un sistema de **monitorización** de servidores Linux con dos herramientas open source que suelen ir de la mano: **Prometheus** (recoge y guarda las métricas) y **Grafana** (las muestra en paneles y gráficas).

## ¿Qué es Prometheus?

Prometheus es un sistema de monitorización y una **base de datos de series temporales** (TSDB). Sus características principales:

- **Modelo *pull***: es Prometheus quien pregunta cada cierto tiempo (`scrape_interval`) a los equipos monitorizados por sus métricas, en lugar de que estos se las envíen.
- **Exporters**: pequeños servicios que exponen las métricas de un sistema en formato texto a través de HTTP (`/metrics`). En esta práctica usamos **Node Exporter**, que expone las métricas del sistema operativo (CPU, memoria, disco, red...).
- **PromQL**: su propio lenguaje de consultas para filtrar, agregar y operar con las métricas (por ejemplo, `node_cpu_seconds_total`).
- **Etiquetas (*labels*)**: cada serie temporal se identifica por su nombre y un conjunto de etiquetas (`job`, `instance`, `mode`...), lo que permite filtrar y agrupar fácilmente.
- **Configuración en YAML**: los objetivos a monitorizar (*targets*) y la forma de agruparlos (*jobs*) se definen en `/etc/prometheus/prometheus.yml`.

## ¿Qué es Grafana?

Grafana es una plataforma de **visualización y análisis**. No almacena las métricas: se conecta a fuentes de datos (*data sources*), como Prometheus, y permite:

- Crear **dashboards** con paneles de gráficas, indicadores, tablas, etc.
- Importar dashboards ya hechos por la comunidad (como *Node Exporter Full*).
- Escribir consultas personalizadas y diseñar paneles propios.
- Configurar **alertas** que notifican (por ejemplo, por correo) cuando una métrica supera un umbral.

## Arquitectura

```
 ┌──────────────────┐   scrape (HTTP :9100)   ┌──────────────────┐   consultas PromQL   ┌──────────────┐
 │  Node Exporter   │ ◄────────────────────── │    Prometheus    │ ◄─────────────────── │   Grafana    │
 │ (cada servidor)  │                         │  (TSDB + PromQL) │                      │ (dashboards) │
 └──────────────────┘                         └──────────────────┘                      └──────────────┘
                                                      :9090                                   :3000
```

| Componente    | Puerto | Función                                    |
|---------------|--------|--------------------------------------------|
| Node Exporter | 9100   | Expone las métricas del sistema            |
| Prometheus    | 9090   | Recoge, almacena y consulta las métricas   |
| Grafana       | 3000   | Visualiza las métricas y gestiona alertas  |

## Entorno de la práctica

- Servidor **Debian 13 (trixie)** (`debian212`) con IP `172.26.2.62`, donde se instalan Prometheus, Node Exporter y Grafana.
- Equipo cliente con navegador para acceder a las interfaces web.

## Qué se aprende

1. Instalar y configurar Prometheus y Node Exporter, y validar su configuración YAML.
2. Instalar Grafana desde su repositorio oficial.
3. Conectar Grafana con Prometheus como fuente de datos e importar un dashboard.
4. Monitorizar varios servidores a la vez.
5. Generar carga en un servidor y observar su efecto en las gráficas.
6. Escribir consultas PromQL propias y diseñar paneles nuevos.
7. Configurar alertas que avisan por Gmail.
8. Aplicarlo todo a la monitorización de bases de datos MariaDB.
