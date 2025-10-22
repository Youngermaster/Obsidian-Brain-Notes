# Resumen Ultra-Compacto: Sistemas Distribuidos + Docker + Kubernetes

## 📚 Capítulos del Libro (Tanenbaum)

### Cap 1: Introducción

**Qué es**: Sistema de computadoras independientes que parece uno solo. **Objetivos**: Transparencia, escalabilidad, tolerancia a fallas. **Desafío clave**: No hay reloj global, componentes fallan independientemente.

### Cap 2: Arquitecturas

- **Cliente-Servidor**: Cliente pide, servidor responde. Simple pero cuello de botella.
- **Three-Tier**: UI + Lógica + Datos. Mejor escalabilidad.
- **P2P**: Todos iguales. Súper escalable, difícil de controlar.
- **Microservicios**: Servicios pequeños e independientes. Complejo pero flexible.
- **Event-Driven**: Comunicación por eventos. Desacoplado, ideal para escala masiva.

### Cap 3: Procesos

- **Threads**: Concurrencia para ocultar latencias.
- **Virtualización**: VMs (pesadas) vs Contenedores (ligeros).
- **Servidores**: Sin estado = escalable, Con estado = eficiente pero difícil escalar.

### Cap 4: Comunicación

- **RPC**: Llamadas remotas como locales. Síncrono, simple.
- **MOM** (Kafka/RabbitMQ): Mensajes asíncronos. Confiable, desacoplado.
- **Streaming**: Para datos continuos (video, IoT).

### Cap 5: Naming

- **DNS**: Nombres jerárquicos. Probado y funciona.
- **DHT**: Para P2P. Descentralizado, O(log N) búsquedas.
- **Service Discovery**: Encontrar servicios dinámicamente.

### Cap 6: Coordinación

- **Sincronización**: Lamport (orden parcial) vs Vector Clocks (causalidad completa).
- **Exclusión Mutua**: Centralizado (simple, SPOF) vs Distribuido (robusto, costoso).
- **2PC**: Transacciones atómicas. Bloquea si coordinador falla.
- **Deadlocks**: Prevención con timeouts u ordenamiento.

### Cap 7: Consistencia

- **Fuerte** (Sequential): Todos ven mismo orden. Costoso.
- **Causal**: Solo eventos relacionados en orden. Balance.
- **Eventual**: Converge eventualmente. Alta disponibilidad.
- **Primary-Backup**: Un maestro, N réplicas. Simple.
- **Quorum** (N, W, R): Flexible. W+R > N garantiza consistencia.
- **Teorema CAP**: Solo 2 de 3: Consistencia, Disponibilidad, Tolerancia a Particiones.

---

## 🐳 Docker

**Qué es**: Empaqueta apps en contenedores portables.

**Ventajas vs VMs**:

- Arranque: segundos vs minutos
- Tamaño: MBs vs GBs
- Overhead: mínimo vs significativo

**Conceptos clave**:

- **Image**: Plantilla inmutable (capas)
- **Container**: Instancia ejecutable
- **Volume**: Persistencia de datos
- **Network**: Bridge, host, overlay

**Best practices**:

- Una imagen por servicio
- Inmutabilidad (no modificar en runtime)
- Multi-stage builds (imágenes pequeñas)
- No-root user

---

## ☸️ Kubernetes

**Qué es**: Orquestador de contenedores. Docker solo maneja un host, K8s maneja clusters.

**Arquitectura**:

- **Control Plane**: API Server, etcd (estado), Scheduler, Controllers
- **Nodes**: Kubelet (agente), Container Runtime, Kube-proxy (red)

**Recursos clave**:

|Recurso|Qué hace|Cuándo usar|
|---|---|---|
|**Pod**|1+ contenedores juntos|Unidad básica|
|**Deployment**|Gestiona réplicas, updates|Apps stateless|
|**StatefulSet**|Identidad estable, orden|Bases de datos, caches|
|**Service**|Load balancer + DNS|Exponer apps|
|**Ingress**|HTTP routing + SSL|Entry point único|
|**ConfigMap**|Configuración|Variables no sensibles|
|**Secret**|Datos sensibles|Passwords, API keys|
|**PV/PVC**|Almacenamiento|Datos persistentes|
|**HPA**|Auto-scaling|Escalar por métricas|

**Patrones**:

- **Rolling Update**: Sin downtime, gradual
- **Blue-Green**: Cambio instantáneo, fácil rollback
- **Canary**: Testing gradual (10% tráfico primero)

---

## 🏢 Data Centers

**Componentes**:

- **Servidores**: Racks de 42U
- **Red**: Spine-Leaf (moderno, sin bloqueo) > Three-Tier (tradicional)
- **Storage**: SAN/NAS/Object Storage
- **Tiers**: I-IV (99.671% - 99.995% uptime)

**Key**: Spine-Leaf = latencia predecible, escalable horizontalmente.

---

## 🎯 Casos de Uso: ¿Cuándo usar qué?

### Caso 1: **Startup MVP** 🚀

