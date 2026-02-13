# Echidna Board Test - Firmata

Herramienta web para testear la placa de robotica educativa **Echidna** desde el navegador, comunicandose mediante el protocolo **Firmata** a traves de la **Web Serial API**.

## Requisitos

- **Navegador**: Google Chrome o Chromium (escritorio). La Web Serial API no esta disponible en Firefox ni Safari.
- **Placa Echidna** conectada por USB con **StandardFirmata** cargado.

## Cargar StandardFirmata en la placa

1. Abre el **Arduino IDE**.
2. Ve a **Archivo > Ejemplos > Firmata > StandardFirmata**.
3. Selecciona la placa **Arduino Nano** y el procesador **ATmega328P** (o ATmega328P Old Bootloader si falla).
4. Selecciona el puerto serie correspondiente.
5. Haz clic en **Subir**.

## Uso

La aplicacion se sirve en la ruta `/echidna-firmata-test/`. Accede desde Chrome/Chromium a la URL del servidor (por ejemplo `https://tu-servidor.com/echidna-firmata-test/`).

1. Abre la URL de la aplicacion en Chrome/Chromium.
2. Conecta la placa Echidna por USB.
3. Haz clic en **Conectar** y selecciona el puerto serie en el dialogo del navegador.
4. Espera a que el indicador cambie a **Conectado** (tarda ~3 segundos mientras la placa se reinicia).
5. Activa cada periferico individualmente con su boton dedicado.

> Tambien puedes abrir `index.html` directamente desde el sistema de ficheros para desarrollo local.

### Probar sensores

Haz clic en **Leer** junto al sensor que quieras probar. Aparecera el valor en tiempo real con una barra de progreso. Haz clic en **Parar** para dejar de leer.

| Sensor               | Pin | Tipo     | Rango      |
|----------------------|-----|----------|------------|
| Joystick X           | A0  | Analogico | 0 - 1023  |
| Joystick Y           | A1  | Analogico | 0 - 1023  |
| LDR (Luz)            | A3  | Analogico | 0 - 1023  |
| Temperatura          | A6  | Analogico | 0 - 1023 (muestra conversion a Celsius) |
| Microfono            | A7  | Analogico | 0 - 1023  |
| Boton Derecho (SR)   | D2  | Digital   | 0 (pulsado) / 1 (no pulsado) |
| Boton Izquierdo (SL) | D3  | Digital   | 0 (pulsado) / 1 (no pulsado) |

> Los botones usan `INPUT_PULLUP`: el valor es **0** cuando se pulsa y **1** cuando no.

> La temperatura se convierte a Celsius con la formula: `(ADC * 0.4658) - 50.0`

### Probar actuadores

Haz clic en el boton del actuador para activarlo. Los actuadores PWM muestran un slider para ajustar la intensidad (0-255). Los digitales se encienden/apagan con el boton.

| Actuador      | Pin | Tipo    | Control         |
|---------------|-----|---------|-----------------|
| Buzzer        | D10 | PWM     | Slider 0-255    |
| LED Verde     | D11 | PWM     | Slider 0-255    |
| LED Naranja   | D12 | Digital | Encender/Apagar |
| LED Rojo      | D13 | Digital | Encender/Apagar |
| RGB Rojo      | D9  | PWM     | Slider 0-255    |
| RGB Verde     | D5  | PWM     | Slider 0-255    |
| RGB Azul      | D6  | PWM     | Slider 0-255    |

> El LED RGB incluye una previsualizacion del color resultante junto a los sliders.

### Pines GPIO

Los pines de proposito general (A2, D4, D7, D8) se pueden configurar como:

- **Entrada Analogica** (solo A2): lee valores 0-1023.
- **Entrada Digital**: lee estado HIGH/LOW con pull-up interno.
- **Salida Digital**: alterna entre HIGH y LOW con un boton.

Selecciona el modo en el desplegable antes de activar el pin.

## Pinout de la placa Echidna

```
                    ┌─────────────┐
                    │  USB (Nano) │
                    ├─────────────┤
         Buzzer ── D10        A7 ── Microfono
      LED Verde ── D11        A6 ── Temperatura
    LED Naranja ── D12        A5 ── I2C SCL (Accel.)
       LED Rojo ── D13        A4 ── I2C SDA (Accel.)
       RGB Rojo ── D9         A3 ── LDR (Luz)
       RGB Azul ── D6         A2 ── GPIO
      RGB Verde ── D5         A1 ── Joystick Y
       GPIO D8  ── D8         A0 ── Joystick X
       GPIO D7  ── D7
       GPIO D4  ── D4
   Boton Der SR ── D2
   Boton Izq SL ── D3
                    └─────────────┘
```

