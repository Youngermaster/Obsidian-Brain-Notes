# Data Centers, Docker y Kubernetes en Sistemas Distribuidos

## Data Centers

### Conceptos Fundamentales

Un **data center** es una infraestructura física que alberga sistemas computacionales y componentes asociados (networking, almacenamiento, energía, refrigeración).

### Arquitectura de Data Centers

#### **Componentes Clave**

1. **Servidores**: Montados en racks (típicamente 42U)
2. **Networking**:
    - **ToR (Top-of-Rack) switches**: Conectan servidores en un rack
    - **Spine-Leaf architecture**: Red moderna escalable
    - **Fat-tree topology**: Ancho de banda uniforme
3. **Almacenamiento**:
    - SAN (Storage Area Network)
    - NAS (Network Attached Storage)
    - Object Storage (como S3)
4. **Infraestructura de soporte**:
    - UPS y generadores
    - Sistemas de refrigeración (CRAC units)
    - Seguridad física

#### **Diseño de Red en Data Center**

**Topología Tradicional (Three-Tier)**

```
Core Layer (routers de alta capacidad)
    ↓
Aggregation Layer (switches agregación)
    ↓
Access Layer (ToR switches)
    ↓
Servidores
```

**Problema**: Cuello de botella en capas superiores

**Topología Moderna (Spine-Leaf)**

```
Spine switches (todos interconectados)
    ↓↓↓↓↓
Leaf switches (cada leaf conecta a TODOS los spines)
    ↓
Servidores
```

**Ventajas**:

- Latencia predecible (máximo 2 saltos)
- Sin bloqueo (non-blocking)
- Escalabilidad horizontal
- Alta disponibilidad

**Análisis**: Spine-Leaf es el estándar para data centers modernos porque elimina cuellos de botella y es fácil de escalar.

### Tiers de Data Centers

**Tier I**: Básico, sin redundancia (99.671% uptime) **Tier II**: Componentes redundantes (99.741% uptime) **Tier III**: Mantenimiento sin interrupción (99.982% uptime) **Tier IV**: Tolerante a fallas (99.995% uptime - 26 min downtime/año)

### Multi-Tenancy y Virtualización

**Desafíos**:

- Aislamiento de recursos
- Interferencia de performance (noisy neighbors)
- Seguridad entre tenants

**Soluciones**:

- Virtualización de red (VLANs, VXLANs)
- QoS y resource quotas
- Hipervisores con strong isolation

---

## Docker

### ¿Qué es Docker?

**Docker** es una plataforma de contenedorización que empaqueta aplicaciones con sus dependencias en unidades portables llamadas **contenedores**.

### Contenedores vs Máquinas Virtuales

```
┌─────────────────────┐  ┌─────────────────────┐
│   VM Architecture   │  │ Container Architecture│
├─────────────────────┤  ├─────────────────────┤
│ App A │ App B       │  │ App A │ App B       │
├───────┼─────────────┤  ├───────┼─────────────┤
│ Bins/Libs │ Bins    │  │ Bins/Libs │ Bins    │
├───────────┼─────────┤  ├─────────────────────┤
│ Guest OS  │ Guest OS│  │   Container Runtime │
├───────────┴─────────┤  │     (Docker)        │
│    Hypervisor       │  ├─────────────────────┤
├─────────────────────┤  │     Host OS         │
│      Host OS        │  ├─────────────────────┤
├─────────────────────┤  │    Infrastructure   │
│   Infrastructure    │  └─────────────────────┘
└─────────────────────┘
```

**Diferencias Clave**:

|Aspecto|VMs|Contenedores|
|---|---|---|
|Arranque|Minutos|Segundos|
|Tamaño|GBs|MBs|
|Performance|Overhead significativo|Overhead mínimo|
|Aislamiento|Fuerte (hardware)|Medio (OS-level)|
|Portabilidad|Baja|Alta|

### Arquitectura de Docker

**Componentes**:

1. **Docker Daemon**: Proceso que gestiona contenedores
2. **Docker Client**: CLI para interactuar con daemon
3. **Docker Images**: Plantillas inmutables (capas read-only)
4. **Docker Containers**: Instancias en ejecución de imágenes
5. **Docker Registry**: Repositorio de imágenes (Docker Hub)

### Conceptos Fundamentales

#### **Imágenes (Images)**

Son plantillas con capas:

```dockerfile
FROM ubuntu:20.04          # Capa base
RUN apt-get update         # Capa 1
RUN apt-get install -y python3  # Capa 2
COPY app.py /app/          # Capa 3
CMD ["python3", "/app/app.py"]  # Metadata
```

