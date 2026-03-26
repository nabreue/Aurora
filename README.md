
---

## 📊 Productos Analíticos

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

## 🚀 Despliegue

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
