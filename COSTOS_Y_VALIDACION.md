# Costos y Validación Comercial — Plataforma Salud Inteligente
## DOCUMENTO DE TRABAJO INTERNO — PENDIENTE DE VALIDACIÓN

> ⚠️ Este documento contiene estimaciones preliminares y elementos que requieren
> validación antes de presentarse a cualquier cliente. **No compartir externamente.**

---

## 1. MODELO COMERCIAL — OPCIONES DE PRECIO (BORRADOR)

> **Estado:** Estimaciones iniciales basadas en proyectos similares (ICFES, PON).
> Requieren validación con área comercial y ajuste según alcance real de cada cliente.

### Opción A — Implementación por fases

| Fase | Semanas | Alcance | Estimado USD |
|---|---|---|---|
| F1 | 1–8 | Expediente FHIR + Credencialización | ~180.000 |
| F2 | 9–16 | Motor de asignación + georreferenciación | ~220.000 |
| F3 | 17–22 | Telemedicina + App móvil | ~160.000 |
| F4 | 23–28 | Mapas predictivos ML + dashboards | ~140.000 |
| F5 | 29–32 | Cámaras de compensación + go-live | ~100.000 |
| **TOTAL** | **32 semanas** | **Plataforma completa** | **~800.000** |

**Pendiente de validar:**
- [ ] Equipo estimado (perfiles, dedicación)
- [ ] Horas por fase detalladas (como ICFES: 2.089h)
- [ ] ¿Incluye soporte post go-live?
- [ ] ¿Aplica IVA / retenciones según país del cliente?

---

### Opción B — SaaS mensual

| Módulo | USD/mes |
|---|---|
| Base (FHIR + asignación + app) | 15.000 |
| + Telemedicina | 8.000 |
| + Mapas predictivos ML | 12.000 |
| + Cámaras de compensación | 10.000 |
| **Plataforma completa** | **45.000** |

**Pendiente de validar:**
- [ ] ¿Incluye infraestructura AWS o solo licencia de uso?
- [ ] SLAs de disponibilidad y penalizaciones
- [ ] Límites de usuarios / transacciones incluidas
- [ ] Política de escalamiento de precio

---

## 2. INFRAESTRUCTURA AWS — DESGLOSE DE COSTOS ESTIMADOS

> **Metodología:** Estimaciones basadas en AWS Pricing Calculator y experiencia en
> proyectos ICFES (~USD 825/convocatoria) y PON. Deben validarse con AWS Calculator
> oficial para cada configuración de cliente.
> **Región de referencia:** us-east-1 (N. Virginia). Ajustar para Mexico/São Paulo (sa-east-1).

---

### Escenario 1 — Piloto (1 hospital / 1 municipio / ~50K usuarios activos/mes)

| Servicio AWS | Configuración | USD/mes estimado |
|---|---|---|
| **ECS Fargate** | 4 microservicios · 0.5 vCPU · 1GB RAM · ~720h/mes | ~120 |
| **RDS Aurora PostgreSQL** | db.t3.medium · Single-AZ · 20 GB storage | ~95 |
| **Amazon HealthLake** | FHIR R4 · ~50K recursos/mes · 10 GB | ~85 |
| **S3** | Data Lake ~100 GB + backups + objetos FHIR | ~15 |
| **API Gateway** | ~500K llamadas/mes (REST) | ~8 |
| **Lambda** | ~1M invocaciones/mes · avg 500ms · 256MB | ~12 |
| **Amazon Cognito** | ~50K MAU · tier gratuito hasta 50K | ~0–15 |
| **Amazon Location Service** | ~10K geocodificaciones/mes (HERE) | ~5 |
| **Amazon Bedrock** | ~100K tokens/mes (triage + resúmenes) | ~15 |
| **Amazon Chime SDK** | ~2K minutos de video/mes | ~20 |
| **CloudFront** | ~10 GB transferencia + ~500K requests | ~5 |
| **SES + SNS** | ~5K emails + ~2K SMS/mes | ~8 |
| **CloudWatch + X-Ray** | Logs básicos + trazas | ~20 |
| **WAF** | Reglas básicas + ~500K requests | ~15 |
| **KMS** | ~5 claves + ~100K operaciones | ~5 |
| **TOTAL ESTIMADO PILOTO** | | **~USD 428–443/mes** |

> **Nota:** Sin SageMaker ni QuickSight (dashboards básicos en CloudWatch).
> Sin Amazon Forecast (predicción desactivada en piloto).

---

### Escenario 2 — Regional (red de 20 IPS / ~500K usuarios activos/mes)

