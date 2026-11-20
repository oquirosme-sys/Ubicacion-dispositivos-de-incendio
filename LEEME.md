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

## Imán de dibujo y generación incremental
- Al dibujar recintos («Dibujar recinto» y «Recinto rectangular») el cursor se engancha a las esquinas de muro (cuadro verde) y, si no hay esquina cerca, al borde de muro más cercano (círculo azul). Se desactiva con la casilla «Imán a esquinas y muros» o manteniendo Alt.
- «Generar prediseño» solo calcula los recintos nuevos o modificados: lo ya acomodado (incluso lo que movió o editó a mano) no se mueve. «↻ Regenerar todo» recalcula todo; clic derecho sobre un recinto → «Regenerar este recinto» recalcula solo ese. Si dibuja un subrecinto dentro de uno ya generado, regenere el contenedor para que descuente el subrecinto.
- Cobertura visual: cuadrados anidados desde el muro (pared) o centrados (cielo), con las medidas de las tablas 18.5.5.7.1(a)/(b).

## Cobertura limpia
- La cobertura de detectores y la de notificación se recortan al recinto que las contiene.
- Clic derecho sobre un recinto o uno de sus dispositivos → «Ocultar / Mostrar cobertura» de ese recinto (oculta detectores, visual, auditiva y nivel sonoro de ese recinto; los dispositivos se quedan). En la barra superior: «Ocultar todas / Mostrar todas».

## Guardado en el navegador
- El trabajo (proyecto, plano, recintos, dispositivos, coberturas ocultas) se guarda solo en IndexedDB de este navegador y se restaura al recargar la página o al publicar una mejora; no usa cookies ni localStorage. Casilla «Recordar mi trabajo en este navegador» (Archivo) para activarlo o desactivarlo y botón «Olvidar lo guardado».
- Es local a ese equipo y navegador: el borrador .json sigue siendo el medio portátil (ahora también restaura los resultados de cada recinto sin mover dispositivos).
- Nota para la norma de herramientas Sinergia: esta herramienta se aparta de «no persistir en el navegador» porque el usuario lo pidió expresamente; es opcional y desactivable.

## Requisitos del sistema de alarma (según área, ocupación y rociadores)
- El módulo 1 pregunta solo lo que el ingeniero eléctrico maneja: ocupación, niveles, altura, habitaciones, área agregada de incendio, área de la planta y si lleva rociadores (únicamente por su efecto sobre la alarma). No pide datos de supresión (ocupantes, tubería vertical, riesgo de contenidos).
- Evalúa solo requisitos del sistema de alarma contra el Reglamento Nacional de Protección contra Incendios 2023 (§11.1, §11.3, §11.4, §11.6, §11.7) y las NFPA elegidas. La columna «Tipo» dice si es Obligatorio, Por confirmar, Recomendado o No obligatorio; arriba hay un resumen de lo obligatorio y la tabla va en un panel plegable.
- En Costa Rica el recorrido máximo a una estación manual es 60 m (§11.1.7); el prediseño lo usa.

## Bloques de equipo (matriz de supervisión)
- La tabla de equipos y señales sale de Matriz_Supervision_NFPA72_2.xlsx (Sinergia). Con «+ Equipo» se coloca en el plano cualquier equipo de la matriz (bomba, generador, ATS, damper, ascensor…); «+ Manejadora» y «+ Ascensor» siguen igual.
- Clic derecho sobre un bloque: cambiarlo por cualquier equipo de la matriz (o por salida/FACP), ver los dispositivos y señales que pide, mover o borrar. También se arrastra.
- Cada bloque muestra una nota con los dispositivos requeridos por unidad (cantidad × P/N Simplex y función); casilla «Notas de equipos». Cada bloque suma una unidad a «Equipos supervisados» y a los módulos totales.

## Estrobo de pared (Figura 18.5.5.7.1)
- El estrobo de pared se coloca a mitad de un muro y se pega al muro: al elegir «Pared» en el panel, al agregarlo o al arrastrarlo siempre queda en el muro más cercano. De ese muro parten los cuadrados anidados de cobertura (6.1, 9.1, 12.2… m).
- Se prueba cada muro del recinto y se elige el que da el cuadrado más pequeño que lo contiene. La verificación del recinto comprueba que el recinto cabe en el cuadrado que cubren las candelas del aparato.
- Los estrobos de pared se pueden rotar: clic derecho → «Rotar 90°» / «Rotar −90°», botones ↻ ↺ del panel «Dispositivo seleccionado» o tecla R (Mayús+R en sentido contrario). Cada giro pasa el estrobo al punto medio del siguiente muro del recinto y la cobertura parte de ese muro.

## Nivel sonoro por ocupación
- En el mapa de nivel sonoro (dBA) todo lo que baja del nivel requerido o sube de 110 dBA se pinta en rojo. Una leyenda sobre el lienzo indica, por recinto, el nivel que requiere y el mínimo y máximo que alcanza hoy (en rojo y negrita si está fuera de parámetros).
- Módulo 1 → Criterios: tabla «Niveles sonoros requeridos por tipo de recinto» (ambiente supuesto + 15 dB; dormir ≥ 75 dBA; máximo 110 dBA). El panel «Recinto seleccionado» también muestra el nivel requerido.

