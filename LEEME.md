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

## Clic derecho sobre un recinto
Menú con **Ajustar polígono** (arrastrar vértices, clic en un lado agrega vértice, Mayús+clic quita uno, arrastrar dentro mueve el recinto; Enter/Esc termina), **Copiar** (queda pegado al cursor; clic para pegar, varias veces; Esc termina) y **Borrar**. Para desplazar el plano con el botón derecho, arrastre (más de 4 px).

## Plano de prueba
`pruebas/A02.00_planta_72dpi.png` (A02.00, 1:75, 72 px/pulgada → escala 1:75 y resolución 72).

## PDF
Lector propio, sin librerías ni internet (usa la descompresión nativa del navegador):
- **Planos**: dibuja el vector de la hoja (con rotación) y busca «ESCALA 1:N» en el texto para fijar la escala; siempre verifique con «Calibrar con 2 puntos».
- **Estándares de cadena**: extrae las frases normativas del PDF, sugiere parámetros (cd en dormitorios, dBA mínimo, cd en corredores, CO, pulsador en recepción) y usted elige qué adoptar.
- **Con plano en PDF** (por defecto): se detecta la **huella del edificio como un solo recinto**, sin seccionar espacios, y el prediseño cubre toda la planta (detectores en malla, estrobos de cielo por celdas, audibles por nivel sonoro). No considera muros interiores. Si se necesita separar espacios: clic derecho → Ajustar polígono, «Dibujar recinto», o el método «Rótulos + espacios sin rótulo» / «Solo por rótulos de área» (selector junto al botón «Detectar recintos automáticamente»).
- **Sin plano**: se crean recintos con dimensiones (Modo B) o dibujándolos.
- **Capas de cobertura** seleccionables: detectores, notificación visual y notificación auditiva (las dos últimas recortadas a cada recinto; colores: verde detectores, rojo visual, azul auditiva).
- No lee PDF escaneados, cifrados ni imágenes incrustadas; en ese caso exporte a PNG/JPG o a .csv/.json. Probado con el plano A02.00 y el Manual de Disposiciones Técnicas 2013 (184 páginas).

## Verificación normativa
Verificado contra las copias provistas: NFPA 72:2022 (Handbook) y NFPA 101:2015. Otras ediciones muestran «sección no verificada». Símbolos: NFPA 170:2018, Tablas 8.3 y 8.4.2.

## Pendiente (paso 2)
Base de datos Supabase (proyectos, recintos, dispositivos, estándares de cadena).

## Estructura en módulos
1. **Proyecto y criterios de diseño:** datos del proyecto, normas, estándar de cadena, parámetros NFPA y equipos.
2. **Ejecución:** planta o recintos, generar prediseño, coberturas, resultados y archivo (borrador .json, PNG, CSV, informe).
3. **Glosario de simbología:** imagen y explicación de cada símbolo, capas de cobertura y siglas.

## Jurisdicción
Por defecto Costa Rica: Reglamento Nacional de Protección contra Incendios 2023, §2.4.2 → NFPA 72:2019 y NFPA 101:2021 en español (se pueden elegir ediciones más recientes, §2.4.1). Las citas de sección solo están verificadas para NFPA 72:2022 y NFPA 101:2015; con otras ediciones se muestran como "no verificado".

## Subrecintos
Un recinto cuyo contorno cae por completo dentro de otro se trata como subrecinto: el contenedor no recibe dispositivos ahí, y el subrecinto se prediseña por separado con su propio tipo. Método «Huella + subrecintos por rótulos» los crea automáticamente.

## Notificación audible y edición de dispositivos
- Solo los estrobos necesarios llevan bocina (visual + auditivo); el resto queda solo visual. El nivel sonoro combinado (dBA) se verifica en todo el recinto y se ve en la capa «Nivel sonoro (dBA)» (rojo: bajo el requerido; verde: cumple; naranja: >110 dBA); con la capa activa, el valor bajo el cursor aparece en la barra de estado.
- Cada dispositivo se puede arrastrar (se marca con anillo amarillo), seleccionar con clic para cambiar función, montaje pared/cielo, candelas y dBA en el panel «Dispositivo seleccionado», o con clic derecho agregar/quitar bocina o borrar. Cada cambio vuelve a verificar el recinto.

## Dispositivos manuales y borrado de recintos
- «＋ Dispositivo» (con el selector de tipo) agrega dispositivos a mano en el plano, además de los automáticos; se conservan al volver a generar y entran en la verificación del recinto.
- «Borrar recintos…» abre una lista con casillas para elegir cuáles borrar.
- Las herramientas y las capas están en el panel izquierdo del módulo 2.

## Paneles plegables y edición rápida
- Cada tarjeta de los módulos 1 y 2 se pliega con clic en su título; «Plegar todo / Desplegar todo» arriba del panel. Al seleccionar un dispositivo se abre su panel.
- Clic derecho sobre un dispositivo: Mover (clic en el nuevo lugar), Agregar similares (clona tipo, candelas y dBA con un clic por cada uno; Esc termina), Cambiar tipo/propiedades, bocina sí/no y Borrar. Los mismos botones están en el panel «Dispositivo seleccionado».
- Recinto rectangular: clic en dos esquinas o arrastrar. Los recintos nuevos toman el nombre del texto del plano que cae dentro.