**Ventajas del sistema de capas**:

- Reutilización (capas compartidas)
- Builds incrementales
- Eficiencia de almacenamiento

#### **Contenedores**

Instancia ejecutable de una imagen con:

- Sistema de archivos aislado
- Namespaces (PID, network, mount, etc.)
- Cgroups (límites de CPU, memoria)

#### **Volúmenes**

Persistencia de datos fuera del contenedor:

```bash
# Volumen anónimo
docker run -v /data mysql

# Volumen nombrado
docker run -v mysql-data:/var/lib/mysql mysql

# Bind mount
docker run -v /host/path:/container/path app
```

#### **Networking**

**Modos**:

1. **Bridge** (default): Red privada, contenedores se comunican entre sí
2. **Host**: Usa red del host directamente (sin aislamiento)
3. **Overlay**: Comunicación entre hosts (para Swarm/Kubernetes)
4. **None**: Sin red

### Dockerfile Best Practices

```dockerfile
# Usar imágenes base específicas y ligeras
FROM python:3.9-slim

# Minimizar capas combinando comandos
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*

# Copiar requirements primero (cache de capas)
COPY requirements.txt .
RUN pip install -r requirements.txt

# Luego copiar código (cambia más frecuentemente)
COPY . /app

# Usuario no-root por seguridad
USER appuser

# Metadata
EXPOSE 8080
CMD ["python", "app.py"]
```

**Principios**:

- **Inmutabilidad**: No modificar contenedores en ejecución
- **Efímero**: Contenedores deben poder destruirse y recrearse fácilmente
- **Un proceso por contenedor**: Facilita escalamiento y debugging
- **Configuración vía variables de entorno**: Portabilidad

### Docker Compose

Orquestación de múltiples contenedores:

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://db/myapp
    depends_on:
      - db
  
  db:
    image: postgres:13
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret

volumes:
  postgres-data:
```

**Uso**: Desarrollo local, testing, aplicaciones pequeñas

---

## Kubernetes (K8s)

### ¿Qué es Kubernetes?

**Kubernetes** es una plataforma de orquestación de contenedores que automatiza despliegue, escalamiento y gestión de aplicaciones containerizadas.

### ¿Por qué Kubernetes?

Docker solo maneja contenedores en **un** host. Para producción necesitas:

- Despliegue en múltiples hosts
- Alta disponibilidad
- Auto-scaling
- Service discovery
- Load balancing
- Rolling updates
- Health checks
- Secret management

**Kubernetes resuelve todo esto.**

### Arquitectura de Kubernetes

#### **Control Plane (Master)**

1. **API Server**: Punto de entrada, expone API REST
2. **etcd**: Base de datos distribuida (key-value) para estado del cluster
3. **Scheduler**: Decide en qué nodo ejecutar pods
4. **Controller Manager**: Ejecuta controladores (ReplicaSet, Deployment, etc.)
5. **Cloud Controller Manager**: Integración con proveedores cloud

#### **Worker Nodes**

1. **Kubelet**: Agente que ejecuta en cada nodo, gestiona pods
2. **Container Runtime**: Docker, containerd, CRI-O
3. **Kube-proxy**: Maneja networking y load balancing

```
┌────────────────── Control Plane ─────────────────┐
│                                                   │
│  ┌──────────┐  ┌──────┐  ┌───────────┐          │
│  │API Server│←→│ etcd │  │ Scheduler │          │
│  └────┬─────┘  └──────┘  └───────────┘          │
│       │                                           │
│  ┌────┴──────────────────┐                       │
│  │ Controller Manager    │                       │
│  └───────────────────────┘                       │
└───────────────┬───────────────────────────────────┘
                │
    ┌───────────┼───────────┐
    │           │           │
┌───┴───┐   ┌───┴───┐   ┌───┴───┐
│ Node 1│   │ Node 2│   │ Node 3│
│       │   │       │   │       │
│┌──────┴┐  │┌──────┴┐  │┌──────┴┐
││Kubelet│  ││Kubelet│  ││Kubelet│
│└───────┘  │└───────┘  │└───────┘
│┌──────┐   │┌──────┐   │┌──────┐
││Kube- │   ││Kube- │   ││Kube- │
││Proxy │   ││Proxy │   ││Proxy │
│└──────┘   │└──────┘   │└──────┘
│           │           │
│  Pods     │  Pods     │  Pods
└───────────┴───────────┴─────────
```

### Conceptos Fundamentales

#### **1. Pod**

La unidad básica de despliegue. Agrupa uno o más contenedores.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: web
    image: nginx:1.21
    ports:
    - containerPort: 80
  - name: sidecar
    image: logging-agent
```

