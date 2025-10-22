# Resumen de Sistemas Distribuidos (Tanenbaum) - Capítulos 1-7

## Capítulo 1: Introducción a los Sistemas Distribuidos

### Conceptos Fundamentales

Un **sistema distribuido** es una colección de computadores independientes que aparece ante los usuarios como un sistema único y coherente.

**Características clave:**

- **Transparencia**: El sistema oculta que sus componentes están distribuidos (ubicación, migración, replicación, concurrencia, fallas)
- **Apertura**: Usa interfaces estándar bien definidas
- **Escalabilidad**: Puede crecer en tamaño, geográficamente y administrativamente
- **Manejo de fallas**: Debe continuar funcionando incluso cuando algunos componentes fallan

**Objetivos principales:**

1. Hacer recursos accesibles
2. Ocultar la distribución (transparencia)
3. Ser abierto y escalable
4. Ser tolerante a fallas

**Desafíos:**

- Heterogeneidad de componentes
- Falta de reloj global
- Fallas independientes de componentes
- Seguridad

## Capítulo 2: Arquitecturas

### Estilos Arquitectónicos Fundamentales

#### 1. **Arquitectura en Capas (Layered)**

Organiza el sistema en capas jerárquicas donde cada capa usa servicios de la capa inferior.

**Ventajas:** Modularidad, fácil mantenimiento **Desventajas:** Potencial ineficiencia por múltiples capas

#### 2. **Arquitectura Basada en Objetos**

Los componentes son objetos que se comunican mediante invocación de métodos.

**Ejemplo:** CORBA, Java RMI

#### 3. **Arquitectura Centrada en Datos (Data-Centered)**

Los procesos se comunican a través de un repositorio común.

**Ejemplos:** Bases de datos distribuidas, sistemas de archivos distribuidos

#### 4. **Arquitectura Basada en Eventos (Event-Based)**

Los componentes se comunican mediante publicación/suscripción de eventos.

**Ventajas:** Desacoplamiento temporal y espacial **Uso ideal:** Sistemas que requieren alta escalabilidad y componentes desacoplados

### Arquitecturas de Sistema

#### **Cliente-Servidor**

Modelo básico donde clientes solicitan servicios a servidores.

**Variantes:**

- **Servidor sin estado**: No mantiene información entre peticiones (más escalable)
- **Servidor con estado**: Mantiene contexto del cliente (más eficiente pero menos escalable)

**Análisis:** Mejor para aplicaciones tradicionales web, pero crea cuellos de botella en el servidor.

#### **Arquitectura de Tres Capas (Three-Tier)**

Separa: interfaz de usuario, lógica de aplicación, y datos.

**Ventajas:** Mejor escalabilidad y mantenibilidad que cliente-servidor de dos capas **Uso ideal:** Aplicaciones empresariales complejas

#### **Peer-to-Peer (P2P)**

Todos los nodos son iguales y pueden actuar como cliente y servidor.

**Tipos:**

- **Estructurado**: Usa DHT (Distributed Hash Tables) como Chord, Pastry
- **No estructurado**: Conexiones aleatorias, búsqueda por flooding o random walks

**Análisis:** Excelente escalabilidad y resistencia a fallas, ideal para compartir archivos y blockchain, pero difícil de gestionar y puede tener problemas de seguridad.

#### **Arquitecturas Híbridas**

Combinan elementos de diferentes arquitecturas.

**Ejemplo:** Edge-server (servidores en el borde más P2P)

### Patrones de Diseño Importantes

**Proxy**: Intermediario que representa a un objeto remoto **Broker**: Coordina comunicación entre componentes desacoplados **Interceptor**: Permite modificar el flujo de control sin cambiar el código base

## Capítulo 3: Procesos

### Hilos (Threads)

Los sistemas distribuidos usan extensivamente multithreading para:

- Manejar múltiples peticiones concurrentemente
- Ocultar latencias de red
- Mejorar el rendimiento en sistemas multiprocesador

**Modelos:**

- **Hilos a nivel de usuario**: Más rápidos, pero bloquean todo el proceso en I/O
- **Hilos a nivel de kernel**: Más pesados, pero permiten verdadero paralelismo

### Virtualización

Abstrae el hardware físico para ejecutar múltiples sistemas operativos.

**Tipos:**

