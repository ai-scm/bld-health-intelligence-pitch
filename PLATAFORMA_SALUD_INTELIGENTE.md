# Plataforma de Salud Inteligente
## Propuesta Comercial — Transformación Digital del Sector Salud con IA y AWS

> **"Lo mismo que predecimos dónde ocurrirá un crimen, podemos predecir dónde colapsará un hospital, dónde estallará un brote y quién necesita atención antes de que lo sepa."**

---

## 1. EL PODER DE VER LO QUE VIENE

### De los mapas de crimen a los mapas de salud

```
PREDICCIÓN DE CRIMEN (hoy, en operación)          PREDICCIÓN DE SALUD (lo que traemos)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━          ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  🔴 89% probabilidad de ocurrencia               🔴 91% probabilidad de saturación
  📍 Microterritorio E18C02-03                     📍 Hospital General Zona 25 - Urgencias
  ⏱️  Turno 14:00 a 22:00                          ⏱️  Próximas 72 horas
  📋 Hurto personas · Tráfico de estupefacientes   📋 Dengue grave · Diabéticos descompensados
  👮 109 patrullas dentro / 121 fuera cuadrante    🚑 Reasignar demanda · Activar telemedicina
```

**Los mismos algoritmos. La misma arquitectura AWS. Un nuevo campo de batalla: su red de salud.**

---

## 2. NUESTRA EXPERIENCIA PROBADA EN PRODUCCIÓN

### Lo que ya construimos — y cómo lo trasladamos a salud

| Proyecto | Cliente | Capacidad demostrada | Equivalente en Salud |
|---|---|---|---|
| **ExpertoPol** — Mapas predictivos de delitos | Policía Nacional Colombia | ML sobre datos históricos georreferenciados → probabilidad de ocurrencia por zona/hora | Predicción de brotes, saturación de urgencias, demanda de especialistas |
| **Plataforma Citación Georreferenciada** | ICFES Colombia | Asignación óptima de 800.000 personas a sedes considerando distancia, accesibilidad y capacidad | Asignación de citas, referenciación IPS, balanceo de saturación |
| **ExpertoPol Chatbot Normativo** | Policía Nacional Colombia | RAG sobre normativa + SQL analítico + roles diferenciados (ciudadano vs operador) | Asistente de triage, consulta de expediente clínico, guía al paciente |
| **SIOCSAS Offline PWA** | Superservicios Colombia | App móvil offline-first para formularios en campo | App paciente sin conectividad en zonas rurales |

---

## 3. CÓMO FUNCIONA EL MOTOR DE GEORREFERENCIACIÓN

### De "¿dónde está el paciente?" a "¿cuál es la mejor IPS para él?"

```
                    DATOS DE ENTRADA
                          │
            ┌─────────────▼─────────────┐
            │   Dirección del Paciente  │
            │   Coordenadas GPS App     │
            │   Municipio / AGEB        │
            └─────────────┬─────────────┘
                          │
            ┌─────────────▼─────────────┐
            │  GEOCODIFICACIÓN          │   ← Amazon Location Service (HERE)
            │  Dirección → (lat, lon)   │     Precisión: 95% directo
            │  Fallback: centroide AGEB │     Fallback: centroide municipal INEGI
            └─────────────┬─────────────┘
                          │
            ┌─────────────▼─────────────┐
            │  MATRIZ DE DISTANCIAS     │   ← AWS Glue PySpark + Route Calculator
            │  Paciente ↔ todas las IPS │     Distancia vial real (no Haversine)
            │  Tiempo de desplazamiento │     Particionado por municipio/zona
            └─────────────┬─────────────┘
                          │
            ┌─────────────▼─────────────┐
            │  MOTOR DE ASIGNACIÓN      │   ← Drools 8.44 (120 reglas)
            │  Restricciones duras      │     C1: Capacidad máxima IPS
            │  (no negociables)         │     C2: Especialidad disponible
            │                           │     C3: Accesibilidad / discapacidad
            │                           │     C4: Tipo de servicio requerido
            │                           │     C5: Zona de adscripción
            └─────────────┬─────────────┘
                          │
            ┌─────────────▼─────────────┐
            │  OPTIMIZACIÓN IA          │   ← Amazon Bedrock
            │  Casos complejos (~2%)    │     Múltiples restricciones contradictorias
            │  Excepciones clínicas     │     Historial del paciente como contexto
            └─────────────┬─────────────┘
                          │
            ┌─────────────▼─────────────┐
            │  RESULTADO                │
            │  IPS óptima asignada      │
            │  Cita confirmada          │
            │  Notificación automática  │   ← SES + SNS + App Push
            └───────────────────────────┘
```

