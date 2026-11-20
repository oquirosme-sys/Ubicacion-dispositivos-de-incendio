# Prediseño de alarmas contra incendio (paso 1: HTML)

Archivo único `index.html`, sin librerías ni CDN, funciona sin internet (doble clic).

## Uso rápido
1. **Proyecto**: edición NFPA 72 y NFPA 101, ocupación, datos del edificio → tabla de requisitos obligatorios.
2. **Estándar de cadena**: Hyatt / Marriott integrados; se puede cargar otro (.json o .csv con columnas ID, Tema, Regla, Referencia).
3. **Planta y recintos**
   - Modo A: cargar plano (PNG/JPG), fijar escala (1:N + px/pulgada, o calibrar 2 puntos), crear recintos con «Recinto por clic», «Dibujar recinto» o «Recinto rectangular»; marcar salidas, FACP, AHU y ascensores.
   - Modo B: crear un recinto con ancho, fondo y altura (el botón Crear coloca la salida en el muro elegido).
4. **Generar prediseño**: detectores (humo/calor/CO/ducto), estrobos con candelas, audible, pulsadores y módulos, con verificación independiente y supuestos/limitaciones junto a cada resultado.
5. **Archivo**: borrador .json, PNG, CSV de dispositivos e informe imprimible (PDF desde el navegador).

## Plano de prueba
`pruebas/A02.00_planta_72dpi.png` (A02.00, 1:75, 72 px/pulgada → escala 1:75 y resolución 72).

## PDF
Sin librerías, el navegador no puede dibujar PDF. Opciones: exportar la hoja a PNG (como el de `pruebas/`), o colocar `pdfjs/pdf.min.js` y `pdfjs/pdf.worker.min.js` locales junto al HTML (no se usa CDN). La ruta PDF **no se probó**.

## Verificación normativa
Verificado contra las copias provistas: NFPA 72:2022 (Handbook) y NFPA 101:2015. Otras ediciones muestran «sección no verificada». Símbolos: NFPA 170:2018, Tablas 8.3 y 8.4.2.

## Pendiente (paso 2)
Base de datos Supabase (proyectos, recintos, dispositivos, estándares de cadena).