**Necesidad**: Lanzar rápido, pocos usuarios, presupuesto limitado.

**Arquitectura**:

- Cliente-Servidor tradicional
- Monolito en Docker
- Deploy: Docker Compose en VPS único

```yaml
# docker-compose.yml
services:
  app:
    image: myapp
    ports: ["80:3000"]
  db:
    image: postgres
    volumes: ["db-data:/var/lib/postgresql/data"]
```

**Por qué**:

- ✅ Simple y rápido
- ✅ Bajo costo ($5-20/mes)
- ✅ Fácil debugging
- ❌ No escala (pero no necesitas aún)

**Cuándo migrar**: >10k usuarios activos o necesitas HA.

---

### Caso 2: **E-commerce Mediano** 🛒

**Necesidad**: 50k-500k usuarios, alta disponibilidad, Black Friday sin caerse.

**Arquitectura**:

- Three-Tier en Kubernetes
- Servicios: Frontend, API, Database, Cache
- Multi-zona para HA

```yaml
# Frontend: 5 réplicas
# API: 10-50 réplicas (HPA)
# PostgreSQL: Primary + 2 replicas
# Redis: 3 nodos cluster
```

**Por qué**:

- ✅ Escala automáticamente (HPA)
- ✅ Tolerancia a fallas (multi-zona)
- ✅ Zero-downtime deploys
- ✅ Capas escalables independientemente
- ❌ Complejidad moderada
- ❌ Costo medio ($500-2000/mes)

**Cuándo migrar a algo más**: >1M usuarios o necesitas microservicios.

---

### Caso 3: **SaaS Empresarial B2B** 🏢

**Necesidad**: Multi-tenancy, compliance (GDPR, SOC2), SLAs 99.9%, audit logs.

**Arquitectura**:

- Microservicios + Event-Driven
- Kubernetes multi-namespace (tenant por namespace)
- Service Mesh (Istio) para mTLS y observability

```
Servicios independientes:
- Auth Service (OIDC)
- Tenant Service
- Billing Service  
- Analytics Service
- Notification Service

Comunicación:
- Síncronos: gRPC
- Asíncronos: Kafka

Storage:
- PostgreSQL por tenant (aislamiento)
- S3 para archivos
- Redis para sessions
```

**Por qué**:

- ✅ Aislamiento fuerte (seguridad)
- ✅ Equipos independientes por servicio
- ✅ Escala servicios individualmente
- ✅ Auditoría completa (logs, traces)
- ✅ mTLS automático (Istio)
- ❌ Alta complejidad
- ❌ Requiere equipo DevOps dedicado
- ❌ Costo alto ($5k-20k/mes)

**Cuándo**: Desde el inicio si compliance es crítico.

---

### Caso 4: **Red Social / App Viral** 📱

**Necesidad**: Escala masiva (millones usuarios), viral spikes, eventual consistency OK.

**Arquitectura**:

- Microservicios + Event-Driven + CQRS
- P2P para distribución de contenido
- Multi-región global

```
Características:
- Arquitectura: Event-Driven (Kafka)
- Consistencia: Eventual (Cassandra, DynamoDB)
- Cache: Redis agresivo (Memcached)
- CDN: CloudFlare, Akamai
- Read/Write separation (CQRS)

Ejemplo flujo:
1. User posts → Kafka
2. Fanout service → Follower feeds (async)
3. Reads from cache (99%)
4. Write to Cassandra (quorum)
```

**Por qué**:

- ✅ Escala casi infinita
- ✅ Alta disponibilidad (AP en CAP)
- ✅ Resiste viral spikes
- ✅ Multi-región (baja latencia global)
- ❌ Eventual consistency (posts tardan segundos en aparecer)
- ❌ Complejidad extrema
- ❌ Costo muy alto ($50k-500k/mes)

**Pattern clave**: Sacrificar consistencia fuerte por disponibilidad.

---

### Caso 5: **Banco / Fintech** 🏦

**Necesidad**: Consistencia FUERTE, transacciones ACID, compliance extremo.

**Arquitectura**:

- Monolito modular o microservicios selectivos
- Consistencia fuerte (CP en CAP)
- Primary-Backup replication
- Multi-DC sync

```
Core Banking:
- Monolito para transacciones (ACID)
- PostgreSQL con replicación síncrona
- 2PC para transacciones distribuidas

Servicios auxiliares:
- Microservicios para: notificaciones, analytics
- Eventual consistency permitido aquí

Comunicación:
- Síncronos: REST/gRPC (no async para core)
```

**Por qué**:

- ✅ Consistencia fuerte garantizada
- ✅ Auditoría completa
- ✅ Rollback de transacciones
- ❌ Menor disponibilidad (preferimos consistencia)
- ❌ No escala como red social
- ❌ Latencia mayor

**Pattern clave**: **CP en CAP**. Consistencia > Disponibilidad.

---

### Caso 6: **IoT / Real-Time Analytics** 📊

**Necesidad**: Millones de dispositivos, streaming de datos, analytics en tiempo real.

