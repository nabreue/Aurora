# Aurora Tickets - Proyecto Big Data

![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20S3%20%7C%20RDS%20%7C%20CloudWatch-orange)
![Spark](https://img.shields.io/badge/Spark-3.5.1-red)
![Python](https://img.shields.io/badge/Python-3.10-blue)
![License](https://img.shields.io/badge/License-MIT-green)

## Descripción del Proyecto

Aurora Tickets es una plataforma de venta de entradas para eventos (conciertos, teatro, festivales) que ha detectado varios problemas operativos:
- **Baja conversión**: mucho tráfico web pero pocas compras completadas
- **Problemas de rendimiento**: picos de errores y lentitud en algunas páginas
- **Tráfico sospechoso**: posibles bots o tráfico anómalo
- **Falta de visibilidad**: ausencia de una capa de observabilidad y analítica Big Data

Este proyecto implementa una solución completa en AWS que:
1. Captura y centraliza el clickstream de usuarios
2. Permite consultas rápidas y visualización operativa con CloudWatch
3. Procesa datos a escala con Spark distribuido
4. Publica métricas finales en RDS MySQL y resultados en S3 (Parquet)

---

## Arquitectura
┌─────────────────────────────────────────────────────────────────┐
│ AWS CLOUD │
├─────────────────────────────────────────────────────────────────┤
│ │
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ │
│ │ EC2-1 │ │ EC2-2 │ │ EC2-3 │ │
│ │ Spark Master │────▶│ Spark Worker │────▶│ Spark Worker │ │
│ └──────────────┘ └──────────────┘ └──────────────┘ │
│ │ │ │ │
│ ▼ ▼ ▼ │
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ │
│ │ EC2-4 │ │ EC2-5 │ │ EC2-6 │ │
│ │ Spark Worker │ │ Spark Submit │ │ Web + FastAPI│ │
│ └──────────────┘ └──────────────┘ └──────────────┘ │
│ │ │ │ │
│ └────────────────────┼────────────────────┘ │
│ ▼ │
│ ┌─────────────────┐ │
│ │ S3 │ │
│ │ raw/curated/ │ │
│ │ analytics/ │ │
│ └────────┬────────┘ │
│ │ │
│ ▼ │
│ ┌─────────────────┐ │
│ │ RDS MySQL │ │
│ │ metrics tables │ │
│ └─────────────────┘ │
│ │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ CloudWatch │ │
│ │ Log Group: /aurora/<student_id>/clickstream │ │
│ │ Dashboard: aurora-dashboard-<student_id> │ │
│ │ 4 Queries guardadas │ │
│ └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘


### Instancias EC2

| EC2 | Rol | Tipo |
|-----|-----|------|
| spark-master | Master Spark | t3.micro |
| spark-worker1 | Worker 1 | t3.micro |
| spark-worker2 | Worker 2 | t3.micro |
| spark-worker3 | Worker 3 | t3.micro |
| spark-submit | Nodo submit | t3.micro |
| aurora-web | Web + FastAPI + CloudWatch Agent | t3.micro |

---

## Estructura del Proyecto
aurora-tickets/
├── infraestructura/
│ ├── terraform/ # Despliegue de infraestructura
│ │ ├── main.tf
│ │ ├── variables.tf
│ │ └── outputs.tf
│ └── scripts/ # Scripts de configuración
│ ├── setup-spark-master.sh
│ ├── setup-spark-worker.sh
│ └── setup-web.sh
├── spark-jobs/
│ ├── job_curated.py # Job 1: Curación (raw → curated)
│ ├── job_analytics.py # Job 2: Analítica (curated → analytics + RDS)
│ └── requirements.txt
├── generators/
│ ├── generate_business_data.py # Generador de datos de negocio
│ └── generate_clickstream.py # Generador de eventos de clickstream
├── web/
│ ├── main.py # FastAPI backend
│ └── static/ # Frontend estático
├── cloudwatch/
│ └── config.json # Configuración del CloudWatch Agent
├── docs/
│ ├── CRISP-DM.md # Documentación CRISP-DM
│ └── evidence/ # Capturas de evidencias
│ ├── ec2-panel.png
│ ├── spark-ui.png
│ ├── cloudwatch-logs.png
│ ├── cloudwatch-queries.png
│ ├── cloudwatch-dashboard.png
│ ├── s3-structure.png
│ └── rds-tables.png
├── memoria.pdf # Memoria del proyecto (20 páginas)
└── README.md # Este archivo


---

## Productos Analíticos

### Producto A - Funnel de Conversión Diario

| Campo | Descripción |
|-------|-------------|
| dt | Fecha |
| sessions_total | Total de sesiones |
| sessions_event_list | Sesiones que vieron listado |
| sessions_event_detail | Sesiones que vieron detalle |
| sessions_begin_checkout | Sesiones que iniciaron checkout |
| sessions_purchase | Sesiones que completaron compra |
| conversion_rate | Tasa de conversión (purchase/total) |

### Producto B - Ranking de Eventos

| Campo | Descripción |
|-------|-------------|
| dt | Fecha |
| event_id | ID del evento |
| detail_views | Número de vistas detalle |
| purchases | Número de compras |
| revenue_total | Ingresos totales |
| interest_to_purchase_ratio | Ratio conversión |

### Producto C - Detección de Anomalías

| Campo | Descripción |
|-------|-------------|
| dt | Fecha |
| ip | Dirección IP |
| requests | Número de peticiones |
| is_anomaly | Indicador de anomalía |
| reason | Explicación de la anomalía |

---

## Despliegue

### Requisitos Previos

- AWS Academy Learner Lab (o cuenta AWS con créditos)
- Terraform >= 1.6.0
- AWS CLI configurado
- Python 3.10+
- Spark 3.5.1

### 1. Despliegue de Infraestructura (Terraform)

```bash
cd infraestructura/terraform
terraform init
terraform plan
terraform apply -auto-approve



