# 📸 PrecioScan

> App mobile para escanear facturas, comparar precios y ganarle a la inflación.

PrecioScan es una aplicación React Native (Expo) que te permite fotografiar tus facturas, extraer los precios automáticamente con OCR, y saber si un producto está caro o si hay un comercio más conveniente — al estilo Google Flights, pero para el supermercado.

Nació en un contexto de hiperinflación, pero está diseñada para funcionar también en economías estables donde simplemente querés trackear precios por producto y por comercio.

---

## ✨ Features principales

- **Escaneo de facturas** — tomá una foto o cargá desde la galería; el OCR (on-device, offline) extrae los productos y precios automáticamente.
- **Entrada manual** — cargá o editá una factura a mano cuando el OCR no alcanza.
- **Catálogo de productos** — identidad canónica de cada producto, independiente del comercio.
- **Comparador de precios** — visualizá qué comercio es más barato para un producto dado.
- **Historial y tendencias** — seguí la evolución del precio de un producto a lo largo del tiempo.
- **Soporte multi-moneda con tasa de cambio** — guardá precios en moneda local y derivá el valor en USD al vuelo contra una tasa `oficial` o `personal`. En países sin hiperinflación la capa de cambio desaparece sin código condicional.
- **Carrito estimado** _(roadmap)_ — armá una lista de compras y estimá cuánto vas a gastar.
- **Persistencia local primero** — todos los datos quedan en tu dispositivo; sync a la nube es una fase futura (Supabase).

---

## 🛠 Stack

| Área                  | Tecnología                                                   |
| --------------------- | ------------------------------------------------------------ |
| Framework             | Expo (managed) + TypeScript                                  |
| Navegación            | `expo-router` (file-based, estilo Next.js)                   |
| Estilos               | NativeWind (Tailwind para React Native)                      |
| Base de datos local   | `expo-sqlite` + Drizzle ORM                                  |
| Estado servidor       | TanStack Query                                               |
| Estado UI             | Zustand                                                      |
| Cámara / galería      | `expo-image-picker`                                          |
| OCR                   | `@react-native-ml-kit/text-recognition` (on-device, offline) |
| Gráficos              | `react-native-gifted-charts` / `victory-native`              |
| Build                 | EAS Build (development build — requerido por ML Kit)         |
| Sync cloud _(futuro)_ | Supabase                                                     |

---

## 📦 Requisitos previos

- Node.js >= 18
- Expo CLI: `npm install -g expo-cli`
- Cuenta en [Expo EAS](https://expo.dev) (para builds nativos)
- Dispositivo físico Android o iOS (el OCR y la cámara no funcionan bien en simulador)

> ⚠️ **Importante:** ML Kit **no funciona en Expo Go**. La app requiere un _development build_ generado con EAS.

---

## 🚀 Instalación y arranque

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu-usuario/precioscan.git
cd precioscan

# 2. Instalar dependencias
npm install

# 3. Levantar el servidor de desarrollo
npx expo start --dev-client
```

Luego, en tu dispositivo físico con el development build instalado, escaneá el QR que aparece en la terminal para conectarte al bundler local.

### Generar un development build (primera vez)

```bash
# Configurar EAS
eas build:configure

# Generar el APK de desarrollo para Android
eas build --profile development --platform android
```

Instalá el APK en tu dispositivo, luego levantá el server con `npx expo start --dev-client` y escaneá el QR desde la pantalla Home del dev client.

---

## 🗂 Estructura del proyecto

```
src/
├── app/               # Rutas (expo-router, file-based)
│   ├── (tabs)/        # Navegación principal por tabs
│   │   ├── scans/     # Listado e historial de facturas
│   │   ├── catalog/   # Catálogo de productos
│   │   ├── compare/   # Comparador de precios
│   │   ├── cart/      # Carrito estimado
│   │   └── settings/  # Ajustes y tasa de cambio
├── features/          # Lógica por dominio (scan, catalog, prices, cart)
├── db/                # Esquema Drizzle, migraciones y repositorios
├── lib/               # Utilidades transversales
└── components/        # Componentes UI reutilizables
```

---

## 🗄 Modelo de datos (resumen)

| Entidad        | Descripción                                                                                |
| -------------- | ------------------------------------------------------------------------------------------ |
| `Store`        | Comercio donde se realizó la compra                                                        |
| `Product`      | Identidad canónica de un producto del catálogo                                             |
| `Scan`         | Una factura escaneada (comercio, fecha, imagen, moneda)                                    |
| `LineItem`     | Línea de la factura: texto OCR crudo + precio en moneda original + FK opcional a `Product` |
| `ExchangeRate` | Fecha, par de monedas, valor y tipo (`oficial` / `personal`)                               |

> **Regla de oro:** `LineItem.priceOriginal` siempre se guarda en la moneda de la transacción. El valor en USD se **deriva al vuelo** y nunca se persiste.

---

## 🗺 Roadmap

### Fase 0 — Fundaciones

- [x] Proyecto Expo + TypeScript inicializado
- [ ] Tooling: ESLint, Prettier, path aliases
- [ ] Navegación base con `expo-router`
- [ ] Development build con EAS
- [ ] Componentes UI base (Button, Input, Card, Screen)

### Fase 1 — Persistencia local

- [ ] Diseño del modelo de datos
- [ ] `expo-sqlite` + Drizzle + migraciones
- [ ] Capa de repositorios (acceso a datos desacoplado de la UI)
- [ ] Datos semilla para desarrollo

### Fase 2 — Captura manual de facturas

- [ ] Pantalla de cámara / galería
- [ ] Almacenamiento de imagen en filesystem
- [ ] Formulario editable de factura (cabecera + ítems)
- [ ] Persistir scan completo (transacción atómica)

### Fase 3 — OCR

- [ ] Integración de ML Kit
- [ ] Parser de texto OCR → estructura `ParsedInvoice`
- [ ] Pre-llenado del formulario con resultado OCR
- [ ] Revisión y corrección manual post-OCR

### Fase 4 — Catálogo y normalización

- [ ] Pantalla de catálogo de productos
- [ ] Vinculación `LineItem` → `Product`
- [ ] Búsqueda y sugerencias de productos

### Fase 5 — Comparador y tendencias

- [ ] Vista de precio histórico por producto
- [ ] Comparador multi-comercio (estilo Google Flights)
- [ ] Alertas de precio

### Fase 6 — Tasa de cambio

- [ ] Interfaz para cargar tasa manual (oficial / personal)
- [ ] Derivación de precios en USD al vuelo

### Fase 7 — Carrito estimado _(futuro)_

- [ ] Armar lista de compras
- [ ] Estimado de gasto total por comercio

### Fase 8 — Sync cloud _(futuro)_

- [ ] Autenticación (Supabase Auth)
- [ ] Sincronización de datos locales a la nube
- [ ] Acceso multi-dispositivo

---

## 🤝 Contribuciones

Este es un proyecto personal. Las contribuciones no están abiertas por ahora, pero los issues con feedback son bienvenidos.

---

## 📄 Licencia

MIT
