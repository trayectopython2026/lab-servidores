# Servidores web desde cero con Ubuntu Server, SSH y Apache

**CFP20 – AMDELCO · Martes 17 de noviembre · 19:00 a 21:00**

Después de instalar Ubuntu en el mini PC, vas a manejarlo desde Windows por SSH. Al final de la charla, una notebook o un celular va a abrir la web que tenías en Windows, alojada en el mini PC. Si la conexión acompaña, también vas a poder mostrar esa misma página desde Internet.


## 1. Prepará el equipo antes de la charla

- Mini PC con fuente, teclado, pantalla o proyector y cable de video. Usá el teclado del mini PC para la instalación y el primer inicio. Después, proyectá la notebook y trabajá desde PowerShell por SSH.
- Cable Ethernet. El recorrido principal usa **red cableada con DHCP**, para que el router le entregue una IP automáticamente al servidor. La notebook puede estar por Wi‑Fi si ambos equipos se pueden comunicar.
- Notebook con Windows para usar PowerShell y el navegador, un pendrive de 8 GB o más para el instalador y un celular para comprobar la página. Probá `ssh -V` en PowerShell antes de salir. Si Windows no reconoce el comando, instalá **Cliente OpenSSH** desde **Características opcionales**.
- Un SSD que puedas destinar a la demo. **La opción de usar el disco entero borra las particiones y los datos del disco seleccionado.** Si el mini PC tiene Windows o archivos que querés conservar, usá un SSD separado y, si podés, desconectá el disco original antes de instalar.
- Guardá este README en la notebook. Antes de la charla, probá el menú de arranque, la salida de video y la comunicación entre el mini PC y la notebook. Si ensayás Apache, guardá también dos capturas: la bienvenida de Apache y la página del CFP20 abierta desde otro equipo.
- Si tenés VirtualBox a mano, podés dejar una VM de respaldo con Ubuntu Server, Apache y una copia de tu sitio ya listos. Probá de antemano cómo la vas a mostrar. Con la consola de la VM alcanza para demostrar la respuesta HTTP si el equipo físico falla.

### Creá el USB desde Windows con Rufus