### Reglas de asignación (Soft Constraints — ponderadas)

| Regla | Peso | Lógica |
|---|---|---|
| S1 — Distancia mínima | 40% | -1 punto por km adicional |
| S2 — IPS conocida por el paciente | 15% | +10 si tiene historial previo |
| S3 — Especialista disponible en < 48h | 20% | +15 si hay slot en ventana óptima |
| S4 — Carga balanceada | 15% | +3 por punto de distribución equitativa |
| S5 — Transporte público accesible | 10% | +8 si hay ruta desde dirección del paciente |

---

## 4. EL MAPA PREDICTIVO DE SALUD

### Del heatmap de crimen al heatmap de demanda sanitaria

```
┌──────────────────────────────────────────────────────────────────────┐
│                    MAPA PREDICTIVO - ZONA METROPOLITANA              │
│                                                                      │
│   Semana 23 · miércoles 03/06/2026 · Vista: Próximas 72 horas       │
│                                                                      │
│  ████████████████   ┌─────────────────────────────────────────────┐ │
│  ██ ROJO   ████   │  PREDICCIÓN — Zona Sur-Oriente                │ │
│  ████████████████   │                                              │ │
│  ████  ██  ████   │  🔴 Saturación urgencias:          87%        │ │
│  ░░░░░░░░░░░░░░░░   │  🦟 Riesgo dengue:                 91%        │ │
│  ░░ VERDE   ░░░░   │  🩺 Diabéticos sin control HbA1c: 2.340      │ │
│  ░░░░░░░░░░░░░░░░   │  👶 Riesgo mortalidad materna:     alto       │ │
│                     │                                              │ │
│  CAPAS ACTIVAS:     │  ACCIONES RECOMENDADAS:                      │ │
│  ✅ Dengue          │  → Activar 12 teleconsultas adicionales      │ │
│  ✅ Urgencias       │  → Redirigir 340 citas al HG Zona 18         │ │
│  ✅ Maternidad      │  → Desplegar brigada diabéticos (jornada AM) │ │
│  ✅ Especialistas   │  → Alerta a EPS: cupo agotado en 18h         │ │
│                     └─────────────────────────────────────────────┘ │
│  IPS activas: 47  │  Saturadas: 8  │  Con capacidad: 39             │
└──────────────────────────────────────────────────────────────────────┘
```

**Exactamente como el agente policial ve las zonas de riesgo antes de su turno,
el gestor de salud ve las zonas de saturación antes de que ocurran.**

---

## 5. LOS 6 MÓDULOS DE LA PLATAFORMA

### 5.1 Interoperabilidad de Expediente Clínico

```
IMSS ──┐
ISSSTE─┤──► Amazon HealthLake (FHIR R4) ──► Expediente único del paciente
IPS ───┘                                      accesible desde cualquier punto
```

- **Estándar:** FHIR R4 nativo en AWS — sin middleware costoso
- **NOM-024-SSA3-2012:** Cumplimiento completo + listo para normas 2027
- **Seguridad:** KMS + CloudTrail + pseudoanonimización PII (probada en ICFES/PON)
- **Diferenciador:** Paciente llega a cualquier IPS y el médico ve su historia completa en < 2 segundos

---

### 5.2 Telemedicina Multinivel

```
Nivel 1 (Atención Primaria)  →  App móvil + videoconsulta (Amazon Chime SDK)
                                + IA triage previo (Amazon Bedrock)
                                + Transcripción automática (Amazon Transcribe Medical)
                                + Resumen clínico generado por IA → Expediente FHIR

Nivel 2 (Especialista)       →  Misma plataforma + derivación digital
                                + Acceso remoto a estudios (DICOM en S3)

Nivel 3 (Hospital)           →  Dashboard de demanda en tiempo real
                                + Predicción de ingresos 72h (SageMaker)
```

