
[English](README.md) | [简体中文](README.zh.md) | [한국어](README.ko.md) | **Español**

# survivalcraft-js-tools
Herramienta HTML de un solo archivo para ejecutar JavaScript y teletransportarse en Survivalcraft API 1.9.3.1 mediante el Control Remoto de JavaScript del juego.

# Ejecutor y teletransportador JS de Survivalcraft

Una herramienta HTML de un solo archivo para ejecutar JavaScript contra **Survivalcraft API 1.9.3.1** en Android, usando el servidor de Control Remoto de JavaScript integrado en el juego.

Te ofrece un editor de texto real (sin límite de caracteres), un menú desplegable de comandos predefinidos útiles y un teletransportador de un toque.

---

## Qué hace

- **Pestaña Ejecutor JS** — escribe o pega cualquier JavaScript que acepte la API del juego, envíalo y ve el resultado.
- **Menú de preajustes** — elige entre comandos precargados con descripciones claras. Al seleccionar uno, su código se carga en el editor para que puedas revisarlo, editarlo o ejecutarlo directamente.
- **Pestaña Teletransportador** — escribe X/Y/Z y teletranspórtate. También incluye botones de ajuste fino, "¿Dónde estoy?" y guardar/ir al punto de aparición.

---

## Requisitos

- **Survivalcraft API 1.9.3.1** (Android). La versión estándar de Play Store no incluye el Control Remoto de JavaScript.
- **Chrome para Android** (o un navegador basado en Chromium).
- El juego debe estar **abierto con un mundo cargado** y el **Control Remoto activado**.
- El juego debe permanecer en **primer plano** o en **pantalla dividida** mientras ejecutas scripts. Si Android pausa el juego, su servidor muere.

---

## Compatibilidad de plataformas

Esta herramienta se creó y probó en **Android**. El protocolo subyacente (HTTP POST a `127.0.0.1:7765`) no es exclusivo de Android, pero el juego y el navegador deben ejecutarse en el **mismo dispositivo**, porque `127.0.0.1` apunta a la dirección de bucle local del dispositivo donde esté el navegador.

| Plataforma | Estado | Notas |
|---|---|---|
| Android | ✅ Probado | Se requiere pantalla dividida para que el juego permanezca en primer plano |
| Windows / Linux | ✅ Debería funcionar | Ejecuta el juego y Chrome en el mismo PC; no se necesita pantalla dividida |
| macOS | ⚠️ Sin probar | Solo si la compilación de la API y el Control Remoto JS están disponibles |
| iOS | ⚠️ Sin probar | El control remoto JS se añadió originalmente como solución para iOS, así que podría funcionar |
| Navegador del móvil → juego en PC | ❌ No funciona | `127.0.0.1` no puede cruzar dispositivos |

### Si estás en un PC

No necesitas este archivo HTML. Usa `curl`, `requests` de Python o cualquier cliente HTTP para hacer POST directamente al servidor del juego:

```
curl -X POST http://127.0.0.1:7765/ -H "password: YOUR_PASSWORD" -d 'findSubsystem("Players").PlayersData.Count'
```

La herramienta HTML existe principalmente porque los teléfonos rara vez tienen instalado un cliente HTTP usable, y escribir JavaScript largo en una terminal móvil es tedioso.

## Configuración

### 1. Activa el Control Remoto en el juego

- Inicia Survivalcraft API y carga un mundo.
- Abre el menú del juego.
- Busca **JavaScript Remote Control**.
- Actívalo (**ON**).
- Anota el **puerto** (por defecto `7765`) y la **contraseña** que se muestran en pantalla.

### 2. Activa la línea de comandos de Chrome en dispositivos sin root

- Abre Chrome → `chrome://flags`.
- Busca **"Enable command line on non-rooted devices"**.
- Ponlo en **Enabled** y reinicia Chrome.

### 3. Crea el archivo de línea de comandos de Chrome

Este archivo desactiva la seguridad web de Chrome para que pueda comunicarse con el servidor local del juego.

Necesitas una forma de ejecutar un comando de shell en tu teléfono. **aShell You** con **Shizuku** funciona. También **ADB desde un PC**.

Ejecuta este comando exacto:

```bash
echo "chrome --disable-web-security --user-data-dir=/data/user/0/com.android.chrome/app_chrome/Default" > /data/local/tmp/chrome-command-line
```

4. Fuerza la detención y vuelve a abrir Chrome

Ajustes → Aplicaciones → Chrome → Forzar detención. Luego vuelve a abrir Chrome.

5. Verifica que el indicador esté activo

Visita chrome://version en Chrome. En Command Line deberías ver:

```
chrome --disable-web-security --user-data-dir=...
```

Si aparece, estás listo.

6. Pon Chrome y el juego en pantalla dividida

Android suspenderá el juego si cambias de aplicación, matando el servidor de Control Remoto. Pon Chrome y Survivalcraft en pantalla dividida para que ambos sigan ejecutándose.