| Servicio AWS | Configuración | USD/mes estimado |
|---|---|---|
| **ECS Fargate** | 8 microservicios · 1 vCPU · 2GB RAM · auto-scaling | ~650 |
| **RDS Aurora PostgreSQL** | db.r6g.large · Multi-AZ · 100 GB | ~620 |
| **Amazon HealthLake** | ~500K recursos/mes · 100 GB | ~450 |
| **ElastiCache Redis** | cache.t3.medium · 2 nodos | ~140 |
| **S3** | ~1 TB Data Lake + DICOM + backups | ~80 |
| **API Gateway** | ~5M llamadas/mes | ~55 |
| **Lambda** | ~10M invocaciones/mes | ~85 |
| **Amazon Cognito** | ~500K MAU | ~1.250 |
| **Amazon Location Service** | ~100K geocodificaciones + Route Calculator | ~60 |
| **Amazon Bedrock** | ~1M tokens/mes | ~120 |
| **Amazon SageMaker** | Inferencia endpoints (dengue + saturación) | ~280 |
| **Amazon Forecast** | ~12 datasets · inferencias mensuales | ~150 |
| **Amazon Chime SDK** | ~50K minutos video/mes | ~500 |
| **Amazon Transcribe Medical** | ~100h transcripción/mes | ~240 |
| **Amazon Comprehend Medical** | ~500K unidades texto/mes | ~50 |
| **QuickSight** | 5 autores + 50 lectores | ~180 |
| **CloudFront** | ~100 GB + 5M requests | ~30 |
| **SES + SNS + Pinpoint** | ~100K emails + ~50K SMS | ~90 |
| **CloudWatch + X-Ray** | Logs + trazas completas | ~120 |
| **WAF + Shield Standard** | Protección DDoS básica | ~45 |
| **KMS + Secrets Manager** | ~20 claves + secretos | ~25 |
| **TOTAL ESTIMADO REGIONAL** | | **~USD 5.220–5.500/mes** |

> **Con optimizaciones FinOps** (Reserved Instances 1 año RDS/ElastiCache,
> Savings Plans Fargate, S3 Intelligent-Tiering): reducción estimada **35–40%**
> → **~USD 3.200–3.600/mes**

---

### Escenario 3 — Nacional (zona IMSS-Bienestar / ~5M usuarios activos/mes)

| Servicio AWS | Configuración | USD/mes estimado |
|---|---|---|
| **ECS Fargate** | 12 microservicios · auto-scaling agresivo | ~3.500 |
| **RDS Aurora PostgreSQL** | db.r6g.2xlarge · Multi-AZ · Global · 500 GB | ~3.800 |
| **Amazon HealthLake** | ~5M recursos/mes · 1 TB | ~2.800 |
| **ElastiCache Redis** | Cluster mode · 6 nodos r6g.large | ~1.100 |
| **S3** | ~10 TB + lifecycle policies | ~350 |
| **API Gateway** | ~50M llamadas/mes | ~350 |
| **Lambda** | ~100M invocaciones/mes | ~600 |
| **Amazon Cognito** | ~5M MAU | ~7.500 |
| **Amazon Location Service** | ~1M geocodificaciones + Route Calculator | ~550 |
| **Amazon Bedrock** | ~10M tokens/mes | ~1.200 |
| **Amazon SageMaker** | Multi-model endpoints + reentrenamiento mensual | ~1.800 |
| **Amazon Forecast** | ~50 datasets nacionales | ~600 |
| **Amazon Chime SDK** | ~500K minutos video/mes | ~5.000 |
| **Amazon Transcribe Medical** | ~1.000h/mes | ~2.400 |
| **Amazon Comprehend Medical** | ~5M unidades/mes | ~500 |
| **QuickSight** | 20 autores + 500 lectores | ~1.800 |
| **CloudFront** | ~1 TB + 50M requests | ~200 |
| **SES + SNS + Pinpoint** | ~1M emails + 500K SMS | ~750 |
| **CloudWatch + X-Ray + GuardDuty** | Observabilidad completa | ~800 |
| **WAF + Shield Advanced** | Protección enterprise | ~3.200 |
| **KMS + Secrets Manager + CloudTrail** | Seguridad y auditoría | ~350 |
| **TOTAL ESTIMADO NACIONAL** | | **~USD 39.150–41.000/mes** |

> **Con Reserved Instances + Savings Plans (1 año):** reducción ~40%
> → **~USD 23.500–24.600/mes**

---

### Resumen de escenarios

