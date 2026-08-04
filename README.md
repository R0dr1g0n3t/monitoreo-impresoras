# Monitoreo de impresoras HP LaserJet MFP E42540

Sistema de monitoreo para impresoras de red desarrollado con Docker, Prometheus, Grafana, Blackbox Exporter y SNMP Exporter.

Permite supervisar en tiempo real:

- Disponibilidad online y offline.
- Nivel de tóner.
- Alertas activas de Printer-MIB.
- Estado de Prometheus.
- Antigüedad de los datos recibidos.
- Mensajes como bandeja vacía, atasco o tamaño de papel incorrecto.

## Dashboard

![Dashboard completo](screenshots/Dashboard%20Completo.png)

## Arquitectura

```text
Impresoras de red
        │
        ├── HTTP/HTTPS ──> Blackbox Exporter
        │
        └── SNMP ───────> SNMP Exporter
                               │
                               ▼
                           Prometheus
                               │
                               ▼
                            Grafana
```

## Tecnologías

- Docker y Docker Compose
- Prometheus
- Grafana
- Blackbox Exporter
- SNMP Exporter
- SNMP v2c
- Printer-MIB
- PromQL

## Estructura

```text
.
├── docker
│   ├── blackbox.yml
│   ├── prometheus.yml
│   └── snmp-alerts.yml
├── grafana
│   └── grafana-dashboard.json
├── screenshots
│   └── Dashboard Completo.png
├── .gitignore
├── docker-compose.yml
└── README.md
```

## Requisitos

- Docker Desktop o Docker Engine.
- Docker Compose.
- Impresoras accesibles desde el host.
- SNMP v2c habilitado en las impresoras.
- Comunidad SNMP configurada.

## Configuración previa

### 1. Configurar la comunidad SNMP

Edita:

```text
docker/snmp-alerts.yml
```

Reemplaza:

```yaml
community: CHANGE_ME
```

por la comunidad SNMP configurada en las impresoras.

### 2. Configurar las IP

Edita:

```text
docker/prometheus.yml
```

Reemplaza las direcciones de ejemplo:

```text
192.168.1.101
192.168.1.102
...
```

por las IP reales de las impresoras.

Para el trabajo de Blackbox, conserva el prefijo:

```text
https://
```

## Iniciar el stack

Desde la raíz del proyecto:

```bash
docker compose up -d
```

Comprobar los contenedores:

```bash
docker compose ps
```

Servicios publicados:

| Servicio | Dirección |
|---|---|
| Grafana | `http://localhost:3000` |
| Prometheus | `http://localhost:9090` |
| Blackbox Exporter | `http://localhost:9115` |
| SNMP Exporter | `http://localhost:9116` |
| SNMP Alerts Exporter | `http://localhost:9117` |

## Configurar Grafana

1. Abre Grafana en `http://localhost:3000`.
2. Inicia sesión.
3. Crea un datasource de tipo Prometheus.
4. Usa como URL:

```text
http://prometheus:9090
```

5. Asigna al datasource el nombre:

```text
Prometheus
```

## Importar el dashboard

En Grafana:

```text
Dashboards → New → Import
```

Importa:

```text
grafana/grafana-dashboard.json
```

Selecciona el datasource `Prometheus` si Grafana lo solicita.

## Alertas SNMP personalizadas

El módulo estándar utilizado inicialmente no exponía el texto de las alertas activas.

Se creó un módulo adicional que consulta:

```text
1.3.6.1.2.1.43.18
```

y publica métricas como:

```text
prtAlertDescription
prtAlertSeverityLevel
prtAlertCode
```

Ejemplo:

```text
Bandeja 2 vacía: Normal, Oficio
```

Los estados informativos, como el modo de reposo, pueden filtrarse mediante PromQL.

## Problemas resueltos

- Lectura de alertas activas mediante Printer-MIB.
- Conversión de texto SNMP en etiquetas de Prometheus.
- Diferencias entre consultas `Range` e `Instant`.
- Paneles de Grafana mostrando `No data`.
- Persistencia y reinicio de contenedores.
- Filtrado de mensajes informativos.
- Conversión de timestamps para mostrar la antigüedad de los datos.
- Comunicación entre servicios mediante la red interna de Docker Compose.

## Aprendizajes

- Monitoreo de infraestructura.
- SNMP, MIB y OID.
- Exporters de Prometheus.
- Consultas PromQL.
- Dashboards en Grafana.
- Contenedores Docker.
- Diagnóstico entre Grafana, Prometheus y exporters.

## Estado

Proyecto funcional para monitoreo de impresoras HP LaserJet MFP E42540 y otros equipos compatibles con Printer-MIB.