## Aplicacion Web Progresiva (PWA)

La aplicacion es una **PWA** instalable. Permite:

- **Instalar** la app en el escritorio o pantalla de inicio desde el navegador.
- **Funcionar offline** una vez cargada (los recursos se cachean via service worker).
- **Actualizarse automaticamente** cuando se despliega una nueva version.

### Generar iconos PNG

El manifest referencia iconos PNG para maxima compatibilidad. Para generarlos:

1. Abre `generate-icons.html` en Chrome.
2. Haz clic en **Descargar** en cada icono.
3. Coloca los ficheros PNG en la misma carpeta que `index.html`.

Ficheros necesarios: `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`.

> Sin los PNG, la app funciona igualmente usando el icono SVG, pero algunos navegadores no mostraran la opcion de instalacion.

### Estructura de ficheros

```
echidna-firmata-test/
  .github/workflows/
    deploy-pages.yml      Workflow de GitHub Actions para GitHub Pages
  .nojekyll               Desactiva Jekyll en GitHub Pages
  index.html              Aplicacion principal
  manifest.json           Manifest PWA
  sw.js                   Service worker (cache offline)
  icon.svg                Icono vectorial
  icon-192.png            Icono 192x192 (generar con generate-icons.html)
  icon-512.png            Icono 512x512 (generar con generate-icons.html)
  icon-maskable-512.png   Icono maskable (generar con generate-icons.html)
  generate-icons.html     Utilidad para generar los PNG
  README.md               Este fichero
```

### Actualizar la version cacheada

Cuando se modifique la aplicacion, cambia el valor de `CACHE_NAME` en `sw.js` (por ejemplo de `echidna-test-v1` a `echidna-test-v2`). Esto fuerza la descarga de la nueva version y la limpieza de la cache antigua.

## Despliegue en GitHub Pages

### Configuracion inicial

1. Crea un repositorio en GitHub (por ejemplo `echidna-firmata-test`).
2. Sube el codigo al repositorio:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/TU-USUARIO/echidna-firmata-test.git
   git push -u origin main
   ```
3. En el repositorio de GitHub, ve a **Settings > Pages**.
4. En **Source**, selecciona **GitHub Actions**.
5. El workflow incluido (`.github/workflows/deploy-pages.yml`) se ejecutara automaticamente con cada push a `main`.

### URL de la aplicacion

Una vez desplegado, la aplicacion estara disponible en:

```
https://TU-USUARIO.github.io/echidna-firmata-test/
```

> La Web Serial API requiere un contexto seguro. GitHub Pages sirve por HTTPS, por lo que funciona directamente.

> Los paths del manifest y service worker son relativos, asi que funcionan con cualquier nombre de repositorio.

### Despliegue manual

Si prefieres desplegar manualmente en lugar de usar GitHub Actions, tambien puedes ir a **Settings > Pages > Source** y seleccionar **Deploy from a branch** con la rama `main` y la carpeta `/ (root)`.

## Detalles tecnicos

- **Comunicacion**: protocolo Firmata binario sobre Web Serial API a **57600 baudios**.
- **Sin dependencias externas**: fichero HTML unico y autocontenido, sin librerias ni instalacion.
- **PWA**: service worker con estrategia cache-first, manifest con iconos SVG y PNG.
- **Protocolo implementado**: SET_PIN_MODE, DIGITAL_MESSAGE, ANALOG_MESSAGE, REPORT_ANALOG, REPORT_DIGITAL, SYSTEM_RESET.
- **Compatibilidad**: cualquier placa Arduino compatible con StandardFirmata (Nano, Uno, Mega...) con el pinout Echidna.

## Solucion de problemas

| Problema | Solucion |
|----------|----------|
| No aparece el boton Conectar | Asegurate de usar Chrome/Chromium en escritorio. La Web Serial API no funciona en movil. |
| No aparece el puerto serie | Verifica que el cable USB es de datos (no solo de carga) y que los drivers estan instalados. |
| Se conecta pero no responde | Confirma que StandardFirmata esta cargado en la placa. Prueba a desconectar y reconectar. |
| Lecturas erraticas en sensores | Es normal cierto ruido en las entradas analogicas. Verifica las conexiones fisicas. |
| El buzzer no suena | El sonido se genera por PWM a ~490 Hz. Ajusta el slider a un valor alto (>100) para comprobar. |

## Licencia

Este proyecto es parte del ecosistema educativo [Echidna](https://echidna.es).