**Características**:

- Comparten IP y volumes
- Efímeros (mortal)
- Normalmente no se crean directamente

#### **2. ReplicaSet**

Mantiene N réplicas de un pod ejecutándose.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: web
        image: myapp:1.0
```

**Función**: Auto-healing, reemplaza pods que fallan

#### **3. Deployment**

Capa sobre ReplicaSet, maneja actualizaciones declarativas.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: web
        image: myapp:2.0
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
```

**Capacidades**:

- Rolling updates
- Rollbacks
- Scaling
- Historial de versiones

#### **4. Service**

Abstracción para exponer aplicaciones (load balancing estable).

**Tipos**:

**a) ClusterIP (default)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

- IP interna del cluster
- Solo accesible dentro del cluster

**b) NodePort**

```yaml
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080
```

- Expone en puerto de todos los nodos
- Accesible externamente vía `<NodeIP>:<NodePort>`

**c) LoadBalancer**

```yaml
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
```

- Crea load balancer externo (en cloud providers)
- IP pública automática

**d) ExternalName**

- CNAME a servicio externo

**Análisis**: Para producción en cloud, usa LoadBalancer o Ingress. Para desarrollo, NodePort es suficiente.

#### **5. Ingress**

Maneja acceso HTTP(S) externo con reglas de ruteo.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
```

**Ventajas**:

- Un punto de entrada para múltiples servicios
- SSL/TLS termination
- Path-based routing
- Name-based virtual hosting

**Requiere**: Ingress Controller (nginx, traefik, etc.)

#### **6. ConfigMap**

Configuración externalizada.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgresql://db:5432/mydb"
  log_level: "info"
  config.json: |
    {
      "feature_flags": {
        "new_ui": true
      }
    }
```

**Uso en Pod**:

```yaml
spec:
  containers:
  - name: app
    image: myapp
    envFrom:
    - configMapRef:
        name: app-config
    volumeMounts:
    - name: config
      mountPath: /config
  volumes:
  - name: config
    configMap:
      name: app-config
```

#### **7. Secret**

Similar a ConfigMap pero para datos sensibles (base64 encoded).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=  # admin en base64
  password: cGFzc3dvcmQ=  # password en base64
```

**Mejores prácticas**:

- Usar external secret managers (Vault, AWS Secrets Manager)
- Encriptar secrets en etcd
- RBAC estricto

#### **8. Persistent Volumes (PV) y Persistent Volume Claims (PVC)**

**PersistentVolume**: Almacenamiento en el cluster **PersistentVolumeClaim**: Solicitud de almacenamiento por un usuario

```yaml
# PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: fast-ssd

# Uso en Pod
spec:
  volumes:
  - name: mysql-storage
    persistentVolumeClaim:
      claimName: mysql-pvc
  containers:
  - name: mysql
    volumeMounts:
    - name: mysql-storage
      mountPath: /var/lib/mysql
```

**Storage Classes**:

- Define tipos de almacenamiento (SSD, HDD, NFS, etc.)
- Provisión dinámica

#### **9. StatefulSet**

Para aplicaciones con estado (bases de datos, caches).

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi
```

**Características**:

- Identidad de red estable (pod-0, pod-1, etc.)
- Almacenamiento persistente por pod
- Orden de despliegue/terminación
- Rolling updates ordenadas

**Diferencia con Deployment**:

- Deployment: Para apps stateless
- StatefulSet: Para apps stateful

#### **10. DaemonSet**

Ejecuta un pod en cada nodo del cluster.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: logging-agent
spec:
  selector:
    matchLabels:
      app: logging
  template:
    metadata:
      labels:
        app: logging
    spec:
      containers:
      - name: fluentd
        image: fluentd:v1
```

**Uso**: Logs, monitoreo, networking (CNI plugins)

#### **11. Job y CronJob**

**Job**: Tarea que se ejecuta hasta completarse

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: backup
spec:
  template:
    spec:
      containers:
      - name: backup
        image: backup-tool
        command: ["./backup.sh"]
      restartPolicy: OnFailure
```

**CronJob**: Job programado

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-backup
spec:
  schedule: "0 2 * * *"  # 2 AM diariamente
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: backup-tool
          restartPolicy: OnFailure
