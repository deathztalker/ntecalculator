# 📘 NTE Bond Tracker: Documentación Técnica y Arquitectura

Este documento sirve como referencia para entender cómo está programada la aplicación, cómo se gestionan los datos y cómo actualizarla en el futuro.

## 1. Arquitectura del Sistema
El proyecto es una **Aplicación Web Progresiva Standalone (Vanilla)**. Esto significa que no utiliza frameworks pesados como React o Vue, ni requiere NodeJS para compilar. Todo el motor corre de forma ultra-ligera en un solo archivo `index.html` que contiene el HTML, CSS y JavaScript.

## 2. Gestión de Estado (Base de Datos Local)
La app no tiene backend. Toda la información del usuario se guarda en el navegador del usuario utilizando `localStorage` bajo la clave maestra `nte_v4`.

El objeto global que maneja la memoria se llama `S` (Estado) y contiene:
*   `S.ap`: Un diccionario que mapea el ID del personaje con su Total de AP (Ej. `{ nanally: 18000, chiz: 400 }`).
*   `S.day`: Cuenta cuántos regalos ha recibido un personaje hoy (Máximo 3).
*   `S.lastDate`: Guarda el string del día actual (Ej. `"2026-05-23"`). Cuando abres la app, si tu fecha no coincide con esta, **resetea automáticamente `S.day` a 0**.
*   `S.calLog`: Un registro histórico (Heatmap) de regalos para pintar el calendario de los últimos 30 días.

## 3. Estructuras de Datos Maestras (Modificables)
En el código fuente, existen tres grandes matrices (Arrays) que alimentan toda la UI:

### `CHARS` (Personajes)
Contiene los **18 personajes oficiales**. Cada objeto tiene:
*   `id`: Identificador único en minúsculas.
*   `el` y `rar`: Elemento (Cosmos, Anima, etc.) y Rareza (S o A).
*   `col`: El código Hexadecimal de su color principal. Este color se inyecta en el CSS como `var(--hc)` para crear los **resplandores de neón dinámicos** en las tarjetas.
*   `buff`: El Stat pasivo que se desbloquea a Nivel 10.
*   `gifts`: La lista de regalos favoritos.

### `SHOPS` (Tiendas y Rutas)
Agrupa los regalos por ubicación física dentro de Hethereau. El motor visual lee esta matriz y, gracias a la función `renderShopFaces()`, inserta dinámicamente los rostros de los personajes (Avatar Pills) al lado de los regalos que necesitan.

### `UNLOCKS` (Recompensas por Nivel)
Define qué te da el juego cada 2 niveles (Llamadas, Misiones, Annuliths). 

## 4. Motores Lógicos Principales
*   **Calculadora Inversa de AP (`setAP`)**: El jugador no introduce AP Totales. Introduce "Nivel" y "AP en Nivel". El motor lee la matriz `THRESH` (Múltiplos de 5600) y calcula el AP global en segundo plano.
*   **Eficiencia de Fons (`renderDetail`)**: Al abrir un personaje, la app escanea sus regalos de pago, divide `Precio / AP` y asigna medallas en tiempo real:
    *   **F2P (Verde)**: Si el precio es 0.
    *   **Mejor Valor (Azul)**: El regalo con el ratio Fons/AP más bajo.
    *   **Muy Caro (Rojo)**: Si el coste supera los 20 Fons por cada 1 AP (Ej. La infame *Bunny Box*).

## 5. Diseño y Estética (Premium HD)
*   **Glassmorphism**: Los paneles utilizan `backdrop-filter: blur(24px) saturate(1.4)` para crear ese efecto de cristal esmerilado AAA.
*   **Background Dinámico**: El fondo no es una imagen. Es una combinación de un filtro SVG de ruido (Noise) y esferas de gradientes radiales animadas con CSS (`@keyframes drift`) que se mueven lentamente para dar vida a la app.
*   **Píldoras y Badges**: Componentes UI altamente estilizados para los roles y elementos, diseñados para coincidir con la vibra Cyberpunk del juego.

---
**Nota de Mantenimiento:** Para agregar un nuevo personaje en futuras expansiones (ej. V1.2), simplemente clona un bloque dentro del array `CHARS`, asigna su Elemento y añade sus 3-4 regalos favoritos. La app recalculará automáticamente sus rutas en tienda, sus días para MAX y sus Fons necesarios.