| Escenario | Usuarios/mes | Costo bruto/mes | Con FinOps (1 año RI) |
|---|---|---|---|
| Piloto (1 hospital) | ~50K | ~USD 440 | ~USD 300 |
| Regional (20 IPS) | ~500K | ~USD 5.350 | ~USD 3.400 |
| Nacional (zona IMSS-B) | ~5M | ~USD 40.000 | ~USD 24.000 |

> **Pendiente de validar:**
> - [ ] Correr AWS Pricing Calculator oficial para cada escenario
> - [ ] Validar región: sa-east-1 (Sao Paulo) puede ser +10–15% vs us-east-1
> - [ ] HealthLake pricing: verificar costo por recurso actualizado (cambia frecuente)
> - [ ] Cognito: ~5M MAU tiene impacto significativo — validar si aplica tarifa enterprise
> - [ ] Chime SDK: precio por minuto varía según tipo de llamada (audio vs video vs screen share)
> - [ ] Shield Advanced: USD 3.000/mes fijo + uso — validar si el cliente ya tiene contrato AWS

---

## 3. MERCADO PRIVADO — PENDIENTE DE VALIDACIÓN

> Los siguientes compradores del sector privado tienen potencial pero requieren
> validación de fuentes y estrategia de entrada antes de incluirlos en la propuesta.

### Redes hospitalarias privadas México

| Institución | Por qué es atractivo | Qué falta validar |
|---|---|---|
| **Hospital ÁNGELES** (15 hospitales) | Red grande, presupuesto alto, competencia limitada en IA | ¿Tienen ya un HIS? ¿SAP Health? ¿Quién decide tecnología? |
| **STAR MÉDICA** (8 hospitales) | Enfocado en nivel socioeconómico medio-alto | Ciclos de compra, proceso de licitación interno |
| **ABC Medical Center** | Internacional, orientado a excelencia clínica | Probablemente ya tiene EMR avanzado (Epic/Cerner) |
| **Grupo Christus Muguerza** (Monterrey) | Noreste industrial, employer-sponsored health | Integración con seguros empresariales |
| **Aseguradoras / Seguros privados** | AXA, Mapfre, GNP — gestión de red de prestadores | Regulación CNSF, no SSA — diferente ciclo regulatorio |

**Acciones pendientes antes de incluir en pitch:**
- [ ] Confirmar que ninguno tiene contrato vigente con competidor directo
- [ ] Identificar stakeholder de tecnología en cada red
- [ ] Verificar si usan Epic, Cerner, HL7 v2 — determina esfuerzo de integración
- [ ] Validar disposición a adoptar AWS vs on-premise o Azure

---

## 4. VALIDACIÓN DE FUENTES — DATOS USADOS EN LA PROPUESTA

| Dato | Fuente | Estado |
|---|---|---|
| Mercado digital health MX 2024: USD 2.83B | Grand View Research | ✅ Fuente pagada, confiable |
| Telemedicina MX 2024: USD 342M → 2033: USD 1.63B | IMARC Group | ✅ Reconocida en el sector |
| Dengue MX: 29 → 279 casos/100K (2022-2024) | PLOS ONE, peer-reviewed | ✅ Publicación científica |
| MMR MX: 48.7 → 72.4/100K (2019-2022) | PMC / UNAM | ✅ Publicación científica |
| IMSS: solo 3% diabéticos con atención interdisciplinaria | Commonwealth Fund 2026 | ✅ Fuente reconocida |
| 80% sin cobertura en Chiapas/Oaxaca/Guerrero | INEGI / Mexico Business News | ✅ Citado en múltiples fuentes |
| Credencial Universal de Salud lanzada abril 2026 | Mexico Business News | ✅ Noticia verificable |
| Reforma Ley General de Salud aprobada Senado | SaludDigital.com / Consultor Salud | ✅ Verificable en Gaceta del Senado |
| World Bank National Health Compact dic 2025 | World Bank docs | ✅ Documento público |
| IMSS: 22% crecimiento consultas especialidad 2025 | Mexico Business News | ⚠️ Validar cifra exacta en comunicado IMSS |
| Reducción 23% hospitalizaciones con predicción | "análisis interno IMSS 2025" | ❌ No verificado — eliminar o buscar fuente |
| Lista de espera endocrinología: "semanas a meses" | The Lancet / Commonwealth Fund | ⚠️ General, no específico para IMSS |

> **Acción:** Eliminar o reemplazar el dato "23% hospitalizaciones IMSS" antes de
> presentar al cliente. No tiene respaldo de fuente externa verificable.

---

*Documento interno — Blend360 AI Solutions — Junio 2026*
*No compartir sin revisión del área comercial y técnica*