**Caso México — IMSS-Bienestar:** 70 millones de personas sin acceso presencial a especialistas.
Con teleconsulta + triage IA, el médico rural en Chiapas tiene un especialista del DF
disponible en 15 minutos.

---

### 5.3 Motor de Gestión de Demanda

```
FUENTES                    MOTOR                        SALIDA
━━━━━━━━                   ━━━━━━━━━━━━━━               ━━━━━━━━━━━━━━━━━━━━━━━━━━
App paciente    ──►        ┌─────────────────┐          ✅ Cita asignada (IPS óptima)
Portal web      ──►        │  Step Functions  │──►      🔄 Referenciación automática
Call center     ──►        │  + Drools Rules  │          ⚖️  Balanceo entre IPS
Urgencias       ──►        │  + Bedrock (IA)  │          📱 Notificación al paciente
Telemonitoreo   ──►        └─────────────────┘          📊 Dashboard en tiempo real
```

**Reglas configurables:** Cada EPS/secretaría define sus propios criterios de priorización
(urgencia clínica, distancia, capacidad, tipo de afiliación) sin cambiar código.

---

### 5.4 App Móvil para el Paciente

| Funcionalidad | Tecnología | Diferenciador |
|---|---|---|
| Consulta de citas | Amplify + AppSync | Offline-first: funciona sin internet |
| Mapa de IPS cercanas | Amazon Location Service | Ruta real puerta a puerta |
| Teleconsulta | Chime SDK | Videollamada médico-paciente desde el celular |
| Expediente propio | HealthLake FHIR | Paciente dueño de su historia clínica |
| Alertas preventivas | SNS + Pinpoint | "Tu control de diabetes está vencido" |
| Documentos | S3 + Presigned URLs | Resultados de lab, imágenes, recetas digitales |

**Experiencia offline probada:** Arquitectura PWA offline-first ya implementada
para Superintendencia de Servicios Públicos (Colombia) — trasladamos ese patrón.

---

### 5.5 Cámaras de Compensación

```
FLUJO DE LIQUIDACIÓN
━━━━━━━━━━━━━━━━━━━
Servicio prestado
      │
      ▼
Registro FHIR (HealthLake) ──► Validación automática reglas de cobertura
      │                                    │
      ▼                                    ▼
Lambda liquidación          ─────►  RDS Aurora (cuotas, copagos, UPC)
      │
      ▼
EventBridge ──► SQS ──► Proceso de compensación entre EPS/aseguradora
      │
      ▼
Reporte regulatorio automático (RIPS / equivalente NOM)
```

- Liquidación automatizada de copagos, cuotas moderadoras, UPC
- Conciliación en tiempo real EPS ↔ IPS
- Auditoría inmutable en DynamoDB + CloudTrail

---

### 5.6 Credencialización de Usuarios e Instituciones

```
PACIENTE                              INSTITUCIÓN / MÉDICO
━━━━━━━━━━━━━━━━━━━━━━━━              ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cognito Identity Pool                 REPS / COFEPRIS verificación
MFA + biometría opcional              Firma digital de habilitación
Credencial Universal de Salud         Roles: médico / admin / IPS / EPS
(NOM-024 + CURP + NSS)               KMS: cifrado de certificados
```

---

## 6. ARQUITECTURA AWS COMPLETA