**Arquitectura**:

- Event-Driven + Stream Processing
- Lambda Architecture (batch + streaming)

```
Ingestion:
- Kafka (buffer infinito)
- MQTT para IoT devices

Processing:
- Kafka Streams / Flink (real-time)
- Spark (batch)

Storage:
- TimescaleDB (time-series)
- S3 (data lake)
- Redis (hot data)

K8s:
- StatefulSets para Kafka
- Deployments para processors
- HPA basado en lag de Kafka
```

**Por qué**:

- ✅ Maneja millones eventos/seg
- ✅ Procesamiento real-time y batch
- ✅ Escalable horizontalmente
- ❌ Complejidad de stream processing
- ❌ Requiere expertise en Kafka/Flink

---

### Caso 7: **Marketplace (Uber, Airbnb)** 🚗

**Necesidad**: Matching en tiempo real, geo-distribuido, alta disponibilidad.

**Arquitectura**:

- Microservicios + Event-Driven
- Geo-sharding
- Multi-región activo-activo

```
Servicios clave:
- User Service (global)
- Matching Service (geo-sharded)
- Payment Service (strong consistency)
- Notification Service (eventual)

Geo-sharding:
- Users en región más cercana
- Matching por ciudad (latencia <100ms)
- Replicación cross-region para failover

Comunicación:
- WebSockets para real-time
- Kafka para eventos
- gRPC entre servicios
```

**Por qué**:

- ✅ Baja latencia (geo-distributed)
- ✅ Escala por región
- ✅ Resiliente a fallas regionales
- ❌ Complejidad de geo-replication
- ❌ CAP challenges (eventual consistency regional)

---

## 📊 Tabla de Decisión Rápida

|Requisito|Arquitectura|Comunicación|Consistencia|Infra|
|---|---|---|---|---|
|**Simplicidad**|Monolito|REST|Strong|Docker Compose|
|**Escalabilidad**|Microservicios|gRPC + Kafka|Eventual|K8s + HPA|
|**Disponibilidad 99.99%**|Microservicios|Event-Driven|Eventual|K8s Multi-zona|
|**Transacciones ACID**|Monolito/Modular|Síncronos|Strong (2PC)|K8s + StatefulSet|
|**Viral spikes**|Event-Driven|Kafka|Eventual|K8s + HPA agresivo|
|**Compliance estricto**|Monolito modular|REST/gRPC|Strong|K8s + NetworkPolicies|
|**Global baja latencia**|Microservicios|gRPC|Eventual|Multi-región K8s|
|**IoT/Streaming**|Event-Driven|MQTT/Kafka|Eventual|K8s + Kafka cluster|

---

## 🎓 Reglas de Oro para el Examen

### 1️⃣ **Consistencia vs Disponibilidad (CAP)**

```
Necesitas transacciones exactas (banco)? 
→ CP (Strong consistency + Primary-Backup)

Necesitas siempre responder (red social)?
→ AP (Eventual consistency + Quorum)
```

### 2️⃣ **Comunicación**

```
Respuesta inmediata necesaria?
→ RPC/gRPC (síncrono)

Puede esperar? Alta confiabilidad?
→ MOM/Kafka (asíncrono)
```

### 3️⃣ **Arquitectura**

```
Equipo pequeño (<5)? App simple?
→ Monolito

Equipos grandes? Escala diferenciada?
→ Microservicios

Picos impredecibles? Desacoplamiento total?
→ Event-Driven
```

### 4️⃣ **Infraestructura**

```
Desarrollo local? Testing?
→ Docker Compose

Producción? Escalabilidad? HA?
→ Kubernetes

Millones usuarios? Multi-región?
→ K8s + Service Mesh + Multi-cloud
```

### 5️⃣ **Estado**

```
Stateless (API, web servers)?
→ Deployment (escala fácil)

Stateful (DB, cache)?
→ StatefulSet (identidad estable)

Batch jobs?
→ Job/CronJob
```

---

## 💡 Tips Finales

**Del libro a K8s**:

- **Naming (Cap 5)** → Service Discovery automático en K8s
- **Coordinación (Cap 6)** → etcd implementa consenso (Raft)
- **Replicación (Cap 7)** → ReplicaSets + StatefulSets
- **Tolerancia a fallas** → Health checks + auto-healing
- **Transparencia** → Services abstraen endpoints

**Patrón más común en producción**:

```
Frontend (Deployment) 
  ↓ REST
API Gateway (Deployment)
  ↓ gRPC
Microservicios (Deployments) 
  ↓ Kafka
Event Processors (Deployments)
  ↓
Databases (StatefulSets)
```

**Errores comunes**: ❌ Microservicios para apps pequeñas (overkill) ❌ Consistencia fuerte cuando no se necesita (sacrificas disponibilidad) ❌ No usar health checks en K8s ❌ Stateful apps en Deployments (usa StatefulSet) ❌ Secrets en ConfigMaps (usa Secrets)

✅ **Start simple, evolve based on needs**