1. **Virtualización de procesos**: JVM, Python interpreter
2. **Virtualización de máquinas**: VMware, VirtualBox
3. **Contenedores**: Docker (más ligero que VMs completas)

**Análisis:** Los contenedores son ideales para microservicios por su ligereza y portabilidad.

### Clientes

**Tipos:**

- **Clientes ligeros (thin)**: Mínima lógica, todo en servidor
- **Clientes pesados (fat)**: Mucha lógica local
- **Clientes móviles**: Consideraciones de energía y conectividad intermitente

### Servidores

#### Diseños de Servidor

**1. Servidor Iterativo**: Maneja una petición a la vez

- **Uso:** Peticiones rápidas y simples

**2. Servidor Concurrente**: Maneja múltiples peticiones simultáneamente

- **Multithreaded**: Un hilo por petición
- **Multiproceso**: Un proceso por petición
- **Uso:** Peticiones complejas o largas

**3. Servidor de Estado vs Sin Estado**

- **Sin estado**: Más escalable y tolerante a fallas
- **Con estado**: Mejor rendimiento para sesiones largas

**Análisis:** Para aplicaciones web modernas, servidores sin estado son preferibles por escalabilidad horizontal.

### Migración de Código

Transferir código entre máquinas para:

- Reducir latencia de comunicación
- Balanceo de carga
- Flexibilidad

**Ejemplos:** JavaScript en navegadores, agentes móviles

### Clusters y Grids

**Clusters**: Computadoras homogéneas interconectadas de alta velocidad para computación paralela **Grids**: Recursos heterogéneos distribuidos geográficamente para compartir recursos

## Capítulo 4: Comunicación

### Protocolos en Capas

**Modelo OSI** vs **TCP/IP**: El modelo TCP/IP es el estándar de facto.

### Tipos de Comunicación

#### 1. **Remote Procedure Call (RPC)**

Llamar procedimientos en máquinas remotas como si fueran locales.

**Ventajas:** Transparencia, fácil de programar **Desventajas:** No maneja bien fallas de red, acoplamiento sincrónico

**Semánticas:**

- **At-least-once**: Puede ejecutar múltiples veces
- **At-most-once**: Ejecuta máximo una vez (preferible)
- **Exactly-once**: Ideal pero difícil de implementar

#### 2. **Remote Method Invocation (RMI)**

Versión orientada a objetos de RPC.

**Ejemplo:** Java RMI, permite pasar objetos por referencia

#### 3. **Message-Oriented Middleware (MOM)**

Comunicación asíncrona mediante colas de mensajes.

**Ventajas:**

- Desacoplamiento temporal
- Persistencia de mensajes
- Confiabilidad

**Ejemplos:** RabbitMQ, Apache Kafka, ActiveMQ

**Análisis:** Ideal para sistemas que requieren alta confiabilidad y desacoplamiento. Kafka es excelente para streaming de datos en tiempo real.

#### 4. **Comunicación Basada en Streams**

Para datos continuos (audio, video).

**Consideraciones:** Sincronización, buffering, QoS (Quality of Service)

### Multicast

Envío de mensajes a múltiples receptores eficientemente.

**Protocolos:**

- **IP Multicast**: Eficiente pero limitado a LANs
- **Application-level multicast**: Más flexible, funciona sobre internet

## Capítulo 5: Naming (Nomenclatura)

### Tipos de Nombres

1. **Nombres (Names)**: Identificadores legibles por humanos
2. **Direcciones (Addresses)**: Puntos de acceso
3. **Identificadores (Identifiers)**: Únicos, no cambian

### Sistemas de Naming

#### **Naming Plano (Flat)**

Identificadores sin estructura.

**Soluciones:**

- **Broadcasting/Multicasting**: No escalable
- **Forwarding pointers**: Cadenas de referencias (problema: cadenas largas)
- **Home-based approaches**: Ubicación fija conocida
- **DHT (Distributed Hash Tables)**: Como Chord, Pastry
    - **Análisis:** DHT es excelente para P2P, garantiza O(log N) saltos para encontrar nodos

#### **Naming Estructurado (Structured)**

Nombres jerárquicos organizados en árbol.

**Ejemplo:** DNS (Domain Name System)

- Jerarquía: root → TLD → domain → subdomain
- **Caching**: Mejora rendimiento enormemente
- **Replicación**: TLD y root servers replicados

