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

**Componentes incluidos en el precio mensual:**
- ✅ Infraestructura AWS (ver desglose en Sección 2) — se incluye como parte del servicio gestionado
- ✅ Licencia de uso de la plataforma Blend360
- ✅ Licencia HounDoc (formularios inteligentes + portal web) — valor agregado, validar tier con comercial HounDoc
- ✅ Soporte operativo nivel 1 y monitoreo 24/7
- ⚠️ Validar con comercial: ¿el cliente puede traer su propia cuenta AWS (BYOC)?

**Pendiente de validar con área comercial:**
- [ ] SLAs de disponibilidad y penalizaciones por incumplimiento
- [ ] Límites de usuarios / transacciones incluidas por tier
- [ ] Política de escalamiento de precio al superar umbrales
- [ ] Tier de licencia HounDoc según volumen de formularios activos
- [ ] ¿Aplica descuento por volumen si el cliente ya tiene AWS Enterprise Support?

---

## 2. GLOSARIO TÉCNICO CLAVE

### ¿Qué es un Expediente FHIR?

**FHIR** (*Fast Healthcare Interoperability Resources*) es el estándar internacional
para representar e intercambiar información clínica entre sistemas de salud.
Publicado por HL7 International ([hl7.org/fhir](https://hl7.org/fhir/)), la versión R4
es la más adoptada globalmente (OPS, OMS, NHS Reino Unido, CMS Estados Unidos, NOM-024 México).

Un **Expediente FHIR** es el historial clínico de un paciente estructurado según ese estándar:
cada diagnóstico, medicamento, resultado de lab, imagen, vacuna y consulta es un "recurso"
FHIR con formato estándar (JSON o XML), identificado de forma única y consultable desde
cualquier sistema que entienda el mismo estándar.

**En términos simples:** es lo mismo que usar el formato JPEG para imágenes — cualquier
programa que lo soporte puede abrirlo, sin importar quién lo creó.
Con FHIR, un paciente de IMSS que llega a una IPS privada o al ISSSTE puede ser atendido
inmediatamente porque su expediente es legible por todos los sistemas.

**Amazon HealthLake** implementa FHIR R4 de forma nativa, lo que significa que no se
requiere desarrollo adicional para cumplir el estándar. Fuente oficial:
[aws.amazon.com/healthlake](https://aws.amazon.com/healthlake/)

---

## 3. INFRAESTRUCTURA AWS — DESGLOSE DE COSTOS ESTIMADOS

> **Metodología:** Estimaciones basadas en AWS Pricing Calculator y experiencia en
> proyectos ICFES (~USD 825/convocatoria) y PON. Deben validarse con AWS Calculator
> oficial para cada configuración de cliente.
> **Región de referencia:** us-east-1 (N. Virginia). Ajustar para Mexico/São Paulo (sa-east-1).

---

### Escenario 1 — Piloto (1 hospital / 1 municipio / ~50K usuarios activos/mes)

| Servicio AWS | Qué hace | Configuración piloto | USD/mes est. |
|---|---|---|---|
| **ECS Fargate** | Ejecuta los microservicios de la plataforma sin gestionar servidores. [AWS Docs](https://aws.amazon.com/fargate/) | 4 servicios · 0.5 vCPU · 1GB · ~720h/mes | ~120 |
| **RDS Aurora PostgreSQL** | Base de datos relacional gestionada, alta disponibilidad. [AWS Docs](https://aws.amazon.com/rds/aurora/) | db.t3.medium · Single-AZ · 20 GB | ~95 |
| **Amazon HealthLake** | Almacén de datos clínicos nativo FHIR R4 con capacidades de ML integradas. [AWS Docs](https://aws.amazon.com/healthlake/) | FHIR R4 · ~50K recursos/mes · 10 GB | ~85 |
| **S3** | Almacenamiento de objetos: imágenes, documentos, backups, Data Lake. [AWS Docs](https://aws.amazon.com/s3/) | ~100 GB + backups | ~15 |
| **API Gateway** | Punto de entrada único para todas las llamadas REST/HTTP de la app y portal. [AWS Docs](https://aws.amazon.com/api-gateway/) | ~500K llamadas/mes | ~8 |
| **Lambda** | Funciones serverless: triggers S3, integraciones, lógica auxiliar. [AWS Docs](https://aws.amazon.com/lambda/) | ~1M invocaciones/mes | ~12 |
| **Amazon Cognito** | Autenticación y gestión de usuarios (paciente, médico, admin, IPS). [AWS Docs](https://aws.amazon.com/cognito/) | ~50K MAU (tier gratuito) | ~0–15 |
| **Amazon Location Service** | Geocodificación de direcciones y cálculo de rutas reales entre paciente e IPS. [AWS Docs](https://aws.amazon.com/location/) | ~10K geocodificaciones/mes (HERE) | ~5 |
| **Amazon Bedrock** | Modelos de IA generativa (triage, resumen de consulta, optimización). [AWS Docs](https://aws.amazon.com/bedrock/) | ~100K tokens/mes | ~15 |
| **Amazon Chime SDK** | Videoconsultas médico-paciente integradas en la plataforma. [AWS Docs](https://aws.amazon.com/chime/chime-sdk/) | ~2K min video/mes | ~20 |
| **Amazon SageMaker** | Entrenamiento y despliegue de modelos ML propios (predicción demanda, riesgo). [AWS Docs](https://aws.amazon.com/sagemaker/) | Endpoints de inferencia básicos (2 modelos) | ~60 |
| **Amazon Forecast** | Predicción de series temporales: demanda de citas, ocupación hospitalaria. [AWS Docs](https://aws.amazon.com/forecast/) | 2 datasets · inferencias semanales | ~35 |
| **Amazon QuickSight** | Dashboards interactivos para gestores y secretarías de salud. [AWS Docs](https://aws.amazon.com/quicksight/) | 1 autor + 5 lectores (SPICE básico) | ~38 |
| **Amazon Transcribe Medical** | Transcripción automática de consultas con vocabulario médico especializado. [AWS Docs](https://aws.amazon.com/transcribe/medical/) | ~10h/mes (piloto reducido) | ~24 |
| **Amazon Comprehend Medical** | Extracción de entidades clínicas (diagnósticos, medicamentos, síntomas) de notas. [AWS Docs](https://aws.amazon.com/comprehend/medical/) | ~50K unidades texto/mes | ~5 |
| **CloudFront** | CDN: distribución del portal web y app con baja latencia. [AWS Docs](https://aws.amazon.com/cloudfront/) | ~10 GB + ~500K requests | ~5 |
| **SES + SNS** | Notificaciones: confirmación de citas, alertas, recordatorios por email y SMS. [AWS Docs](https://aws.amazon.com/ses/) | ~5K emails + ~2K SMS | ~8 |
| **CloudWatch + X-Ray** | Monitoreo de servicios, logs, trazas de peticiones. [AWS Docs](https://aws.amazon.com/cloudwatch/) | Logs básicos + trazas | ~20 |
| **WAF** | Firewall de aplicaciones web: protege contra ataques comunes (OWASP Top 10). [AWS Docs](https://aws.amazon.com/waf/) | Reglas básicas + ~500K requests | ~15 |
| **KMS** | Cifrado de datos en reposo: expedientes, credenciales, datos PII. [AWS Docs](https://aws.amazon.com/kms/) | ~5 claves + ~100K operaciones | ~5 |
| **TOTAL ESTIMADO PILOTO** | | | **~USD 610–630/mes** |

> **Demo básico disponible para cada servicio:**
> - **HealthLake:** carga de un expediente FHIR sintético y consulta via API
> - **SageMaker:** predicción de ocupación con datos históricos de ejemplo
> - **Forecast:** curva de demanda proyectada para una especialidad
> - **QuickSight:** dashboard con mapa de IPS + indicadores de saturación
> - **Chime SDK:** videollamada de prueba en entorno sandbox
> - **Transcribe Medical:** transcripción de audio de consulta demo
> - **Location Service:** geocodificación de dirección real + ruta a IPS más cercana

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
| Piloto (1 hospital) | ~50K | ~USD 620 | ~USD 420 |
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

## 4. MERCADO PRIVADO — PENDIENTE DE VALIDACIÓN

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

## 5. VALIDACIÓN DE FUENTES — DATOS USADOS EN LA PROPUESTA

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
| Reducción 15–30% hospitalizaciones evitables con predicción de demanda | Health Affairs, Vol. 40, 2021 — estudios ER en redes públicas | ✅ Reemplazado por fuente verificable |
| Lista de espera endocrinología: "semanas a meses" | The Lancet / Commonwealth Fund | ⚠️ General, no específico para IMSS |

---

*Documento interno — Blend360 AI Solutions — Junio 2026*
*No compartir sin revisión del área comercial y técnica*
