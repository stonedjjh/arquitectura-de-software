# 🗺️ Roadmap de Arquitectura de Software

Este documento sirve como registro y hoja de ruta de los patrones, arquitecturas y conceptos de nivel "Enterprise" que se documentarán, debatirán y probarán progresivamente en este repositorio.

## 🏗️ Patrones de Diseño y Estructura
- [ ] **Domain-Driven Design (DDD):** Diseño guiado por el dominio (Entidades, Agregados, Casos de Uso).
- [ ] **Arquitectura Hexagonal (Ports & Adapters):** Desacoplamiento estricto de la lógica de negocio de la infraestructura y frameworks.
- [ ] **Microservicios:** Estrategias de división, *bounded contexts* y autonomía de despliegue.

## 🔄 Flujo de Datos y Eventos
- [ ] **CQRS (Command Query Responsibility Segregation):** Separación de modelos de lectura (R) y escritura (W).
- [ ] **Event-Driven Design (Event Sourcing):** Arquitecturas orientadas a eventos como la única fuente de verdad (Single Source of Truth).
- [ ] **Programación Reactiva:** Manejo de flujos de datos asíncronos, backpressure y sistemas no bloqueantes.

## 🕸️ Sistemas Distribuidos y Consistencia
- [ ] **Patrón Saga:** Manejo de transacciones distribuidas sin bloqueos ACID tradicionales.
  - *Debate futuro:* Saga Coreografiada (Eventos descentralizados) vs Saga Orquestada (Gestor central).
  - *Práctica:* Transacciones de compensación (Rollbacks lógicos).
- [ ] **Orquestaciones Complejas:** Control de flujos de negocio entre múltiples contextos.

## 🛡️ Resiliencia y Alta Disponibilidad
- [ ] **Design for Failure (Programación a Fallo):** Asumir que la red y el hardware van a fallar (Implementación de Patrones como Circuit Breaker, Retries, Bulkheads).
- [ ] **Graceful Degradation (Degradación Amigable):** Mantener operativa la experiencia del usuario, aunque sea de forma parcial, cuando los servicios dependientes caen.

---
> *"Todo a su debido tiempo. El objetivo en este proyecto es debatir críticamente, entender el por qué y el para qué antes de implementar cualquiera de estos patrones, evitando la sobre-ingeniería por moda."*
