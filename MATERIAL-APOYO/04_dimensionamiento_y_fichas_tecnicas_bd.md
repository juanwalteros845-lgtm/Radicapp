# GUÍA DIDÁCTICA 04: DIMENSIONAMIENTO, FICHAS TÉCNICAS Y COSTEO DEL MOTOR DE BASE DE DATOS
## Articulación con las Evidencias AA1-EV02 y AA1-EV03
### Ficha 3535599 — Tecnólogo en Análisis y Desarrollo de Software (ADSO)

> **CENTRO:** CENTRO DE DESARROLLO INDUSTRIAL, EMPRESARIAL Y CAMPESINO - CDIEC (Código: 9232)  
> **REGIONAL:** Cundinamarca  
> **INSTRUCTOR FACILITADOR:** Melqui Alexander Romero Veru  
> **COMPETENCIA:** 220501094 — Estructurar propuesta técnica de servicio de tecnología de la información  
> **EVIDENCIAS DIRECTAS:** AA1-EV02 (Fichas Técnicas) y AA1-EV03 (Presupuesto y Proveedores)  

---

## 1. El Vínculo entre la Arquitectura de Datos y la Propuesta Comercial

En un proyecto de software, el cliente no solo paga por el código que escribes: **debe pagar por la infraestructura donde residirán sus datos**.  
Si el equipo de desarrollo no dimensiona la base de datos:
- Podría contratar un servidor muy pequeño y colapsar cuando 20 personas compren al tiempo.
- O podría sobredimensionar y contratar un servidor de \$200 dólares al mes que arruine económicamente al cliente.

Esta guía enseña a seleccionar el motor adecuado, documentar su **Ficha Técnica (AA1-EV02)** y calcular su **Presupuesto (AA1-EV03)** para incluirlo en la propuesta técnica.

---

## 2. Comparativa Técnica de Motores Relacionales (RDBMS)

| Motor RDBMS | Tipo de Licencia | Ventajas Principales | ¿Cuándo elegirlo en el proyecto? |
| :--- | :--- | :--- | :--- |
| **PostgreSQL 16** | *PostgreSQL License* (Open Source tipo MIT/BSD) | Soporte avanzado de tipos de datos, soporte JSON nativo, ACID estricto, excelente para analítica y escalabilidad. | **Recomendado por defecto** para proyectos ADSO empresariales, transaccionales complejos o con datos geoespaciales/híbridos. |
| **MariaDB 11 / MySQL 8** | *GPL v2* (Open Source) | Gran popularidad en la web, fácil configuración con phpMyAdmin, altísimo soporte en hostings compartidos. | Proyectos web tradicionales, sistemas CMS o tiendas virtuales con servidores cPanel/LAMP. |
| **SQLite 3** | *Dominio Público* (100% Libre) | No requiere servidor (`serverless`), toda la BD es un solo archivo en disco, ultra liviano y rápido. | Prototipos iniciales, aplicaciones de escritorio pequeñas o apps móviles offline. |

---

## 3. Modelo Institucional de Ficha Técnica: Motor de Base de Datos (Para AA1-EV02)

Cada equipo de proyecto debe diligenciar esta ficha técnica para la base de datos de su software:

```
┌────────────────────────────────────────────────────────────────────────┐
│             FICHA TÉCNICA DE INFRAESTRUCTURA DE SOFTWARE               │
│                        CENTRO CDIEC (9232)                             │
├────────────────────────────────────────────────────────────────────────┤
│ NOMBRE DEL COMPONENTE: Sistema de Gestión de Bases de Datos Relacional │
│ SOFTWARE SELECCIONADO: PostgreSQL versión 16.x (64 bits)               │
│ TIPO DE LICENCIA: Open Source (PostgreSQL License - Sin costo)         │
├────────────────────────────────────────────────────────────────────────┤
│ 1. REQUISITOS MÍNIMOS DE HARDWARE EN SERVIDOR:                         │
│ - Procesador (vCPU): Mínimo 2 núcleos (2.0 GHz o superior)             │
│ - Memoria RAM: 2 GB RAM (Dedicados exclusivamente a PostgreSQL)        │
│ - Almacenamiento: 20 GB SSD NVMe con IOPS garantizados                 │
│                                                                        │
│ 2. PARÁMETROS DE RED Y CONECTIVIDAD:                                   │
│ - Puerto de escucha: 5432 (TCP/IP) con cifrado SSL/TLS obligatorio     │
│ - Conexiones simultáneas estimadas: 50 conexiones activas concurrentes │
│                                                                        │
│ 3. POLÍTICA DE SEGURIDAD Y RESPALDOS (BACKUPS):                        │
│ - Copias de seguridad automáticas: Diarias en horario nocturno (02:00) │
│ - Retención de copias: 30 días en almacenamiento externo cifrado       │
│ - Punto de Restauración Objetivo (RPO): Máximo 24 horas                │
│ - Tiempo de Recuperación Objetivo (RTO): Máximo 2 horas                │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Cómo Calcular el Almacenamiento y Crecimiento de Datos

Para sustentar en la propuesta técnica cuántos gigabytes de disco necesita el cliente, aplicamos la **Fórmula de Estimación de Volumen de Datos**:

$$\text{Espacio Mensual} = (\text{Tamaño promedio de fila en bytes}) \times (\text{Registros nuevos al mes}) \times 1.5 \text{ (Factor de Índices)}$$

### Caso Real de Ejemplo: Sistema de Ventas en Soacha
- Una ferretería genera **200 ventas al día** = **6.000 ventas al mes**.
- Cada venta tiene un promedio de **3 productos** = **18.000 filas de detalle al mes**.
- Tamaño estimado de una fila: ~200 bytes.
- Cálculo:
  $$18.000 \times 200 \text{ bytes} \times 1.5 = 5.400.000 \text{ bytes} \approx 5.4 \text{ MB al mes}$$
- En 1 año completo:
  $$5.4 \text{ MB} \times 12 = 64.8 \text{ MB de datos puros}$$

> **Conclusión Profesional:**  
> Un disco de **20 GB a 40 GB SSD** es más que suficiente para garantizar más de 5 años de funcionamiento continuo del sistema, incluyendo respaldos, logs del sistema operativo e imágenes livianas de productos.

---

## 5. Matriz Comparativa de Proveedores de Hosting para BD (Para AA1-EV03)

Para la matriz de costos y cotizaciones que se entrega en **AA1-EV03**, los aprendices pueden comparar estas tres opciones del mercado actual:

| Proveedor | Tipo de Servicio | Especificaciones Técnicas | Costo Mensual Estimado | Justificación de Selección |
| :--- | :--- | :--- | :---: | :--- |
| **Contabo / Hostinger VPS** | Servidor VPS No Administrado | 4 vCPU, 8 GB RAM, 50 GB NVMe | \$6.00 USD (\$24.000 COP) | **Más económica:** Ideal para clientes con presupuesto ajustado; el equipo de ADSO debe encargarse de instalar y configurar el motor manualmente. |
| **DigitalOcean Droplet** | Servidor Cloud VPS | 2 vCPU, 2 GB RAM, 50 GB SSD | \$12.00 USD (\$48.000 COP) | **Equilibrada:** Excelente estabilidad de red, soporte de snapshots (copias de seguridad) con un clic e IP pública fija. |
| **Supabase / Railway** | Base de Datos Administrada (PaaS) | PostgreSQL 16 Administrado, backups diarios automáticos, SSL | \$0 a \$25.00 USD (\$0 a \$100.000 COP) | **Máxima productividad:** Cero mantenimiento de servidor; incluye autenticación y APIs automáticas integradas. |