**Análisis:** DNS es extremadamente exitoso por su jerarquía clara y caching agresivo, pero tiene puntos únicos de falla en root servers.

#### **Naming Basado en Atributos**

Buscar entidades por sus características.

**Ejemplos:** LDAP (Lightweight Directory Access Protocol), sistemas de descubrimiento de servicios

### Resolución de Nombres

**Iterativa**: El cliente contacta cada servidor en la cadena **Recursiva**: Cada servidor contacta al siguiente

**Análisis:** La resolución recursiva reduce carga en el cliente pero aumenta carga en servidores intermedios.

## Capítulo 6: Coordinación

### Sincronización de Relojes

**Problema:** No existe un reloj global en sistemas distribuidos.

#### **Sincronización Física**

Sincronizar con tiempo real.

**Algoritmos:**

1. **Cristian's Algorithm**: Clientes sincronizan con servidor de tiempo
    - Considera latencia de red (RTT/2)
2. **Berkeley Algorithm**: Coordinador promedia tiempos y distribuye ajustes
    - No requiere fuente externa de tiempo
3. **Network Time Protocol (NTP)**
    - Jerarquía de servidores (estratos)
    - Muy preciso (ms en Internet, μs en LANs)

**Análisis:** NTP es el estándar de facto por su robustez y precisión.

#### **Sincronización Lógica**

Solo importa el orden de eventos, no el tiempo real.

**1. Relojes Lógicos de Lamport**

- Cada proceso tiene un contador
- Reglas: incrementar antes de cada evento, actualizar al recibir mensajes
- **Propiedad:** Si a → b entonces L(a) < L(b)
- **Limitación:** L(a) < L(b) no implica a → b

**2. Vector Clocks**

- Cada proceso mantiene un vector de contadores
- Captura relaciones de causalidad completas
- **Ventaja:** Puede determinar si eventos son concurrentes

**Análisis:** Vector clocks son superiores para detectar causalidad pero requieren más espacio (O(N) vs O(1)).

### Exclusión Mutua

Garantizar acceso exclusivo a recursos compartidos.

#### **Algoritmos Distribuidos**

**1. Algoritmo Centralizado**

- Un coordinador otorga permisos
- **Ventajas:** Simple, eficiente (3 mensajes)
- **Desventajas:** Punto único de falla

**2. Algoritmo Distribuido (Ricart-Agrawala)**

- Requiere permiso de TODOS los procesos
- **Mensajes:** 2(N-1) por entrada a sección crítica
- **Ventajas:** Sin punto único de falla
- **Desventajas:** Alto overhead de mensajes, un proceso caído bloquea todo

**3. Token Ring**

- Token circula, quien lo tiene puede entrar
- **Ventajas:** Fairness garantizado
- **Desventajas:** Pérdida de token es crítica

**Análisis:** En la práctica, se prefieren algoritmos basados en quorum o sistemas de locks distribuidos como Chubby (Google) o ZooKeeper para balance entre eficiencia y tolerancia a fallas.

### Algoritmos de Elección

Elegir un coordinador cuando el actual falla.

**1. Bully Algorithm**

- El proceso con mayor ID gana
- Proceso con mayor ID "intimida" a los demás
- **Mensajes:** O(N²) en peor caso

**2. Ring Algorithm**

- Pasa mensaje en anillo recolectando IDs
- El mayor ID descubierto gana
- **Mensajes:** O(N)

**Análisis:** Ring es más eficiente en mensajes pero requiere conocer topología de anillo. Bully es más robusto ante cambios de topología.

### Transacciones Distribuidas

#### **Propiedades ACID**

- **Atomicity**: Todo o nada
- **Consistency**: Estado válido a estado válido
- **Isolation**: Transacciones no interfieren
- **Durability**: Cambios persisten tras commit

#### **Protocolos de Commit**

**1. Two-Phase Commit (2PC)**

**Fase 1 - Preparación:**

- Coordinador pregunta: "¿Pueden hacer commit?"
- Participantes responden: "Yes" o "No"

**Fase 2 - Commit:**

- Si todos "Yes": Coordinador ordena commit
- Si algún "No": Coordinador ordena abort

**Ventajas:** Garantiza atomicidad **Desventajas:** Bloqueo si coordinador falla en fase 2

