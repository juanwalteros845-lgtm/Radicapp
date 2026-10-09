# GUÍA DIDÁCTICA 03: MODELO LÓGICO-RELACIONAL Y DICCIONARIO DE DATOS
## Estándar Técnico Formal para Documentación de Software
### Ficha 3535599 — Tecnólogo en Análisis y Desarrollo de Software (ADSO)

> **CENTRO:** CENTRO DE DESARROLLO INDUSTRIAL, EMPRESARIAL Y CAMPESINO - CDIEC (Código: 9232)  
> **REGIONAL:** Cundinamarca  
> **INSTRUCTOR FACILITADOR:** Melqui Alexander Romero Veru  
> **COMPETENCIA:** 220501094 — Estructurar propuesta técnica de servicio de tecnología de la información  
> **DOCUMENTO DESTINO:** `Modelo propuesta tecnica.doc`  

---

## 1. ¿Qué es el Modelo Lógico-Relacional?

Mientras que el Modelo Conceptual (DER) es un esquema visual para entender las relaciones humanas y del negocio, el **Modelo Lógico-Relacional** es la especificación técnica precisa y detallada de cómo se implementarán las tablas en el motor de base de datos.

En el modelo relacional:
1. Cada entidad se convierte en una **Tabla física**.
2. Cada atributo se convierte en una **Columna** con su tipo de dato técnico (`INT`, `VARCHAR(100)`, `DECIMAL(10,2)`).
3. Cada relación 1:N se materializa mediante una **Llave Foránea (FK)**.
4. Cada relación N:M se materializa mediante una **Tabla Intermedia con dos FKs**.

---

## 2. El Diccionario de Datos: El Manual Técnico de la Base de Datos

En el desarrollo de software profesional, un diagrama por sí solo no basta. Un nuevo programador que se integre al equipo necesita saber:  
*¿Cuántos caracteres caben en el correo? ¿El teléfono puede ser nulo? ¿Qué significa el estado '1' o '0'?*

> Un **Diccionario de Datos** es una tabla descriptiva para cada entidad que especifica el nombre técnico de las columnas, sus tipos de datos, sus restricciones de seguridad y el significado que tienen en el software.

### Estructura Estándar de una Tabla en el Diccionario:

| Nombre del Campo | Tipo de Dato | Clave | Nulo | Valor por Defecto | Descripción / Regla de Negocio |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `id_usuario` | INT AUTO_INCREMENT | **PK** | NO | Ninguno | Identificador único secuencial del usuario. |
| `id_rol` | INT | **FK** | NO | Ninguno | Rol asignado (Apunta a `roles.id_rol`). |
| `nombres` | VARCHAR(100) | -- | NO | Ninguno | Nombres y apellidos completos del usuario. |
| `email` | VARCHAR(120) | **UNIQUE** | NO | Ninguno | Correo corporativo único para inicio de sesión. |
| `password_hash` | VARCHAR(255) | -- | NO | Ninguno | Hash criptográfico de la contraseña (bcrypt/Argon2). |
| `activo` | BOOLEAN | -- | NO | TRUE | `TRUE` = Usuario habilitado; `FALSE` = Bloqueado. |
| `creado_en` | TIMESTAMP | -- | NO | CURRENT_TIMESTAMP | Fecha y hora exacta de registro en el sistema. |

---

## 3. Ejemplo Aplicado: Módulo de Facturación y Ventas

Veamos cómo documentar las tablas principales del módulo de ventas para incluir en la propuesta técnica:

### Tabla: `ventas`
**Propósito:** Almacena la cabecera de cada transacción comercial realizada en el establecimiento.