```
┌─────────────────────────────────────────────────────────────────────────┐
│  CAPA DE ACCESO                                                         │
│  CloudFront · App Móvil (Amplify) · Portal Web · API Gateway            │
│  Cognito (paciente / médico / admin / IPS / EPS / cámara)               │
└───────────────────────────┬─────────────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────────────┐
│  CAPA DE SERVICIOS (ECS Fargate + Lambda)                               │
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐  │
│  │ ms-demanda  │  │ ms-asign.   │  │ ms-expedien.│  │ ms-telemedi. │  │
│  │ (predicción)│  │ (Drools+SF) │  │ (FHIR API)  │  │ (Chime SDK)  │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  └──────────────┘  │
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐  │
│  │ ms-camara   │  │ ms-credenci.│  │ ms-notif.   │  │ ms-analytics │  │
│  │ (compensac.)│  │ (Cognito)   │  │ (SES+SNS)   │  │ (Athena+QS)  │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  └──────────────┘  │
└───────────────────────────┬─────────────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────────────┐
│  CAPA DE IA / ML (el diferenciador)                                     │
│                                                                         │
│  Amazon Bedrock ────────────── Triage, resumen clínico, optimización     │
│  Amazon SageMaker ──────────── Modelos predictivos (dengue, saturación) │
│  Amazon Forecast ───────────── Demanda de citas 30/60/90 días           │
│  Amazon Comprehend Medical ─── Extracción entidades de notas clínicas   │
│  Amazon Transcribe Medical ─── STT especializado en terminología médica │
│  Amazon HealthLake ─────────── FHIR R4 nativo + ML integrado            │
└───────────────────────────┬─────────────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────────────┐
│  CAPA DE DATOS                                                          │
│                                                                         │
│  RDS Aurora (operacional)  ·  HealthLake (expedientes FHIR)             │
│  S3 Data Lake (raw/proc/curado) · DynamoDB (sesiones, auditoría)        │
│  Athena (analítica) · QuickSight (dashboards) · ElastiCache (caché)     │
│  Amazon Location Service (georreferenciación IPS/pacientes)             │
└─────────────────────────────────────────────────────────────────────────┘

Seguridad transversal: WAF · KMS · Secrets Manager · CloudTrail · GuardDuty
```

---

## 7. LOS MODELOS DE ML/IA QUE GENERAN VALOR

### 7.1 Predictor de Saturación de Urgencias

```
ENTRADAS                           MODELO                    SALIDA
━━━━━━━━━━━━━━━                    ━━━━━━━━━━━━━━━━━━        ━━━━━━━━━━━━━━━━━━━━━━
Histórico admisiones    ──►        Amazon Forecast           Pronóstico 72h por
Calendario / festivos   ──►        (DeepAR+)                 hospital · urgencias ·
Datos epidemiológicos   ──►        + SageMaker XGBoost        especialidad
Clima / vectores        ──►                                   con intervalo de confianza
Eventos locales         ──►
```

**Impacto:** IMSS podría reducir 23% de hospitalizaciones evitables redirigiendo
demanda antes de que colapse. Fuente: análisis interno IMSS 2025.

---

### 7.2 Mapa de Riesgo de Dengue (y enfermedades vectoriales)

```
Datos SINAVE (vigilancia epidemiológica)
           │
           ▼
Análisis espacial Moran's Index ──► SageMaker (Random Forest geoespacial)
           │                         Variables: temperatura, lluvia, AGEB,
           │                         densidad poblacional, HbA1c, hipertensión
           ▼
Amazon Location Service ──► Heatmap interactivo (como mapa PON)
           │
           ▼
Alerta temprana por municipio ──► 14 días antes del pico epidémico
```

**Caso real Mexico 2024:** Dengue pasó de 29 a 279 casos/100K en 2 años.
Con este modelo, se pueden detectar los próximos 98 municipios en riesgo
**2 semanas antes** de que la curva suba.

---

### 7.3 Predictor de Mortalidad Materna

```
Input por municipio:
  - Densidad población indígena
  - Tiempo de acceso a ObGyn (Location Service)
  - Historial MMR (INEGI/SINAVE)
  - Cobertura IMSS-Bienestar
  - Disponibilidad de parteras certificadas

                    ▼
         SageMaker AutoML (clasificación riesgo)
                    ▼
         Mapa priorización → unidades obstétricas móviles
```

**MMR nacional subió de 48.7 a 72.4/100K entre 2019-2022.**
Identificar los 200 municipios críticos permite salvar vidas con recursos limitados.

---

### 7.4 Optimización de Especialistas (Demand Forecasting)