## Deshacer, módulos para Revit y giro del símbolo
- Ctrl+Z deshace y Ctrl+Y (o Ctrl+Mayús+Z) rehace, también con los botones ↶ ↷ de «Herramientas». Cada acción es un paso: generar prediseño, mover, editar, borrar, agregar equipos, rotar… (hasta 60 pasos).
- Cada bloque de equipo dibuja a la par un símbolo por módulo requerido (AIM monitoreo / AOM control) y su nota con los P/N; casilla «Módulos junto a equipos». El resumen de resultados totaliza los módulos por P/N y el CSV de dispositivos incluye cada equipo y cada módulo (con P/N, recinto y la ubicación del bloque) para que el modelador los replique en Revit.
- Giro del símbolo sobre su propio eje: campo «Giro del símbolo (°)» y botones ↺ −15° / ↻ +15° / ⟳ 90° en el panel, clic derecho → «Girar símbolo 90°», o tecla G (Mayús+G en sentido contrario). Es solo rotación del dispositivo: no cambia su posición ni su cobertura (la rotación de pared/muro sigue con R).

## Leyenda de nivel sonoro y alerta de dispositivos encimados
- La leyenda del nivel sonoro es un panel plegable encima del lienzo (no tapa la planta). Con la casilla «Mostrar también sobre el plano» se dibuja sobre el plano, en la esquina que se elija, para exportar la imagen.
- Dispositivos a menos de 0.5 m uno de otro (o en el mismo punto) se marcan con un círculo rojo discontinuo, aparece una alerta arriba de Resultados con la lista de pares y la barra de estado avisa al generar, mover o agregar.

## Precableado (módulo 3)
- Parte de los dispositivos y del panel (marcador «+ FACP/recepción») ya colocados. Genera lazos SLC (detectores, pulsadores y módulos de los bloques de equipo) y circuitos NAC (estrobos, bocinas, parlantes) recorriendo siempre al dispositivo más cercano desde el panel, con recorrido ortogonal, y cierra cada circuito cuando se incumple el límite (dispositivos máx. por lazo SLC, I máx. NAC 2.4 A o tensión mínima).
- Clase A (con retorno, anillo completo como peor caso) o B (un recorrido) y cable (catálogo de la memoria) para SLC y para NAC; se pueden cambiar por circuito en la tabla y se recalcula al instante.
- Fórmulas de la memoria de cálculo de alarmas (Calc.circuito): R lazo = 2·L/1000·Ω/km; caída = I·R; V disponible = V nominal ×  0.000000in de vida − caída (por defecto 24 V × 85 % = 20.4 V; mínimo 16 V). Corrientes en alarma del catálogo Simplex de la memoria.
- Se ve en el plano con la capa «Precableado» (línea continua = ida, discontinua = retorno clase A). Exporta CSV (circuito, clase, cable, longitud, corriente, caída, estado, dispositivos). Entra en deshacer y en el borrador .json.
- No sigue canalizaciones ni evita obstáculos; el límite de dispositivos del lazo depende del panel (verificar la ficha). La integración directa con la memoria de cálculo (baterías y caída por circuito) queda pendiente.

## Exportar a CAD (.dxf)
- Archivo → «Exportar a CAD (.dxf)»: dibujo a tamaño real (1:1) en metros o milímetros, DXF R12 (ASCII) que abren AutoCAD, Revit y BricsCAD; en AutoCAD se guarda como .dwg. El .dwg no se genera directamente (formato binario propietario; la herramienta es un solo archivo sin librerías ni internet).
- Capas ALARMA-*: recintos, detectores, notificación, pulsadores, módulos, equipos, salidas, FACP, texto, coberturas (opcional), SLC, NAC y límite de la hoja del plano. Los símbolos NFPA 170 son bloques (SIM_*) con el giro de cada dispositivo; los equipos llevan sus módulos y la nota de P/N; el precableado va como polilíneas (retorno de clase A discontinuo).
- La escala de impresión 1:N fija el tamaño de símbolos y rótulos (símbolo de 4 mm en papel × N). El eje Y se invierte (CAD crece hacia arriba); el límite de la hoja indica dónde superponer el plano original.

## Dispositivos manuales a la mano
- Tarjeta «Prediseño» → «Dispositivos manuales»: selector de tipo + «Colocar en el plano» (mismo efecto que «+ Dispositivo»), y «CO en cocinas y recintos con combustión», que pone un detector de CO en cada recinto tipo cocina, lavandería, cuarto de máquinas o parqueo que aún no lo tenga (el prediseño solo lo coloca si el estándar de cadena lo exige o si el recinto tiene CO «Forzar»). Quedan como dispositivos manuales y se conservan al regenerar.
- El menú del botón derecho ya no se abre junto con el menú del navegador.

## Vista del precableado y símbolo de CO
- Módulo 3 (Precableado): tarjeta «Plano con dispositivos y precableado» con la misma vista del módulo 2 (plano, recintos, dispositivos, bloques de equipo y cableado; sin coberturas ni notas), con zoom por rueda, desplazamiento por arrastre y su propio «Ajustar vista». Se actualiza al generar, recalcular o quitar el precableado.
- Símbolo de CO como en la simbología CDCLH-001S: hexágono negro con triángulo blanco y «CO» abajo a la derecha (también en el .dxf). Esa leyenda indica base de CO Simplex 4098-9771 con módulo TC4 4905-9835.
- Cada detector/base de CO lleva su módulo TC4 4905-9835 (simbología CDCLH-001S): se dibuja a la par del detector (casilla «Módulos junto a equipos»), se totaliza por P/N en el resumen, sale en el CSV y en el .dxf (bloque SIM_MODULO_TC4) y cuenta como un punto más del lazo SLC en el precableado. La memoria no tiene su corriente: se toma como 0 mA y queda marcada «aproximada»; verifique la ficha del TC4.
