# GUÍA DE DESPLIEGUE EN CLOUDFLARE PAGES
**Plataforma Web Comercial Digital – Cooperativa Mapuche Newen Leufu**

Esta plataforma transaccional integrada ha sido optimizada para un rendimiento instantáneo (carga < 300ms) mediante un stack moderno de **Vue 3, TypeScript y Tailwind CSS v4**. Está completamente lista para ser desplegada de manera gratuita, rápida y global a través de **Cloudflare Pages**.

---

## ⚙️ Parámetros de Configuración en Cloudflare

Al conectar tu repositorio Git en el panel de Cloudflare Pages, utiliza exactamente los siguientes parámetros de compilación:

*   **Nombre del Proyecto:** `newen-leufu-mockups`
*   **Framework Preset:** `Vite` (o *None*)
*   **Directorio Raíz (Root Directory):** `/` *(Por defecto, ya que este repositorio contiene el código de la app directamente en su raíz)*.
*   **Comando de Compilación (Build Command):** `npm run build`
*   **Directorio de Salida (Build Output Directory):** `dist`
*   **Variables de Entorno (Environment Variables):**
    *   `NODE_VERSION`: `20` o superior (Vite v8 requiere Node 18+).

---

## 🛠️ Paso a Paso para Desplegar desde tu Git

1.  **Crea un repositorio en tu GitHub/GitLab:**
    *   Crea un repositorio vacío (ej: `web-cooperativa-newen-leufu`).
2.  **Sube tu código local:**
    Ejecuta en tu terminal local:
    ```bash
    git branch -m main
    git remote add origin <URL_DE_TU_REPOSITORIO_GITHUB>
    git add .
    git commit -m "feat: setup unified transaccional mockup with multi-theme switcher and real pdf experiences content"
    git push -u origin main
    ```
3.  **Configura Cloudflare Pages:**
    *   Inicia sesión en [dash.cloudflare.com](https://dash.cloudflare.com/).
    *   Navega a **Workers & Pages** -> **Create application** -> **Pages** -> **Connect to Git**.
    *   Selecciona tu repositorio recién creado.
    *   Aplica los **Parámetros de Configuración** detallados arriba (deja el *Root Directory* por defecto).
    *   Presiona **Save and Deploy**.
4.  **¡Listo!** En menos de 1 minuto, Cloudflare Pages te proporcionará un enlace global tipo `https://newen-leufu-mockups.pages.dev` totalmente funcional.

---

## 🔄 Cómo Probar las Dos Propuestas en Línea

Gracias a la integración del **Multi-Theme Switcher**, no necesitas subir dos proyectos separados. Cloudflare compilará una sola aplicación que soporta ambas propuestas de forma reactiva y bilingüe en tiempo real:

1.  **Vista de la Propuesta 2 — "Experiencia Moderna" (Por defecto):**
    *   Carga directamente: `https://tu-proyecto.pages.dev/`
2.  **Vista de la Propuesta 1 — "Raíces Vivas" (Identidad Territorial Profunda):**
    *   Carga con parámetro de subruta: `https://tu-proyecto.pages.dev/?variant=traditional`
3.  **Flujo Interactivo en vivo:**
    *   También puedes alternar dinámicamente entre ambas propuestas usando el panel flotante **"EVALUAR PROPUESTAS"** ubicado en la esquina inferior izquierda de la pantalla, lo que cambiará el tema, colores, fondos, tipografías y secciones (como la de Cosmovisión) de inmediato sin recargar la página.
