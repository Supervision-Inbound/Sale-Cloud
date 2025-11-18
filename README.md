# Workclouds · SII POS FULL (Demo avanzada estilo Bsale)

Esta es una base **avanzada** para un ecosistema tipo Bsale / punto de venta con:

- **Multicliente (tenants / empresas)**
- **Login con roles** (ADMIN y EMPLOYEE)
- **Gestión de empleados** por tenant
- **Gestión de productos** (stock, precio, código de barras)
- **Ventana de ventas (POS)** con input para escáner de código de barras
- **Registro de ventas** y resumen / dashboard básico
- **DTE de boleta (XML simplificado)** por tenant
- Backend en **Cloudflare Workers (API REST)**
- Frontend en `/public` como SPA de administración

> ⚠️ IMPORTANTE (Legal / técnico)
>
> - Esta base **NO está certificada por el SII**.
> - El módulo de DTE es DEMO: estructura XML simplificada + flujo para que luego conectes:
>   - Firma XMLDsig con tu certificado (.pfx / .p12)
>   - WebServices reales del SII (certificación / producción)
>   - Manejo de folios (CAF), trackId, y estados reales.

---

## Estructura

- `wrangler.toml` → Config para Cloudflare Worker.
- `worker/index.js` → Router/API principal.
- `worker/storage.js` → Capa de datos en memoria (tenants, usuarios, sesiones, productos, ventas, DTE).
- `worker/dte.js` → Generador XML de boleta DEMO.
- `worker/siiClient.js` → Cliente SII SIMULADO (donde luego conectas el real).
- `public/index.html` → UI principal (login + panel).
- `public/app.js` → Lógica del frontend (fetch a la API).

---

## Usuarios demo

Al arrancar el Worker se crea un usuario ADMIN por defecto:

- email: `admin@demo.cl`
- password: `admin123`
- rol: `ADMIN`

Puedes hacer login con esas credenciales y desde el panel:

1. Crear tenants (empresas).
2. Crear empleados asociados a un tenant.
3. Crear productos por tenant.
4. Hacer ventas desde la ventana POS (escaneando o digitando códigos de barra).
5. Ver el resumen de ventas (total, tickets, promedio, top productos).
6. Generar boletas demo por tenant y simular el envío al SII.

---

## Endpoints importantes (resumen)

- `POST /api/auth/login` → login, devuelve token.
- `GET  /api/auth/me` → datos del usuario logueado.
- `GET  /api/tenants`, `POST /api/tenants`
- `GET  /api/tenants/:tenantId/employees`, `POST /api/tenants/:tenantId/employees`
- `GET  /api/tenants/:tenantId/products`, `POST /api/tenants/:tenantId/products`, `PATCH /api/tenants/:tenantId/products/:productId`
- `POST /api/tenants/:tenantId/sales` → registra venta (y descuenta stock).
- `GET  /api/tenants/:tenantId/sales/summary` → resumen de ventas.
- `GET  /api/tenants/:tenantId/dte`, `POST /api/tenants/:tenantId/dte/boleta`
- `POST /api/dte/:dteId/send` → simulación de envío al SII.

En producción debes:

- Reemplazar `storage.js` por D1/KV/Postgres.
- Completar `dte.js` + `siiClient.js` para los servicios oficiales del SII.