**2. Three-Phase Commit (3PC)**

- Agrega fase "pre-commit" para evitar bloqueos
- Más complejo, raramente usado en práctica

**Análisis:** 2PC es el estándar aunque tiene limitaciones. Para mejor disponibilidad, sistemas modernos prefieren eventual consistency (sacrificando ACID por BASE).

#### **Control de Concurrencia**

**1. Locking (Pesimista)**

- **Two-Phase Locking (2PL)**: Fase de adquisición, luego fase de liberación
- **Problema:** Deadlocks

**2. Timestamps (Optimista)**

- Asigna timestamp a cada transacción
- Detecta conflictos y aborta transacciones más jóvenes
- **Ventaja:** Sin deadlocks

**3. Optimistic Concurrency Control**

- Asume sin conflictos, valida al final
- **Ideal para:** Pocas colisiones

**Análisis:** En sistemas con alta contención, locking es mejor. Con baja contención, optimistic control es más eficiente.

### Deadlock

**Prevención:**

- Timeouts
- Ordenamiento de recursos
- Timestamps para prioridad

**Detección:**

- Wait-for graphs distribuidos
- Algoritmos de detección de ciclos

## Capítulo 7: Consistencia y Replicación

### ¿Por Qué Replicar?

1. **Confiabilidad**: Tolerancia a fallas
2. **Rendimiento**: Localidad, reducir latencia, balancear carga
3. **Escalabilidad**: Distribuir carga de lectura

**Desafío principal:** Mantener réplicas consistentes

### Modelos de Consistencia

#### **Consistencia Centrada en Datos**

**1. Consistencia Estricta (Strict)**

- Cualquier lectura retorna el valor de la escritura más reciente
- **Problema:** Imposible de implementar eficientemente en sistemas distribuidos (requeriría sincronización perfecta)

**2. Consistencia Secuencial (Sequential)**

- El resultado es equivalente a alguna ejecución secuencial
- Todos los procesos ven el mismo orden de operaciones
- **Ventaja:** Razonable de implementar
- **Desventaja:** Costoso en performance

**3. Consistencia Causal**

- Solo operaciones causalmente relacionadas deben verse en el mismo orden
- Operaciones concurrentes pueden verse en diferente orden
- **Implementación:** Vector clocks
- **Análisis:** Buen balance entre consistencia y rendimiento

**4. Consistencia Eventual (Eventual)**

- Si no hay más actualizaciones, eventualmente todas las réplicas convergen
- **Ventajas:** Alta disponibilidad y performance
- **Uso:** Amazon Dynamo, DNS, sistemas altamente escalables

**Análisis crítico:**

- **Strict consistency**: Ideal teórico, impráctica
- **Sequential consistency**: Para aplicaciones que requieren orden global
- **Causal consistency**: Excelente compromiso para redes sociales, colaboración
- **Eventual consistency**: Mejor para sistemas masivamente distribuidos donde disponibilidad > consistencia fuerte

#### **Consistencia Centrada en Cliente**

Garantías desde la perspectiva del cliente:

1. **Monotonic Reads**: Si lees valor x, lecturas futuras no verán valores anteriores a x
2. **Monotonic Writes**: Escrituras del mismo cliente se propagan en orden
3. **Read Your Writes**: Leer después de escribir ve tu propia escritura
4. **Writes Follow Reads**: Escrituras se propagan después de lecturas previas

**Uso:** Sistemas con réplicas y clientes móviles

### Protocolos de Replicación

#### **Primary-Based Protocols**

**1. Remote-Write (Primary-Backup)**

- Todas las escrituras van al primario
- Primario actualiza backups
- **Ventajas:** Consistencia fuerte, simple
- **Desventajas:** Primario es cuello de botella

**2. Local-Write**

- La réplica local se vuelve primaria para escribir
- Requiere migrar objeto o primario
- **Ventaja:** Mejor para lecturas/escrituras locales frecuentes

**Análisis:** Primary-backup es el más común (usado en bases de datos tradicionales). Funciona bien con más lecturas que escrituras.

#### **Replicated-Write Protocols**

**1. Active Replication**

- Todas las réplicas ejecutan todas las operaciones
- Requiere operaciones determinísticas
- **Uso:** Sistemas de alta disponibilidad

**2. Quorum-Based Protocols**