```

### Conceptos Avanzados

#### **Namespaces**

Aislamiento lógico de recursos.

```bash
kubectl create namespace production
kubectl create namespace staging
```

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    environment: prod
```

**Usos**:

- Separar ambientes (dev, staging, prod)
- Multi-tenancy
- Resource quotas por namespace
- RBAC por namespace

#### **Resource Quotas y Limits**

**ResourceQuota** limita recursos totales en namespace:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "100"
    requests.memory: 200Gi
    limits.cpu: "200"
    limits.memory: 400Gi
    pods: "50"
```

**LimitRange** establece defaults y límites por container:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 500m
    defaultRequest:
      memory: 256Mi
      cpu: 100m
    type: Container
```

#### **Health Checks**

**Liveness Probe**: ¿Está vivo el contenedor? **Readiness Probe**: ¿Está listo para recibir tráfico? **Startup Probe**: Para apps con inicio lento

```yaml
spec:
  containers:
  - name: app
    image: myapp
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 3
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
    startupProbe:
      httpGet:
        path: /startup
        port: 8080
      failureThreshold: 30
      periodSeconds: 10
```

**Tipos de probes**:

- HTTP GET
- TCP Socket
- Exec (comando)

#### **Horizontal Pod Autoscaler (HPA)**

Escala automáticamente basado en métricas.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

**Requiere**: Metrics Server instalado

#### **RBAC (Role-Based Access Control)**

Control de acceso granular.

```yaml
# Role (dentro de namespace)
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

# RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: production
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

**ClusterRole y ClusterRoleBinding**: Para recursos cluster-wide

### Patrones de Despliegue

#### **1. Rolling Update (default)**

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1        # Pods extra durante update
    maxUnavailable: 0  # Pods que pueden estar down
```

- Sin downtime
- Gradual
- Fácil rollback

#### **2. Blue-Green Deployment**

```yaml
# Blue (actual)
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
    version: blue

# Cambiar a green cuando esté listo
spec:
  selector:
    app: myapp
    version: green
```

- Cambio instantáneo
- Fácil rollback
- Requiere doble recursos temporalmente

#### **3. Canary Deployment**

```yaml
# 90% tráfico a versión estable
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-stable
spec:
  replicas: 9

# 10% tráfico a canary
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary
spec:
  replicas: 1
```

- Testing gradual
- Menor riesgo
- Requiere service mesh para control fino (Istio)

### Service Mesh (Istio)

Capa de infraestructura para comunicación service-to-service.

**Capacidades**:

- Traffic management (canary, circuit breaking)
- Security (mTLS automático)
- Observability (tracing, metrics)
- Resilience (retries, timeouts)

```yaml
# Istio VirtualService para canary
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - match:
    - headers:
        user-agent:
          regex: ".*Chrome.*"
    route:
    - destination:
        host: myapp
        subset: v2
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 90
    - destination:
        host: myapp
        subset: v2
      weight: 10
```

**Análisis**: Istio añade complejidad pero es invaluable para microservicios en producción a gran escala.

---

## Integración: Data Centers + Docker + Kubernetes + Patrones del Libro

### 1. Arquitecturas del Libro en Kubernetes

#### **Cliente-Servidor → Service + Deployment**

```yaml
# Backend (servidor)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: api
        image: backend:1.0
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - port: 8080

# Frontend (cliente)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  template:
    spec:
      containers:
      - name: web
        image: frontend:1.0
        env:
        - name: API_URL
          value: "http://backend-service:8080"
```

**Conexión con el libro**: Implementa servidor sin estado (mejor escalabilidad), con múltiples réplicas para alta disponibilidad.

#### **Three-Tier Architecture**

```yaml
# Presentation Layer
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: three-tier
spec:
  replicas: 5

---
# Application Layer
apiVersion: apps/v1
kind: Deployment
metadata:
  name: business-logic
  namespace: three-tier
spec:
  replicas: 10

---
# Data Layer
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: database
  namespace: three-tier
spec:
  replicas: 3
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      resources:
        requests:
          storage: 100Gi
```

**Conexión**: Cada capa escala independientemente. StatefulSet para datos (identidad estable).

#### **Microservicios + Event-Driven**

```yaml
# Servicio A
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: orders
        image: order-service:1.0
        env:
        - name: KAFKA_BROKERS
          value: "kafka:9092"

---
# Kafka (Message Broker)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: kafka
spec:
  serviceName: kafka
  replicas: 3
  template:
    spec:
      containers:
      - name: kafka
        image: confluentinc/cp-kafka:7.0.0

---
# Servicio B
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
spec:
  template:
    spec:
      containers:
      - name: payments
        image: payment-service:1.0
```