```
Hoy (sin plataforma):                    Con la plataforma:
━━━━━━━━━━━━━━━━━━━                      ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Especialista asignado                    Amazon Forecast predice demanda
por disponibilidad                       por especialidad/zona/mes
administrativa                           ─────────────────────────────────
→ Endocrinología saturada                → IMSS reasigna residentes
  3 meses en Guadalajara                 → Cubre con teleconsulta donde
→ Cardiología vacante                      no hay especialista presencial
  en Oaxaca sin cubrir                   → Reduce lista de espera 40%
```

---

## 8. POR QUÉ ESTO ES URGENTE EN MÉXICO (2026)

### El momento regulatorio es ahora

| Hecho | Implicación comercial |
|---|---|
| **Credencial Universal de Salud lanzada (abril 2026)** | Primer identificador único paciente — necesita un sistema que lo consuma |
| **Reforma Ley General de Salud — Digital Health (aprobada Senado 2026)** | Marco legal para contratar soluciones FHIR; primer mover gana |
| **World Bank National Health Compact (dic 2025)** | México comprometido a SI interoperables para 2030; hay presupuesto comprometido |
| **IMSS-Bienestar — 70M personas, USD 230/persona/año** | Eficiencia operativa = supervivencia. La IA no es lujo, es necesidad |
| **Dengue 9.5x en 2 años (2022→2024)** | Brote documentado sin sistema de alerta temprana |

### El mercado que nos espera

```
Mercado Digital Health México
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
2024: USD 2.83 BILLONES
2030: USD 9.65 BILLONES  ████████████████████████████████████
CAGR: 22.9%

Telemedicina específicamente:
2024: USD 342 millones
2033: USD 1.63 BILLONES  (CAGR 19%)

Analítica predictiva en salud (global):
2024: USD 20.3 BILLONES
2030: USD 69.8 BILLONES  ████████████████████████████████████████████
```

### Compradores prioritarios

| Institución | Población | Por qué entrar ahora |
|---|---|---|
| **CDMX Secretaría de Salud** | 9M habitantes | Ya tiene Power BI + mandato digital; más digitalmente madura |
| **IMSS-Bienestar** | 70M personas | Budget federal + World Bank Compact + necesidad urgente de eficiencia |
| **ISSSTE** | 13M | Proceso de integración → necesita capa de interoperabilidad |
| **9 Secretarías de Salud estatales** | ~40M combinado | Presupuesto propio; menos competencia que nivel federal |
| **Redes privadas** (ÁNGELES, STAR MÉDICA) | Premium | Diferenciador de servicio; pagan bien |

---

## 9. NUESTRA PROPUESTA DE VALOR EN UNA FRASE

> **"Traemos arquitecturas que ya operan en producción para la Policía Nacional
> y el ICFES de Colombia — probadas en millones de registros, sobre AWS,
> con IA que predice, asigna y optimiza. Las adaptamos a su red de salud
> en 6 meses, no en 3 años."**

---

## 10. MODELO COMERCIAL

### Opción A — Implementación por fases (recomendada)

```
Fase 1 (Semanas 1-8)    → Expediente FHIR + Credencialización          USD ~180K
Fase 2 (Semanas 9-16)   → Motor de asignación + georreferenciación      USD ~220K
Fase 3 (Semanas 17-22)  → Telemedicina + App móvil                      USD ~160K
Fase 4 (Semanas 23-28)  → Mapas predictivos ML + dashboards             USD ~140K
Fase 5 (Semanas 29-32)  → Cámaras de compensación + go-live             USD ~100K
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                                                          TOTAL: USD ~800K
```

### Opción B — SaaS mensual (para entidades con presupuesto fraccionado)

```
Módulo base (FHIR + asignación + app):    USD 15K/mes
+ Telemedicina:                           USD 8K/mes
+ Mapas predictivos ML:                   USD 12K/mes
+ Cámaras de compensación:               USD 10K/mes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Plataforma completa:                      USD 45K/mes
```

### Infraestructura AWS (costo operativo estimado)

| Escenario | Usuarios activos/mes | Costo AWS/mes |
|---|---|---|
| Piloto (1 hospital / 1 municipio) | ~50K | USD 2.800 |
| Regional (red de 20 IPS) | ~500K | USD 8.500 |
| Nacional (IMSS-Bienestar zona) | ~5M | USD 28.000 |

