# Diseño interno de módulos

> **Proyecto:** Marketplace · **Backend:** Node.js + TypeScript · **Módulo de referencia:** Pedidos  
> **Enfoque arquitectónico:** Clean Architecture dentro de cada módulo del monolito modular  
> **Nivel C4:** 4 – Código · **Versión:** 1.0

---

## 1. Propósito

Este documento describe **cómo se organiza el código dentro de un módulo** del backend. Tiene tres objetivos:

* que todos los módulos se construyan con la misma estructura;
* que las reglas de negocio queden aisladas de la tecnología;
* que cualquier integrante del equipo sepa dónde va cada archivo y de qué puede depender.

---

## 2. Estructura de carpetas

```text
src/
└── modules/
    └── pedidos/
        ├── dominio/
        │   ├── pedido.ts               # Entidad Pedido y sus reglas
        │   ├── item-pedido.ts          # Objeto de valor ItemPedido
        │   ├── estado-pedido.ts        # Enumeración EstadoPedido
        │   ├── order-repository.ts     # Interfaz (puerto) de persistencia
        │   ├── payment-port.ts         # Interfaz (puerto) de cobro
        │   └── resultado-cobro.ts      # DTO de respuesta del cobro
        ├── aplicacion/
        │   ├── crear-pedido.use-case.ts # Caso de uso CrearPedido
        │   └── crear-pedido.command.ts  # Datos de entrada del caso de uso
        ├── infraestructura/
        │   ├── postgres-order-repository.ts # Implementación con PostgreSQL
        │   ├── stripe-payment.adapter.ts    # Implementación con Stripe
        │   └── paypal-payment.adapter.ts    # Implementación con PayPal
        ├── presentacion/
        │   ├── order.controller.ts    # Traduce HTTP <=> caso de uso
        │   └── pedidos.routes.ts      # Rutas /api/v1/pedidos
        ├── pedidos.module.ts          # Raíz de composición del módulo
        └── index.ts                   # API pública del módulo


| Archivo | Capa | Responsabilidad | Importa de |
|---|---|---|---|
| `pedido.ts` | Dominio | Crear pedidos válidos, calcular el total, cambiar de estado | Solo dominio |
| `item-pedido.ts` | Dominio | Representar un producto, su cantidad y su subtotal | Nada |
| `estado-pedido.ts` | Dominio | Definir los estados posibles del pedido | Nada |
| `order-repository.ts` | Dominio | Declarar **qué** operaciones de persistencia necesita el módulo | `pedido.ts` |
| `payment-port.ts` | Dominio | Declarar **qué** necesita el módulo para cobrar | `resultado-cobro.ts` |
| `resultado-cobro.ts` | Dominio | Estructura del resultado de un cobro | Nada |
| `crear-pedido.use-case.ts` | Aplicación | Coordinar los pasos de la compra | Dominio |
| `crear-pedido.command.ts` | Aplicación | Datos de entrada validados del caso de uso | Nada |
| `postgres-order-repository.ts` | Infraestructura | Guardar y leer pedidos en PostgreSQL | Dominio + driver de BD |
| `stripe-payment.adapter.ts` | Infraestructura | Traducir `cobrar()` a la API de Stripe | Dominio + SDK de Stripe |
| `paypal-payment.adapter.ts` | Infraestructura | Traducir `cobrar()` a la API de PayPal | Dominio + SDK de PayPal |
| `order.controller.ts` | Presentación | Leer la petición HTTP, invocar el caso de uso y responder | Aplicación |
| `pedidos.routes.ts` | Presentación | Registrar las rutas en Express | Presentación |
| `pedidos.module.ts` | Composición | Crear las instancias concretas e inyectarlas | Todas las capas |
| `index.ts` | Composición | Exponer solo lo que otros módulos pueden usar | `pedidos.module.ts` |

---

## 3. Capas del módulo y regla de dependencia
(./img/paquetes-modulo-pedidos.png)