**Conexión con el libro**:

- MOM (Kafka) para desacoplamiento
- Event-driven architecture
- Alta escalabilidad independiente por servicio

### 2. Comunicación (Capítulo 4)

#### **RPC en Kubernetes**

```yaml
# gRPC Service
apiVersion: v1
kind: Service
metadata:
  name: grpc-service
spec:
  ports:
  - name: grpc
    port: 50051
    protocol: TCP
  selector:
    app: grpc-server

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grpc-server
spec:
  template:
    spec:
      containers:
      - name: server
        image: grpc-server:1.0
        ports:
        - containerPort: 50051
```

**Dockerfile optimizado para gRPC**:

```dockerfile
FROM golang:1.19 AS builder
WORKDIR /app
COPY . .
RUN go build -o server

FROM alpine:3.16
COPY --from=builder /app/server /server
EXPOSE 50051
CMD ["/server"]
```

**Conexión**: RPC implementado con gRPC, contenedorizado, escalado horizontalmente.

#### **MOM (Kafka/RabbitMQ)**

```yaml
# RabbitMQ
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: rabbitmq
spec:
  serviceName: rabbitmq
  replicas: 3
  template:
    spec:
      containers:
      - name: rabbitmq
        image: rabbitmq:3.11-management
        ports:
        - containerPort: 5672
        - containerPort: 15672
        env:
        - name: RABBITMQ_ERLANG_COOKIE
          valueFrom:
            secretKeyRef:
              name: rabbitmq-secret
              key: erlang-cookie
---
# Consumer
apiVersion: apps/v1
kind: Deployment
metadata:
  name: message-consumer
spec:
  replicas: 5  # Escala consumidores según carga
  template:
    spec:
      containers:
      - name: consumer
        image: consumer:1.0
        env:
        - name: RABBITMQ_HOST
          value: rabbitmq
```

**Conexión con el libro**:

- Comunicación asíncrona (Capítulo 4)
- Desacoplamiento temporal
- Tolerancia a fallas (mensajes persistentes)

### 3. Naming (Capítulo 5)

#### **DNS en Kubernetes**

Kubernetes provee DNS automático:

```
<service-name>.<namespace>.svc.cluster.local
```

**Ejemplo**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: database
  namespace: production
---
# Desde otro pod en el mismo namespace:
# Acceso: database:5432
# O completo: database.production.svc.cluster.local:5432
```

**Service Discovery**:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  # Los servicios se descubren automáticamente vía DNS
  database_url: "postgresql://database:5432/mydb"
  cache_url: "redis://redis-service:6379"
  api_url: "http://api-service:8080"
```

**Conexión con el libro**:

- DNS = naming estructurado jerárquico
- Service = abstracción sobre endpoints cambiantes
- Similar a DNS pero dentro del cluster

#### **External DNS**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  annotations:
    external-dns.alpha.kubernetes.io/hostname: myapp.example.com
spec:
  type: LoadBalancer
```

ExternalDNS actualiza automáticamente DNS provider (Route53, CloudDNS).

### 4. Coordinación (Capítulo 6)

#### **Sincronización con etcd**

Kubernetes usa **etcd** (almacenamiento clave-valor distribuido) como fuente de verdad.

**Características de etcd**:

- Raft consensus (similar a Paxos)
- Consistencia fuerte
- Watch para cambios

**Conexión**: etcd implementa coordinación distribuida, similar a algoritmos del Capítulo 6.

#### **Leader Election**

Kubernetes usa leader election para componentes del control plane:

```go
// Pseudocódigo de controller manager
leaderelection.RunOrDie(ctx, leaderelection.LeaderElectionConfig{
    Lock: &resourcelock.LeaseLock{...},
    LeaseDuration: 15 * time.Second,
    RenewDeadline: 10 * time.Second,
    RetryPeriod:   2 * time.Second,
    Callbacks: leaderelection.LeaderCallbacks{
        OnStartedLeading: func(ctx context.Context) {
            // Este nodo es líder, ejecutar trabajo
        },
        OnStoppedLeading: func() {
            // Ya no es líder
        },
    },
})
```

**Conexión**: Implementa algoritmos de elección del libro usando etcd.

#### **Distributed Locks**

```yaml
# Usando un ConfigMap como lock
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-lock
  annotations:
    control-plane.alpha.kubernetes.io/leader: '{"holderIdentity":"pod-1"}'