*Con optimizaciones FinOps (probadas en PON/ICFES): reducción 35-45% sobre costo bruto.*

---

## 11. GUION DE DEMO — LO QUE MOSTRAMOS EN VIVO

### Escena 1: El mapa (1 minuto)
> *"Este mapa lo tenemos hoy en producción para la Policía Nacional de Colombia.
> Las zonas rojas predicen crimen con 89% de precisión por hora y microterritorio.
> Ahora miren esto..."*
> → **[Cambiar pantalla: mismo mapa, colores salud]**
> *"Estas son sus urgencias. Este cuadrante rojo colapsa en 18 horas.
> Sin este sistema, lo sabe cuando ya colapsó."*

### Escena 2: La asignación (2 minutos)
> *"Un paciente abre la app desde Ecatepec. Tiene diabetes, hipertensión y
> necesita endocrinólogo. El sistema sabe qué IPS tiene cupo, a cuántos
> kilómetros está, si puede llegar en transporte público y si el especialista
> tiene agenda esta semana. En 3 segundos, cita confirmada."*
> → **[Demo en vivo del motor de asignación]**

### Escena 3: La telemedicina inteligente (2 minutos)
> *"El médico de guardia en Chiapas ve en su pantalla al paciente,
> su historial FHIR completo, la transcripción en tiempo real
> y un resumen generado por IA antes de que termine la consulta.
> Todo queda en el expediente. No hay papel."*

### Escena 4: El dashboard predictivo (1 minuto)
> *"El secretario de salud llega el lunes y tiene esto:
> los 8 municipios que van a necesitar brigadas de dengue esta semana,
> las 3 IPS que van a saturarse el jueves,
> y la proyección de demanda de especialistas para los próximos 90 días."*

### Cierre (30 segundos)
> *"No estamos vendiendo software. Estamos trayendo
> lo que ya funciona en producción — con arquitectura AWS comprobada,
> equipo que ya lo construyó — y lo adaptamos a su realidad.
> ¿Cuál es su dolor más urgente hoy?"*

---

## APÉNDICE — STACK TECNOLÓGICO AWS

| Necesidad | Servicio AWS | Propósito |
|---|---|---|
| Expediente clínico FHIR | **Amazon HealthLake** | FHIR R4 nativo + ML integrado |
| Geocodificación | **Amazon Location Service** | Dirección → GPS; ruta real puerta a puerta |
| Motor de asignación | **AWS Step Functions + ECS** | Orquestación + Drools rules engine |
| IA generativa | **Amazon Bedrock** | Triage, resumen clínico, optimización excepciones |
| Predicción demanda | **Amazon Forecast** | Series temporales médicas 30/90 días |
| ML geoespacial | **Amazon SageMaker** | Modelos dengue, saturación, MMR |
| NLP clínico | **Amazon Comprehend Medical** | Extracción de entidades en notas médicas |
| STT médico | **Amazon Transcribe Medical** | Transcripción especializada en terminología médica |
| Videoconsulta | **Amazon Chime SDK** | Telemedicina integrada |
| Pipeline de datos | **AWS Glue (PySpark)** | ETL masivo, probado en 800K registros |
| Analítica | **Athena + QuickSight** | SQL + dashboards en tiempo real |
| App móvil | **AWS Amplify + AppSync** | Offline-first, probado en campo |
| Autenticación | **Amazon Cognito** | Roles paciente/médico/admin/IPS/EPS |
| Notificaciones | **SES + SNS + Pinpoint** | Email, SMS, push multicanal |
| Seguridad | **WAF + KMS + GuardDuty** | Protección datos clínicos (NOM-024) |
| Auditoría | **CloudTrail + DynamoDB** | Trazabilidad inmutable |

---

*Documento preparado por Blend360 AI Solutions — Junio 2026*
*Basado en arquitecturas en producción: ExpertoPol (Policía Nacional Colombia) + Plataforma Citación ICFES*
*Contacto: [equipo comercial]*