1. Descargá **Ubuntu Server 24.04 LTS** desde las [descargas alternativas de Ubuntu](https://ubuntu.com/download/alternative-downloads). Buscá la edición para **Intel o AMD de 64 bits (amd64)**. La guía presupone que el mini PC usa esa arquitectura.
2. Bajá [Rufus](https://rufus.ie/) y abrilo en Windows.
3. Conectá el pendrive. En **Dispositivo**, verificá su nombre y capacidad. Rufus va a borrar todo lo que tenga.
4. En **Selección de arranque**, pulsá **Seleccionar** y elegí la ISO de Ubuntu Server. Para el primer intento, dejá el resto de los valores que propone Rufus.
5. Pulsá **Empezar**. Si Rufus pide archivos adicionales para arrancar, aceptá la descarga. Si ofrece elegir entre modo ISO y DD, elegí **modo ISO**.
6. Confirmá el borrado del pendrive y esperá hasta que Rufus indique **Listo**. Cerrá Rufus y expulsá el USB.

## 2. Arrancá el mini PC desde el USB

1. Con el mini PC apagado, conectá el pendrive, el teclado, la pantalla y el cable Ethernet.
2. Encendelo y pulsá varias veces la tecla del **Boot Menu**. Según la marca, puede ser `F12`, `F11`, `Esc` u otra. Probala antes de la charla. Si entrás al menú de configuración del equipo, buscá **Boot Menu** o **Boot Override**.
3. Elegí el pendrive. Si aparece una entrada como **UEFI: nombre del USB**, usá esa entrada.
4. En el menú de Ubuntu, seleccioná **Try or Install Ubuntu Server**. Esperá a que aparezca el asistente.
5. Si el pendrive no aparece, probá otro puerto USB con el equipo apagado y revisá si el firmware permite el arranque por USB. Si sigue sin aparecer, recrealo en Rufus o usá el respaldo.

En el instalador, movete con las flechas o `Tab`, marcá casillas con la barra espaciadora y confirmá con `Enter`. Los nombres de las pantallas pueden variar un poco según el idioma y la versión del instalador.

## 3. Instalá Ubuntu Server, pantalla por pantalla

| Pantalla | Qué elegir |
| --- | --- |
| **Idioma** | Elegí español o inglés. Los nombres en inglés de esta guía sirven como referencia. |
| **Actualización del instalador** | Si te ofrece actualizar el instalador, elegí continuar con la versión incluida en el USB para la demo. |
| **Keyboard configuration** | Elegí la distribución de tu teclado. Si el asistente permite probarlo, comprobá que coincidan las teclas. |
| **Choose type of install** | Elegí **Ubuntu Server** con las opciones predeterminadas. |
| **Network connections** | Revisá que la interfaz Ethernet tenga una IPv4 asignada por DHCP. Si no aparece, revisá el cable y el puerto del router. Podés continuar sin red si el instalador lo ofrece, pero después vas a necesitar una conexión para descargar Apache. |
| **Configure proxy** | Dejá el campo vacío si la institución no te dio un proxy. |
| **Ubuntu archive mirror** | Dejá el servidor de paquetes que propone el instalador. Si falla la comprobación y seguís sin red, resolvé la conexión antes de instalar Apache. |
| **Guided storage configuration** | Elegí **Use an entire disk**. Seleccioná **el SSD de la demo** por modelo y capacidad. Si aparece la opción de configurar LVM, podés desmarcarla para que el esquema de disco sea más fácil de mostrar. |
| **Storage configuration** | Revisá el resumen y comprobá que el disco seleccionado sea el que podés borrar. Elegí **Done** y después **Continue** para confirmar. Este paso aplica el borrado. |
| **Profile setup** | Nombre visible: `CFP20`. Nombre del equipo: `servidor-cfp20`. Usuario: `docente`. Usá una contraseña propia y tenela anotada para la demo. |
| **Ubuntu Pro**, si aparece | Elegí omitirlo. |
| **SSH Setup** | Marcá **Install OpenSSH server**. Para la demo en la red local, dejá habilitada **Allow password authentication over SSH** si aparece. Entrarás con el usuario y la contraseña que creaste en **Profile setup**. Elegí **Done**. |
| **Featured Server Snaps**, si aparece | Dejá la lista sin seleccionar y elegí **Done**. Apache se instala después desde la terminal. |
| **Installation complete** | Cuando termine, elegí **Reboot Now**. Cuando la pantalla lo pida, retirale el pendrive y presioná `Enter`. Si vuelve a salir el instalador, arrancá desde el SSD. |

Mientras se instala, mostrá el equipo y preguntá al grupo dónde va a quedar el HTML una vez que alguien lo abra desde un celular. También podés usar ese tiempo para revisar la carpeta de tu sitio en la notebook o hacer una pausa.

## 4. Iniciá sesión y verificá la red

Cuando veas `login:`, escribí `docente` o el usuario que elegiste. Después ingresá la contraseña. En Linux, al ingresar la contraseña, normalmente **no se ven puntos ni asteriscos**.

Ejecutá esto en la consola del mini PC:

```bash
hostname
ip -br -4 addr
ip route
```

- `hostname` muestra el nombre del equipo.
- `ip -br -4 addr` lista las direcciones IPv4. Buscá la de la interfaz conectada, por ejemplo `192.168.1.45/24`. Para abrir la página usás `192.168.1.45`, sin `/24`. `127.0.0.1` solo sirve dentro del propio mini PC.
- `ip route` normalmente muestra una línea que empieza por `default via` si el equipo recibió una puerta de enlace para salir a Internet.

Anotá la IP real del mini PC. La vas a usar en la notebook y en el celular.

## 5. Conectate al mini PC por SSH desde Windows

En la **consola física de Ubuntu**, confirmá que hay un proceso escuchando en el puerto 22. El paquete puede usar `ssh.socket` para recibir conexiones.

```bash
sudo ss -ltnp 'sport = :22'
```

Si no aparece una fila `LISTEN` y omitiste OpenSSH durante la instalación, instalalo desde esa consola:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh.socket
sudo ss -ltnp 'sport = :22'
```

Si OpenSSH ya está instalado y falta la escucha, ejecutá `sudo systemctl enable --now ssh.socket` y repetí la comprobación.

En la **notebook con Windows**, abrí PowerShell y conectate. Cambiá `docente` por el usuario que creaste y `192.168.1.45` por la IP que anotaste:

```powershell
ssh docente@192.168.1.45
```

1. En la primera conexión, SSH te muestra la huella digital del servidor. Si te muestra **ED25519**, podés comparar la huella con el resultado de `ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub` en la consola del mini PC. Si coincide, escribí `yes`.
2. Escribí la contraseña de Ubuntu. Aunque el cursor no se mueva, se está ingresando.
3. Ejecutá `hostname` y `whoami`. Si muestran `servidor-cfp20` y `docente` (o los valores elegidos), ya estás ejecutando comandos en el mini PC desde Windows. Podés pegar los bloques de Apache directamente en esa sesión.

Mantené SSH en la red local de la demo. El túnel del final apunta al puerto de Apache (80). Al salir de la sesión con `exit`, Apache sigue funcionando como servicio.

## 6. Evitá que Ubuntu entre en suspensión o hibernación

Desde la sesión SSH, creá una configuración para que el mini PC siga atendiendo peticiones durante la charla aunque nadie use su teclado o se apague la pantalla:

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

## si no funciona

```bash
sudo mkdir -p /etc/systemd/sleep.conf.d
sudo tee /etc/systemd/sleep.conf.d/90-cfp20-awake.conf >/dev/null <<'EOF'
[Sleep]
AllowSuspend=no
AllowHibernation=no
AllowHybridSleep=no
AllowSuspendThenHibernate=no
EOF
systemd-analyze cat-config systemd/sleep.conf
```

En la salida buscá `90-cfp20-awake.conf` y las cuatroes opcion con `no`. En el ensayo, apagá solo el monitor del mini PC y comprobá desde la notebook que podés abrir una segunda sesión SSH y que la web sigue respondiendo. La configuración queda guardada para el próximo arranque. Si necesitás revertirla después de la charla, quitá el archivo con `sudo rm /etc/systemd/sleep.conf.d/90-cfp20-awake.conf`.

Para la exposición, dejá el mini PC enchufado. La configuración evita la suspensión y la hibernación del sistema. Si el equipo pierde alimentación, se apaga.

## 7. Instalá Apache y abrí la página de bienvenida

En la sesión SSH abierta en PowerShell, ejecutá estos comandos de a uno:

```bash
sudo apt update
sudo apt install apache2 curl nano
systemctl is-active apache2
curl -I http://127.0.0.1/
```

- `apt update` actualiza el índice de paquetes. `apt install` instala Apache y las herramientas que vas a usar para probarlo y editar el HTML. Confirmá la instalación si se solicita.
- `systemctl is-active apache2` debería mostrar `active`.
- `curl -I` pide las cabeceras de la página. Esperá una primera línea similar a `HTTP/1.1 200 OK`.

Ahora, en **la notebook**, abrí la IP del mini PC desde el navegador. Escribí `http://` completo. Si la IP fuera la del ejemplo:

```text
http://192.168.1.45/
```

**Resultado esperado:** aparece la página de bienvenida de Apache. Ya hiciste una solicitud HTTP desde un equipo a otro.

## 8. Copiá tu web desde Windows y publicala

La demo usa la web que ya tenés armada. Estos pasos sirven si el sitio se puede publicar como HTML, CSS, JavaScript e imágenes. Si también necesita un servidor de Flask, Laravel, Node u otro backend, ese servicio requiere su propia configuración.

### 8.1 Prepará la carpeta en Windows

1. Creá una carpeta llamada `mi-web`, por ejemplo en Documentos.
2. Si la web es HTML, CSS y JavaScript, copiá a `mi-web` el `index.html` y las carpetas de estilos, scripts e imágenes. Conservá la estructura.
3. Si la web está hecha con **React y Vite**, ejecutá `npm run build` desde la carpeta del proyecto en Windows. Copiá a `mi-web` el **contenido** de `dist`, incluido el `index.html` y la carpeta `assets`.
4. Abrí `mi-web` y verificá que `index.html` esté en el primer nivel. Incluí solo los archivos que vas a servir. Dejá afuera `.env`, `.git`, credenciales y `node_modules`.

### 8.2 Enviá la carpeta al mini PC con SCP

Dejá abierta la sesión SSH. En **otra pestaña de PowerShell en Windows**, ubicáte en la carpeta que contiene `mi-web`. El ejemplo usa Documentos. Si la guardaste en otro lugar, cambiá esa ruta.

```powershell
Set-Location (Join-Path ([Environment]::GetFolderPath('MyDocuments')) 'mi-web') -ErrorAction Stop
Get-Item .\index.html -ErrorAction Stop
Set-Location ..
scp -r .\mi-web docente@192.168.1.45:~/
```

Usá tu usuario y la IP real del mini PC. SCP te va a pedir la misma contraseña de Ubuntu si te conectaste por contraseña. Esperá a que termine. La carpeta queda en `~/mi-web` del servidor. `-r` copia también las subcarpetas.

### 8.3 Instalá la web en Apache desde SSH

Volvé a **la pestaña de PowerShell donde ya estás conectado por SSH** y ejecutá:

```bash
ls -l ~/mi-web/index.html
```

Si la lista muestra tu archivo, ejecutá:

```bash
sudo cp -R ~/mi-web/. /var/www/html/
curl -I http://127.0.0.1/
```

Apache sirve `/var/www/html/` con la configuración predeterminada. En el mini PC recién instalado, tu `index.html` reemplaza la página de bienvenida. La copia conserva las carpetas de recursos y deja la web disponible en la raíz, así que abre en `http://192.168.1.45/` con la IP real. Apache sirve los archivos nuevos sin reiniciar.

### 8.4 Probá el resultado desde otro equipo

- Abrí la URL en la notebook. Comprobá que carguen el contenido, los estilos y las imágenes. Si seguís viendo la bienvenida de Apache, recargá con `Ctrl+F5`.
- Pedile a un voluntario que abra esa misma dirección desde un celular conectado a la red local.
- Si querés mostrar una actualización, cambiá un texto en Windows y repetí la transferencia y la copia a Apache. Si el proyecto usa Vite, volvé a generar `dist` antes de copiar los archivos.

## 9. Opcional: abrí la página desde Internet

La página sigue alojada en el mini PC. Cloudflare crea una dirección pública temporal que le pasa las solicitudes a Apache. Para esta prueba usá únicamente una página de muestra sin datos personales.

1. En el ensayo, descargá el paquete **.deb para Linux amd64** desde la [documentación de descargas de Cloudflare](https://developers.cloudflare.com/tunnel/downloads/). Si vas a instalar Ubuntu desde cero en vivo, tené presente que también vas a tener que instalar `cloudflared` en ese Ubuntu. Podés descargar el paquete en el momento o copiarlo desde otro pendrive.
2. Desde el mini PC con Internet, podés descargar e instalar ese mismo paquete con estos comandos:

```bash
cd ~
curl -fL https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared-linux-amd64.deb
sudo apt install ./cloudflared-linux-amd64.deb
cloudflared --version
```

3. Iniciá el túnel después de verificar que Apache responde localmente:

```bash
cloudflared tunnel --url http://127.0.0.1:80
```

4. Dejá esa terminal abierta. Copiá la dirección `https://…trycloudflare.com` que imprima el comando.
5. En el celular, apagá el Wi‑Fi y abrí esa dirección usando datos móviles. Al terminar, volvé a la terminal SSH y presioná `Ctrl+C` para cerrar el túnel.

## 10. Si algo falla durante la charla

| Qué ves | Qué probás primero |
| --- | --- |
| El mini PC no tiene IPv4 en Ethernet. | Revisá el cable y el puerto del router. Volvé a ejecutar `ip -br -4 addr`. Si tenés otra red que ya probaste, pasá a esa. |
| SSH muestra `Connection refused` o se queda esperando. | Desde PowerShell probá `Test-NetConnection 192.168.1.45 -Port 22` con la IP real. En el mini PC verificá que el puerto 22 esté escuchando y que ambos equipos puedan comunicarse. Si UFW está activo y bloquea SSH, desde la consola del mini PC podés autorizar solo la IPv4 de tu notebook con `sudo ufw allow from 192.168.1.20 to any port 22 proto tcp`, reemplazando la IP de ejemplo. |
| SSH muestra `Permission denied`. | Revisá el usuario y probá la contraseña en la consola física. Confirmá que habilitaste la autenticación por contraseña en el instalador. Podés continuar con la consola del mini PC mientras resolvés el acceso. |
| SSH avisa que la huella cambió después de reinstalar. | Confirmá en el mini PC que la IP es la correcta y anotá la nueva huella. Si confirmaste que es el equipo que reinstalaste, desde PowerShell eliminá la entrada anterior con `ssh-keygen -R 192.168.1.45` usando la IP real. Reconectate y compará la huella nueva antes de aceptarla. |
| `apt update` muestra errores de conexión o de DNS. | Revisá la IP y si hay una ruta `default via` con `ip route`. Si la red del lugar sigue sin salir a Internet, pasá al equipo o la VM con Apache preinstalado. |
| Apache no responde en el propio mini PC. | Probá `sudo systemctl start apache2` y después `systemctl status apache2 --no-pager`. Si el servicio arranca, repetí `curl -I http://127.0.0.1/`. |
| En el mini PC hay respuesta, pero desde la notebook no abre. | Confirmá la IP, que escribiste `http://` y que ambos equipos están en una red que permite comunicación entre ellos. Si usás UFW, revisá `sudo ufw status`. Si está activo y bloquea HTTP en la red de la demo, habilitá `sudo ufw allow 80/tcp`. |
| Seguís viendo la bienvenida de Apache. | Comprobá que `index.html` esté directamente en `~/mi-web/` y que se haya copiado a `/var/www/html/`. Recargá el navegador con `Ctrl+F5`. |
| La página abre sin estilos o imágenes. | Confirmá que copiaste todas las subcarpetas. Verificá que las rutas coincidan en mayúsculas y minúsculas. En Ubuntu, `Logo.png` y `logo.png` son nombres distintos. |
| El enlace público falla. | Mostrá el acceso por IP local y seguí con el cierre. |
| La instalación en vivo se demora y se acerca el bloque de Apache. | Pasá al Ubuntu con Apache preparado en otro equipo, SSD o VM. Podés mostrar desde su consola `curl -I http://127.0.0.1/` y `curl http://127.0.0.1/`. |

Si ese día tenés poca voz, dejá el guion proyectado mientras instalás, mostrá cómo el sitio aparece en el navegador después de copiarlo y pedile a un voluntario que abra la página y cuente qué ve. La prueba con el celular y las preguntas te permiten hacer pausas durante la exposición.

## Referencias oficiales

- [Instalación básica de Ubuntu Server](https://ubuntu.com/server/docs/basic-installation/)
- [Configuración de almacenamiento en el instalador](https://canonical-subiquity.readthedocs-hosted.com/en/latest/howto/configure-storage.html)
- [Crear un USB de arranque con Rufus en Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows)
- [OpenSSH Server en Ubuntu](https://ubuntu.com/server/docs/how-to/security/openssh-server/)
- [Manual de SCP](https://man.openbsd.org/scp)
- [Despliegue estático con Vite](https://es.vite.dev/guide/static-deploy)
- [Cliente OpenSSH en Windows](https://learn.microsoft.com/en-us/windows/terminal/tutorials/ssh)
- [Opciones de suspensión e hibernación de systemd](https://www.freedesktop.org/software/systemd/man/255/systemd-sleep.conf.html)
- [Instalar Apache en Ubuntu Server](https://ubuntu.com/server/docs/how-to/web-services/install-apache2/)
- [Configuración de Apache y directorio raíz del sitio](https://ubuntu.com/server/docs/how-to/web-services/configure-apache2-settings/)
- [Quick Tunnels de Cloudflare](https://developers.cloudflare.com/tunnel/get-started/quick-tunnels/)