```

O usar herramientas externas:

- **ZooKeeper** para coordinación compleja
- **Redis** con RedLock para locks distribuidos
- **Consul** para service discovery + locks

#### **Exclusión Mutua para StatefulSets**

StatefulSets garantizan que solo un pod con cierta identidad existe:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: zookeeper
spec:
  replicas: 3
  podManagementPolicy: OrderedReady  # Secuencial
```

**Conexión**: Garantiza exclusión mutua para recursos con estado.

### 5. Consistencia y Replicación (Capítulo 7)

#### **Replicación en Kubernetes**

**ReplicaSet** implementa replicación:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: web
spec:
  replicas: 5  # 5 réplicas
```

**Modelo de consistencia**: Eventual consistency en el control plane

- Cambios pasan por API server
- Controladores convergen al estado deseado eventualmente

#### **Primary-Backup con StatefulSet**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        env:
        - name: MYSQL_REPLICATION_MODE
          value: "master"  # mysql-0 es master
        # mysql-1, mysql-2 son slaves
---
# Servicio para escrituras (solo master)
apiVersion: v1
kind: Service
metadata:
  name: mysql-master
spec:
  clusterIP: None
  selector:
    statefulset.kubernetes.io/pod-name: mysql-0

---
# Servicio para lecturas (todos)
apiVersion: v1
kind: Service
metadata:
  name: mysql-read
spec:
  selector:
    app: mysql
```

**Conexión**: Implementa Primary-Backup del Capítulo 7.

#### **Quorum-Based con Operadores**

**Ejemplo: Cassandra en K8s**

```yaml
apiVersion: cassandra.datastax.com/v1beta1
kind: CassandraDatacenter
metadata:
  name: dc1
spec:
  clusterName: my-cluster
  size: 3  # N=3
  config:
    cassandra-yaml:
      num_tokens: 16
  # Consistency level en app:
  # QUORUM = 2 (W=2, R=2)
```

**Conexión**: Implementa quorum-based replication del libro.

### 6. Tolerancia a Fallas

#### **Auto-Healing**

Kubernetes implementa auto-healing automáticamente:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: app
        image: myapp:1.0
        livenessProbe:  # Si falla, reinicia contenedor
          httpGet:
            path: /health
            port: 8080
        readinessProbe:  # Si falla, saca del load balancer
          httpGet:
            path: /ready
            port: 8080
```

**Comportamiento**:

1. Pod falla → Kubelet lo reinicia
2. Nodo falla → Scheduler crea pods en otro nodo
3. Health check falla → Tráfico se redirige

**Conexión**: Implementa detección de fallas y recuperación automática.

#### **PodDisruptionBudget**

Garantiza disponibilidad durante mantenimiento:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  minAvailable: 2  # Siempre al menos 2 pods
  selector:
    matchLabels:
      app: myapp
```

**Conexión**: Garantiza que el sistema puede tolerar K-1 fallas (donde K=minAvailable+1).

#### **Multi-Zone y Multi-Region**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 6
  template:
    spec:
      affinity:
        podAntiAffinity:  # Distribuir en zonas diferentes
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: myapp
            topologyKey: topology.kubernetes.io/zone
```

**En data center**: Pods se distribuyen en múltiples racks/zonas para tolerancia a fallas de hardware.

**Conexión**: Replicación geográfica del Capítulo 7.

### 7. Patrones de Diseño del Libro en K8s

#### **Proxy Pattern**

```yaml
# Sidecar proxy (Envoy/Istio)
apiVersion: v1
kind: Pod
metadata:
  name: app-with-proxy
spec:
  containers:
  - name: app
    image: myapp:1.0
    ports:
    - containerPort: 8080
  - name: envoy-proxy  # Sidecar
    image: envoyproxy/envoy:v1.24
    ports:
    - containerPort: 9901
```

**Función**:

- Intercepta tráfico
- Métricas, tracing
- Circuit breaking
- mTLS

**Conexión**: Implementa Proxy/Interceptor patterns del libro.

#### **Broker Pattern (Service Mesh)**

Istio actúa como broker entre servicios:

```
Service A → Envoy Proxy → Istio Control Plane → Envoy Proxy → Service B
```

- Desacopla servicios
- Service discovery
- Load balancing
- Retry logic

**Conexión**: Broker pattern del Capítulo 2.

#### **Circuit Breaker**

```yaml
# Istio DestinationRule
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: myapp-circuit-breaker
spec:
  host: myapp
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 50
        maxRequestsPerConnection: 2
    outlierDetection:
      consecutiveErrors: 5
      interval: 30s
      baseEjectionTime: 30s
      maxEjectionPercent: 50
