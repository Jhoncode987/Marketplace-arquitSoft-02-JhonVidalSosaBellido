# Enfoque Arquitectónico: Clean Architecture

## Descripción del Enfoque Seleccionado

Para la organización interna del código del **Marketplace de Productos para Mascotas**, se adopta el enfoque de **Clean Architecture (Arquitectura Limpia)**. 

El objetivo principal es separar las responsabilidades de la aplicación y controlar estrictamente que las dependencias apunten hacia el núcleo del dominio.

| Elemento | Descripción Aplicada al Marketplace |
|---|---|
| **Patrón / Enfoque Arquitectónico** | Clean Architecture (Arquitectura Limpia). |
| **Objetivo** | Separar responsabilidades y controlar las dependencias hacia el dominio. |
| **¿Qué problema resuelve?** | Evita el acoplamiento entre la interfaz Angular, las reglas del negocio y las tecnologías externas (bases de datos, API REST, pasarela de pagos). |
| **Capas Definidas** | Presentación, Aplicación, Dominio e Infraestructura. |
| **Beneficios** | • Facilita el mantenimiento y las pruebas unitarias.<br>• Permite cambiar implementaciones técnicas sin modificar las reglas del negocio.<br>• Mejora la organización y separación de responsabilidades en el código. |

---

## Estructura de Capas y Dependencias

Las dependencias de código deben apuntar estrictamente **hacia adentro** (hacia el Dominio).

```mermaid
flowchart TD
    %% Estilos
    classDef pres fill:#2563eb,stroke:#fff,stroke-width:1px,color:#fff;
    classDef app fill:#16a34a,stroke:#fff,stroke-width:1px,color:#fff;
    classDef dom fill:#d97706,stroke:#fff,stroke-width:2px,color:#fff;
    classDef infra fill:#4b5563,stroke:#fff,stroke-width:1px,color:#fff;

    subgraph CapaPresentacion ["Capa de Presentación (UI)"]
        UI1[CatalogoComponent]:::pres
        UI2[EstadoCarrito]:::pres
        UI3[CarritoComponent]:::pres
    end

    subgraph CapaAplicacion ["Capa de Aplicación (Casos de Uso)"]
        UC1[ConsultarCatalogoCasoUso]:::app
        UC2[AgregarAlCarritoCasoUso]:::app
        UC3[RegistrarCompraCasoUso]:::app
    end

    subgraph CapaDominio ["Capa de Dominio (Núcleo)"]
        D1[Entidades: Producto, Carrito, Pedido]:::dom
        D2[Contratos / Repositorios]:::dom
        D3[ProcesadorPagos / NotificacionCliente]:::dom
    end

    subgraph CapaInfraestructura ["Capa de Infraestructura (Adaptadores / Frameworks)"]
        INF1[RepositorioProductosMemoria]:::infra
        INF2[ProcesadorPagosSimulado]:::infra
        INF3[NotificacionConsola]:::infra
        INF4[Marketplace API REST]:::infra
    end

    %% Relaciones / Dependencias
    CapaPresentacion -->|Depende de| CapaAplicacion
    CapaAplicacion -->|Depende de| CapaDominio
    CapaInfraestructura -.->|Implementa interfaces de| CapaDominio
    CapaInfraestructura -->|HTTP / REST| INF4