---

Uso de la herramienta

1. Abre survivalcraft-js-runner.html en Chrome (desde tu carpeta Descargas o donde lo hayas guardado).
2. Confirma que la contraseña y el puerto de la parte superior coinciden con lo que muestra el juego.
3. Pestaña Ejecutor JS — elige un preajuste del menú o escribe tu propio código, luego pulsa Run.
4. Pestaña Teletransportador — introduce X/Y/Z y pulsa Teleport.

El cuadro de salida muestra el JSON sin procesar que devuelve el juego.

---

## Cómo funciona

Todo el entorno se ejecuta localmente en tu teléfono. Nada viaja por internet.

```
[Tu teléfono]
     │
     ├── Survivalcraft API (ejecutándose en una mitad de la pantalla dividida)
     │        └── Servidor HTTP integrado en 127.0.0.1:7765
     │             (acepta POST con cabecera "password" + cuerpo JS)
     │
     └── Chrome (ejecutándose en la otra mitad de la pantalla dividida)
              └── teleport.html (cargado desde tu carpeta Descargas)
                   └── fetch() envía HTTP POST a 127.0.0.1:7765
                        └── El servidor ejecuta el JS y devuelve el resultado JSON
```

Cada vez que pulsas **Run** o **Teleport** en la página HTML:

1. Chrome construye un HTTP POST con tu contraseña como cabecera y tu JavaScript como cuerpo.
2. La petición va a `127.0.0.1:7765` — la dirección de bucle local de tu propio teléfono. Nunca sale del dispositivo.
3. El servidor integrado de Survivalcraft la recibe, verifica la contraseña y pasa el JS al motor Jint.
4. Jint ejecuta el código dentro del proceso del juego con acceso a los objetos C# (`findSubsystem`, `ComponentBody`, etc.).
5. El servidor devuelve una respuesta JSON que Chrome muestra en el cuadro de salida.

El indicador `--disable-web-security` existe solo porque Chrome normalmente se niega a que una página `file://` o `content://` llame a `http://127.0.0.1`. Quitar el indicador reactiva ese bloqueo y la herramienta deja de funcionar hasta que lo vuelvas a aplicar.

Limitaciones conocidas

Son límites de la API del juego, no de la herramienta:

· Solo scripts de una ejecución. En esta compilación los manejadores de fotogramas se registran pero nunca se disparan, así que nada se ejecuta automáticamente en segundo plano. Cada acción requiere que pulses Run.
· Algunas lecturas de propiedades bloquean el juego. Leer ciertas propiedades (salud, hora, cielo, clima, algunos componentes) puede congelar el motor de scripts. Cuando ocurre, debes forzar la detención del juego para recuperarte.
· Sin cola ni tiempo de espera. Si un script se cuelga, el servidor dice "There is already a script running" hasta que reinicies el juego.
· Sin renderizado. No puedes dibujar HUDs, superposiciones ni minimapas desde JavaScript.
· Sin atajos de teclado personalizados. Por la misma razón: los manejadores no se disparan.

Lo que funciona de forma fiable

· Leer posición, velocidad, nombre, nivel, salud y punto de aparición del jugador
· Escribir posición (teletransporte) y velocidad (lanzar, detener)
· Escribir el punto de aparición
· Enumerar subsistemas, entidades y objetos de la API

---

Solución de problemas

"Invalid password"
El nombre de la cabecera distingue mayúsculas de minúsculas. Prueba password, Password o PASSWORD. También confirma que la contraseña mostrada en el menú del juego no ha cambiado.

"There is already a script running"
Un script anterior bloqueó el motor. Fuerza la detención del juego, vuelve a abrirlo, carga el mundo y activa de nuevo el Control Remoto. Nada más lo resuelve: ni refrescar la página ni reiniciar Chrome.

"FETCH FAILED" en el cuadro de salida
Normalmente significa que el juego no está en pantalla dividida y ha sido suspendido. Vuelve a ponerlo en pantalla dividida con Chrome e inténtalo de nuevo.

El teletransporte funciona pero leer propiedades se cuelga
Algunas propiedades no son seguras de leer. Limítate a Position, Velocity, Name, Level y Health. Si alguna de ellas se cuelga, quítala de tus scripts.

El juego se congela por completo
Forzar detención desde Ajustes → Aplicaciones → Survivalcraft → Forzar detención.

---

Compartir

Esta herramienta no puede alojarse en GitHub Pages (ni en ningún sitio HTTPS). Los navegadores bloquean que las páginas HTTPS llamen a http://127.0.0.1, y CORS lo bloquea incluso si eso no fuera un problema.

El uso previsto es:

1. Descargar el archivo HTML.
2. Abrirlo localmente en un Chrome con la seguridad web desactivada.
3. Usarlo contra un juego en ejecución en el mismo dispositivo. (tiene que ser en pantalla dividida)

Por eso se distribuye como un solo archivo en lugar de una página alojada.

---