```

**Comportamiento**: Si 5 errores consecutivos, el endpoint se saca por 30s.

**Conexión**: Tolerancia a fallas, evita cascading failures.

#### **Bulkhead Pattern**

```yaml
# Namespace y ResourceQuota
apiVersion: v1
kind: Namespace
metadata:
  name: team-a
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: team-a
spec:
  hard:
    requests.cpu: "50"
    requests.memory: 100Gi
    limits.cpu: "100"
    limits.memory: 200Gi
```

**Efecto**: Aísla recursos, fallas en team-a no afectan team-b.

**Conexión**: Bulkhead pattern para resiliencia.

#### **Saga Pattern**

Para transacciones distribuidas sin 2PC:

```yaml
# Order Service
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  template:
    spec:
      containers:
      - name: orders
        image: order-service:1.0

---
# Saga Orchestrator
apiVersion: apps/v1
kind: Deployment
metadata:
  name: saga-orchestrator
spec:
  template:
    spec:
      containers:
      - name: orchestrator
        image: saga-orchestrator:1.0
        env:
        - name: KAFKA_BROKERS
          value: kafka:9092
```

**Flujo**:

1. CreateOrder → OrderService
2. ReserveInventory → InventoryService
3. ProcessPayment → PaymentService
4. Si falla: CompensateOrder, ReleaseInventory

**Conexión**: Alternativa a 2PC del Capítulo 6, usa eventos y compensaciones.

#### **CQRS (Command Query Responsibility Segregation)**

```yaml
# Write Service (Commands)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: write-service
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: writer
        image: write-service:1.0
        env:
        - name: DB_MASTER
          value: postgres-master:5432

---
# Read Service (Queries)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read-service
spec:
  replicas: 10  # Más réplicas para lecturas
  template:
    spec:
      containers:
      - name: reader
        image: read-service:1.0
        env:
        - name: DB_REPLICA
          value: postgres-replica:5432
```

**Conexión**:

- Separa read/write paths
- Diferentes modelos de consistencia
- Optimiza para diferentes cargas

### 8. Data Center Integration

#### **Multi-Tier Deployment en DC**

```
┌─────────────────────────────────────────┐
│         Data Center Network             │
├─────────────────────────────────────────┤
│                                         │
│  ┌──────────────────────────────────┐  │
│  │   Spine-Leaf Network             │  │
│  │                                  │  │
│  │  ┌────────┐  ┌────────┐         │  │
│  │  │ Spine1 │  │ Spine2 │         │  │
│  │  └───┬────┘  └───┬────┘         │  │
│  │      │  ╲      ╱  │             │  │
│  │      │   ╲    ╱   │             │  │
│  │  ┌───┴───┐ ┌──┴───┐            │  │
│  │  │ Leaf1 │ │ Leaf2│            │  │
│  │  └───┬───┘ └──┬───┘            │  │
│  └──────┼────────┼────────────────┘  │
│         │        │                    │
│    ┌────┴────┐ ┌─┴────────┐         │
│    │ Rack 1  │ │ Rack 2   │         │
│    │         │ │          │         │
│    │ K8s     │ │ K8s      │         │
│    │ Node 1  │ │ Node 3   │         │
│    │ Node 2  │ │ Node 4   │         │
│    └─────────┘ └──────────┘         │
│                                       │
│  Storage Network (SAN/NAS)           │
│  ┌─────────┐  ┌─────────┐           │
│  │ Storage │  │ Storage │           │
│  │ Array 1 │  │ Array 2 │           │
│  └─────────┘  └─────────┘           │
└───────────────────────────────────────┘
```

#### **Placement y Topology**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: critical-app
spec:
  replicas: 6
  template:
    spec:
      affinity:
        # Distribuir entre zonas
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: critical-app
            topologyKey: topology.kubernetes.io/zone
        
        # Preferir nodos con SSD
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            preference:
              matchExpressions:
              - key: disk-type
                operator: In
                values:
                - ssd
      
      # Tolerar nodos específicos
      tolerations:
      - key: "workload"
        operator: "Equal"
        value: "high-performance"
        effect: "NoSchedule"
```

**Labels en nodos**:

```bash
kubectl label nodes node1 disk-type=ssd
kubectl label nodes node1 topology.kubernetes.io/zone=us-east-1a
kubectl label nodes node1 topology.kubernetes.io/rack=rack-1
```

#### **Storage Classes para diferentes tiers**