- Requiere mayoría de votos para leer/escribir
- **Regla:** N_r + N_w > N (donde N = réplicas totales)
- **Ventajas:** Tolerancia a fallas, sin primario
- **Ejemplo:** Amazon Dynamo, Cassandra

**Parámetros comunes:**

- N=3, W=2, R=2 (balance)
- N=3, W=3, R=1 (optimizado para lecturas)
- N=3, W=1, R=3 (optimizado para escrituras)

**Análisis:** Quorum es muy flexible y popular en NoSQL. Permite ajustar trade-off entre consistencia, disponibilidad y latencia.

### Teorema CAP

**Propiedades:**

- **C**onsistency: Todos ven los mismos datos
- **A**vailability: Sistema responde siempre
- **P**artition tolerance: Sistema funciona con particiones de red

**Teorema:** Solo puedes garantizar 2 de 3 simultáneamente.

**Sistemas en la práctica:**

- **CP**: Bancos, sistemas financieros (sacrifican disponibilidad)
- **AP**: Redes sociales, DNS (sacrifican consistencia fuerte)
- **CA**: Bases de datos centralizadas (no toleran particiones)

**Análisis:** En redes reales las particiones ocurren, así que realmente es elegir entre CP y AP. Sistemas modernos tienden hacia AP con eventual consistency.

### BASE vs ACID

**BASE** (alternativa a ACID para alta disponibilidad):

- **B**asically **A**vailable
- **S**oft state
- **E**ventual consistency

**Análisis:** Sistemas web modernos prefieren BASE para escalabilidad (Facebook, Twitter, Amazon retail).

---

## Análisis Comparativo: ¿Qué es Mejor y Cuándo?

### Arquitecturas

|Arquitectura|Mejor Para|Evitar Para|
|---|---|---|
|Cliente-Servidor|Apps tradicionales, pocos clientes|Sistemas masivos, alta escalabilidad|
|Three-Tier|Aplicaciones empresariales|Sistemas tiempo real de baja latencia|
|P2P|Compartir archivos, blockchain, resiliencia|Apps que requieren control centralizado|
|Microservicios|Apps grandes, equipos independientes|Apps pequeñas y simples|
|Event-Driven|IoT, sistemas reactivos, alta escalabilidad|Operaciones transaccionales complejas|

### Comunicación

|Mecanismo|Mejor Para|Evitar Para|
|---|---|---|
|RPC|Operaciones síncronas simples|Sistemas altamente desacoplados|
|MOM|Sistemas confiables, desacoplamiento|Operaciones que requieren respuesta inmediata|
|Streaming|Audio, video, datos continuos|Transferencia de archivos discretos|

### Consistencia

|Modelo|Mejor Para|Trade-offs|
|---|---|---|
|Strict/Sequential|Bancos, inventarios críticos|Baja disponibilidad, alta latencia|
|Causal|Redes sociales, colaboración|Complejidad media, buen balance|
|Eventual|Sistemas web masivos, DNS|Inconsistencias temporales|

### Recomendaciones Generales

**Para alta disponibilidad y escalabilidad masiva:**

- Arquitectura: Microservicios + Event-driven
- Comunicación: MOM (Kafka)
- Consistencia: Eventual (Dynamo-style)
- Replicación: Quorum-based, multi-master

**Para consistencia fuerte y transacciones:**

- Arquitectura: Three-tier tradicional
- Comunicación: RPC/RMI con 2PC
- Consistencia: Sequential o Strong
- Replicación: Primary-backup

**Para sistemas híbridos (común en la realidad):**

- Usar consistencia fuerte donde se necesite (pagos, inventario)
- Usar eventual consistency donde se pueda (perfiles, comentarios)
- Ejemplos: Amazon usa ambos enfoques en diferentes partes del sistema

---

## Patrones y Mejores Prácticas

1. **Idempotencia**: Diseña operaciones idempotentes para retry seguro
2. **Circuit Breaker**: Falla rápido cuando servicio está caído
3. **Bulkhead**: Aísla recursos para evitar fallas en cascada
4. **Saga Pattern**: Para transacciones distribuidas sin 2PC
5. **CQRS**: Separa lecturas de escrituras para mejor escalabilidad
6. **Event Sourcing**: Almacena eventos en vez de estado para auditabilidad
