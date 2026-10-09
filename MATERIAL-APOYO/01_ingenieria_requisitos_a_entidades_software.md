# GUÍA DIDÁCTICA 01: DE LA INGENIERÍA DE REQUISITOS A LAS ENTIDADES DE SOFTWARE
## Transformando Historias de Usuario en Modelos de Datos Reales
### Ficha 3535599 — Tecnólogo en Análisis y Desarrollo de Software (ADSO)

> **CENTRO:** CENTRO DE DESARROLLO INDUSTRIAL, EMPRESARIAL Y CAMPESINO - CDIEC (Código: 9232)  
> **REGIONAL:** Cundinamarca  
> **INSTRUCTOR FACILITADOR:** Melqui Alexander Romero Veru  
> **COMPETENCIA:** 220501094 — Estructurar propuesta técnica de servicio de tecnología de la información  
> **PROYECTO FORMATIVO:** 2681999 — Desarrollo de Software a la medida para el sector productivo de Soacha  
> **AMBIENTE DE FORMACIÓN:** CV-217  

---

## 1. El Puente entre el Cliente y la Base de Datos

En el desarrollo de software profesional, un cliente **nunca** te pedirá: *"Por favor, créame una tabla relacional con una llave foránea compuesta"*.  
El cliente te dirá algo como:  
> *"Necesito un sistema web para mi ferretería en Soacha donde mis 3 vendedores puedan registrar las ventas de los clientes, imprimir la factura y ver cuánto inventario queda de cada tornillo y pintura para que no nos roben mercancía."*

El trabajo del **Tecnólogo en Análisis y Desarrollo de Software (ADSO)** consiste en escuchar esa necesidad, traducirla en requerimientos y modelar la arquitectura de base de datos que sostendrá la aplicación.

```
┌─────────────────────────┐       ┌────────────────────────┐       ┌────────────────────────┐
│ NECESIDAD DEL CLIENTE   │  ──►  │ HISTORIAS DE USUARIO   │  ──►  │ MODELO DE DATOS        │
│ "Quiero vender tornillos│       │ "Como vendedor quiero  │       │ Entidades:             │
│ y saber qué stock hay"  │       │ registrar una venta..."│       │ `productos`, `ventas`  │
└─────────────────────────┘       └────────────────────────┘       └────────────────────────┘
```

---

## 2. La Técnica Lingüística de Extracción de Entidades

Para no quedarse con la mente en blanco frente a una hoja vacía, los analistas de software emplean la **Técnica Lingüística (Gramatical)**:

| Elemento Gramatical | En el Lenguaje del Cliente | En la Base de Datos Relacional |
| :--- | :--- | :--- |
| **Sustantivo** | *"El **cliente**, el **producto**, la **factura**, el **vendedor**"* | **Candidato a ENTIDAD (Tabla)** |
| **Adjetivo / Característica** | *"El producto tiene **precio**, **código de barras**, **marca**"* | **ATRIBUTO (Columna / Campo)** |
| **Verbo** | *"El cliente **compra** productos; el administrador **asigna** un rol"* | **RELACIÓN entre Entidades** |

### Ejemplo Práctico de Análisis Lingüístico:
> *"Un **cliente** llega a la tienda. El **cajero** registra la **factura** con la fecha actual. En esa factura se agregan varios **productos**, indicando la cantidad llevada y el precio unitario."*

- **Entidades detectadas:** `usuarios` (cajero), `clientes`, `facturas`, `productos`.
- **Relaciones detectadas:**
  - Un cajero *atiende* muchas facturas.
  - Un cliente *genera* muchas facturas.
  - Una factura *contiene* muchos productos (Relación N:M -> nace `detalle_factura`).

---

## 3. Los 3 Módulos Universales de Cualquier Software

Casi el 95% de los proyectos formativos de la ficha 3535599 (sea para una peluquería, un taller mecánico, una droguería o una tienda de ropa) comparten una arquitectura base de 3 módulos:

```
┌────────────────────────────────────────────────────────────────────────┐
│             ARQUITECTURA MODULAR UNIVERSAL DE UN SOFTWARE              │
├────────────────────┬─────────────────────────────┬─────────────────────┤
│ 1. MÓDULO DE       │ 2. MÓDULO MAESTRO           │ 3. MÓDULO           │
│    SEGURIDAD       │    (CATÁLOGOS)              │    TRANSACCIONAL    │
│ Control de acceso, │ Cosas fijas del negocio     │ Acciones y eventos  │
│ usuarios y roles.  │ que se consultan a menudo.  │ con fecha y valor.  │
│ Ej: `usuarios`,    │ Ej: `clientes`,             │ Ej: `ventas`,       │
│     `roles`.       │     `productos`, `marcas`.  │     `citas`, `pagos`│
└────────────────────┴─────────────────────────────┴─────────────────────┘
```

### Módulo 1: Seguridad y Acceso
Todo software empresarial necesita restringir quién puede ver o modificar qué cosas:
- **`roles`:** Define el perfil (Administrador, Vendedor, Contador, Cliente).
- **`usuarios`:** Guarda el correo, nombre y contraseña encriptada (`password_hash`).  
*Regla de oro:* ¡Nunca cree columnas fijas como `es_admin = TRUE` en usuarios si el sistema puede crecer! Use siempre la tabla `roles`.

### Módulo 2: Catálogos / Maestros
Son las entidades que describen el inventario o los actores del negocio:
- `clientes` (cédula, nombres, teléfono, dirección).
- `productos` o `servicios` (código, nombre, descripción, precio base).
- `categorias` o `marcas` (agrupación lógica).

### Módulo 3: Transaccional / Movimientos
Es el corazón operativo del negocio. Aquí se registra el movimiento de dinero, tiempo o mercancía:
- `ventas` o `facturas` (fecha, total, estado: 'PAGADA', 'PENDIENTE').
- `detalle_ventas` (los productos específicos de cada venta).
- `citas_agendadas` o `reservas` (en sistemas de servicios).

---

## 4. Los Dos Errores Fatales del Principiante en ADSO

### Error 1: Confundir la Pantalla (UI) con la Base de Datos
Un aprendiz novato suele pensar:  
*"En mi software tengo una pantalla de Login, una pantalla de Inicio y una pantalla con un botón Guardar. Por tanto, creo una tabla llamada `pantalla_login` y otra llamada `boton_guardar`"*.

> [!CAUTION]
> **Aclaración Arquitectónica Vital:**  
> Las pantallas y los botones pertenecen al **Frontend** (HTML, CSS, JavaScript, React, Flutter).  
> La base de datos pertenece a la **Capa de Persistencia** (almacenamiento de datos puros).  
> La base de datos no sabe qué es un botón ni qué color tiene la pantalla; a la base de datos solo le interesa saber quién es el usuario y qué permiso tiene.

### Error 2: El "Super-Usuario Todo en Uno"
Intentar meter el rol, la clave, las compras y la dirección de envío en una sola tabla de usuarios. Si un cliente hace 10 compras, tendrías que registrar al usuario 10 veces.

---

## 5. Taller de Aplicación Rápida para el Proyecto Formativo

Reúnase con su equipo de desarrollo del **Proyecto Formativo 2681999** y responda en una hoja:
1. ¿Cuál es el nombre de su software y qué problema resuelve en Soacha?
2. Liste los 3 tipos de usuarios (Roles) que interactuarán con el sistema.
3. Identifique al menos **6 entidades indispensables** para que su software funcione.
4. Clasifique cada entidad dentro de los 3 módulos universales (Seguridad, Catálogo o Transaccional).