```yaml
# Fast SSD para bases de datos
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  iopsPerGB: "50"
  fsType: ext4

---
# HDD económico para logs
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: slow-hdd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: sc1
  fsType: ext4
```

### 9. Observability y Monitoring

#### **The Three Pillars**

**1. Logs**

```yaml
# Fluentd DaemonSet
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
spec:
  template:
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: containers
          mountPath: /var/lib/docker/containers
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: containers
        hostPath:
          path: /var/lib/docker/containers
```

**2. Metrics**

```yaml
# Prometheus ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-metrics
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    interval: 30s
```

**3. Traces**

```yaml
# Jaeger sidecar
spec:
  containers:
  - name: app
    image: myapp:1.0
  - name: jaeger-agent
    image: jaegertracing/jaeger-agent
    ports:
    - containerPort: 6831
      protocol: UDP
```

**Stack completo**: ELK (Logs) + Prometheus/Grafana (Metrics) + Jaeger (Traces)

### 10. Security Best Practices

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-app
spec:
  # Security Context
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
  
  containers:
  - name: app
    image: myapp:1.0
    
    # Container-level security
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
        add:
        - NET_BIND_SERVICE
    
    # Resource limits (evita noisy neighbors)
    resources:
      requests:
        memory: "128Mi"
        cpu: "100m"
      limits:
        memory: "512Mi"
        cpu: "500m"
    
    # Health checks
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
```

**NetworkPolicy** (firewall a nivel pod):

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-allow-frontend
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
```

---

## Resumen Comparativo Final

### ¿Cuándo usar qué?

|Necesidad|Solución|Justificación|
|---|---|---|
|App monolítica simple|Docker Compose|Suficiente, no necesitas K8s|
|Microservicios en producción|Kubernetes|Orquestación, auto-scaling, resilencia|
|CI/CD pipelines|Docker|Entornos reproducibles|
|Desarrollo local|Docker + Compose|Rápido, ligero|
|Alta disponibilidad|K8s + Multi-zone|Tolerancia a fallas de zona|
|Compliance estricto|K8s + NetworkPolicies + RBAC|Control granular|
|Latencia ultra-baja|Pods en mismo nodo + affinity|Reduce network hops|
|Aplicaciones con estado|StatefulSets + PV|Identidad estable, storage persistente|
|Batch jobs|Jobs/CronJobs|Ejecución controlada, limpieza automática|

### Arquitectura Referencia Completa

```yaml
# Multi-tier microservices en K8s
apiVersion: v1
kind: Namespace
metadata:
  name: production

---
# Frontend (stateless)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: production
spec:
  replicas: 5
  strategy:
    type: RollingUpdate
  template:
    spec:
      containers:
      - name: web
        image: frontend:2.0
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 80
        readinessProbe:
          httpGet:
            path: /ready
            port: 80

---
# Backend API (stateless)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: production
spec:
  replicas: 10
  template:
    spec:
      containers:
      - name: api
        image: api:3.0
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
        - name: REDIS_HOST
          value: redis:6379

---
# Cache (stateful pero replicado)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
  namespace: production
spec:
  serviceName: redis
  replicas: 3
  template:
    spec:
      containers:
      - name: redis
        image: redis:7-alpine
        command: ["redis-server", "--appendonly", "yes"]
        volumeMounts:
        - name: data
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 10Gi

---
# Database (stateful primary-backup)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: production
spec:
  serviceName: postgres
  replicas: 3
  template:
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 100Gi

---
# Message Queue (event-driven)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: kafka
  namespace: production
spec:
  serviceName: kafka
  replicas: 3

---
# Ingress (entry point)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: main-ingress
  namespace: production
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rate-limit: "100"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - app.example.com
    secretName: tls-secret
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api
            port:
              number: 8080
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80

---
# HPA para auto-scaling
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 10
  maxReplicas: 50
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80

---
# PodDisruptionBudget
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-pdb
  namespace: production
spec:
  minAvailable: 5
  selector:
    matchLabels:
      app: api
```

Esta arquitectura implementa:

- ✅ Three-tier architecture (Frontend, API, Database)
- ✅ Microservicios con event-driven (Kafka)
- ✅ Replicación (multiple replicas)
- ✅ Consistencia eventual (Redis, Kafka)
- ✅ Primary-backup (Postgres StatefulSet)
- ✅ Auto-scaling (HPA)
- ✅ Tolerancia a fallas (PDB, health checks)
- ✅ Service discovery (K8s DNS)
- ✅ Load balancing (Services, Ingress)
- ✅ Security (Secrets, NetworkPolicies implícitas)