| Nombre del Campo | Tipo de Dato | Clave | Nulo | Valor por Defecto | Descripción / Regla de Negocio |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `id_venta` | INT AUTO_INCREMENT | **PK** | NO | Ninguno | Número consecutivo de la factura/venta. |
| `numero_factura` | VARCHAR(20) | **UNIQUE** | NO | Ninguno | Consecutivo fiscal (ej. `FAC-2026-0001`). |
| `fecha_hora` | DATETIME | -- | NO | CURRENT_TIMESTAMP | Momento exacto de emisión del comprobante. |
| `id_cliente` | INT | **FK** | NO | Ninguno | Cliente que realiza la compra (`clientes.id_cliente`). |
| `id_usuario` | INT | **FK** | NO | Ninguno | Empleado/Cajero que facturó (`usuarios.id_usuario`). |
| `metodo_pago` | VARCHAR(30) | -- | NO | 'EFECTIVO' | Valores válidos: 'EFECTIVO', 'NEQUI', 'TARJETA'. |
| `total` | DECIMAL(12,2) | -- | NO | 0.00 | Valor total a pagar. Debe ser >= 0. |
| `estado` | VARCHAR(15) | -- | NO | 'PAGADA' | Estados: 'PAGADA', 'ANULADA', 'PENDIENTE'. |

---

### Tabla: `detalle_ventas`
**Propósito:** Desglosa los productos individuales que componen una venta específica (Tabla intermedia 1:N hacia `ventas` y `productos`).

| Nombre del Campo | Tipo de Dato | Clave | Nulo | Valor por Defecto | Descripción / Regla de Negocio |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `id_detalle` | INT AUTO_INCREMENT | **PK** | NO | Ninguno | Identificador de la línea de detalle. |
| `id_venta` | INT | **FK** | NO | Ninguno | Factura a la que pertenece (`ventas.id_venta`). |
| `id_producto` | INT | **FK** | NO | Ninguno | Producto vendido (`productos.id_producto`). |
| `cantidad` | INT | -- | NO | 1 | Unidades compradas. Restricción: `cantidad > 0`. |
| `precio_unitario` | DECIMAL(10,2) | -- | NO | Ninguno | Precio de venta congelado al momento de la compra. |
| `subtotal` | DECIMAL(12,2) | -- | NO | Ninguno | Resultado del cálculo: `cantidad * precio_unitario`. |

---

## 4. Buenas Prácticas de Nomenclatura para el Proyecto ADSO

Al diseñar su modelo relacional, siga estas reglas profesionales:
1. **Convención de Nombres (`snake_case`):** Todo en minúsculas con guiones bajos (`fecha_nacimiento`, `total_impuesto`). Evite mayúsculas intermedias o espacios.
2. **Nombres en Plural o Singular Consistente:** Si llama a una tabla `usuarios`, llame a las otras `productos`, `ventas`, `clientes`.
3. **Llaves Primarias Estandarizadas:** Siempre use `id_nombretabla` (ej. `id_cliente`, `id_producto`). Esto evita confusiones cuando haga consultas `JOIN`.
4. **Campos de Auditoría Universales:** Incluya siempre en tablas maestras `creado_en (TIMESTAMP)` y `actualizado_en (TIMESTAMP)`. Son indispensables para saber cuándo se modificó un dato.
5. **Cero Caracteres Especiales:** Nunca use la letra `ñ`, tildes, signos de puntuación ni palabras reservadas de SQL (como `order`, `select`, `table`).

---

## 5. Lista de Chequeo de Calidad antes de Entregar

Antes de pegar su Diccionario de Datos en el archivo `Modelo propuesta tecnica.doc`, verifique:
- [ ] ¿Todas las tablas tienen una Llave Primaria (PK) definida?
- [ ] ¿Todas las relaciones 1:N tienen su Llave Foránea (FK) identificada con la tabla de procedencia?
- [ ] ¿Los precios y montos de dinero usan `DECIMAL` en lugar de `FLOAT` o `INT` para evitar pérdida de centavos?
- [ ] ¿Los campos críticos (como correo, usuario o documento) tienen restricción `UNIQUE`?
- [ ] ¿Las contraseñas tienen suficiente longitud (mínimo `VARCHAR(255)`) para guardar el hash de seguridad?
