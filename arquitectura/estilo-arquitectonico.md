# Estilo Arquitectónico del Sistema

## Descripción del Estilo Seleccionado

Para el **Marketplace de Productos para Mascotas**, se ha seleccionado el estilo de **Monolito Modular Organizado en Capas**[cite: 15, 20].

Esta elección permite consolidar el despliegue en una única unidad (fácil de operar e implementar en etapas iniciales), manteniendo una separación clara de responsabilidades por módulos funcionales (Usuarios, Sellers, Catálogo, Carrito y Pedidos) y capas lógicas[cite: 19, 20].

| Elemento | Descripción Aplicada al Marketplace |
|---|---|
| **Estilo Arquitectónico** | Monolito Modular en Capas[cite: 15, 20]. |
| **Objetivo** | Organizar el sistema en un solo despliegue manteniendo módulos independientes y separación de capas[cite: 19, 20]. |
| **¿Qué problema resuelve?** | Evita la complejidad prematura de infraestructura distribuida sin perder modularidad para futura escalabilidad[cite: 18, 19]. |
| **Componentes Clave** | Cliente Web (Angular), Middlewares, Capa de Presentación, Capa de Lógica de Negocio, Capa de Datos (PostgreSQL), Pasarela de Pagos y Servicios de Envíos. |
| **Beneficios** | Facilidad de desarrollo, despliegue unificado, mantenimiento ordenado y capacidad de escalamiento horizontal[cite: 18, 19]. |

---

## Diagrama Global de la Arquitectura

```mermaid
flowchart TD
    %% Clientes
    subgraph Clientes ["Clientes del Sistema"]
        C1[Cliente / Comprador]
        C2[Seller / Vendedor]
        C3[Administrador]
    end

    %% Frontend
    subgraph Frontend ["Aplicación Web"]
        WEB[Cliente Web - Angular 18]
    end

    %% Backend Monolítico
    subgraph Backend ["Monolito Marketplace Backend (Node.js / Express)"]
        MW[Middlewares: Auth JWT, Validación, Logger]

        subgraph CapaPresentacion ["1. Capa de Presentación"]
            P1[módulo usuarios]
            P2[módulo sellers]
            P3[módulo catálogo]
            P4[módulo carrito]
            P5[módulo pedidos]
        end

        subgraph CapaNegocio ["2. Capa de Lógica de Negocio"]
            N1[usuarios.service]
            N2[sellers.service]
            N3[catalogo.service]
            N4[carrito.service]
            N5[pedidos.service]
        end

        subgraph CapaDatos ["3. Capa de Datos"]
            D1[usuarios.repository]
            D2[sellers.repository]
            D3[catalogo.repository]
            D4[carrito.repository]
            D5[pedidos.repository]
            ORM[Acceso a datos compartido - Sequelize/Prisma]
        end
    end

    %% Persistencia y Externos
    BD[(Base de Datos PostgreSQL)]
    PAGOS[Pasarela de Pagos API]
    ENVIOS[Servicio de Envíos API]

    %% Relaciones
    Clientes -->|HTTPS / REST| WEB
    WEB -->|HTTPS / JSON REST| MW
    MW --> CapaPresentacion

    P1 --> N1
    P2 --> N2
    P3 --> N3
    P4 --> N4
    P5 --> N5

    N1 --> D1
    N2 --> D2
    N3 --> D3
    N4 --> D4
    N5 --> D5

    D1 & D2 & D3 & D4 & D5 --> ORM
    ORM -->|SQL / TCP| BD

    N5 -->|HTTPS / REST| PAGOS
    N5 -->|HTTPS / REST| ENVIOS