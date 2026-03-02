## Preparación en Termux (HOST)

Actualizar paquetes:

```bash
pkg update
pkg upgrade
```

Instalar repositorios adicionales:

```bash
pkg install x11-repo
pkg install tur-repo
```

Instalar dependencias necesarias:

```bash
pkg install termux-x11-nightly pulseaudio git wget curl
```

---

## Iniciar servidor gráfico X11

Abrir aplicación Termux:X11.

Iniciar servidor:

```bash
termux-x11 :0 &
```

Abrir interfaz Android:

```bash
am start --user 0 \
-n com.termux.x11/com.termux.x11.MainActivity
```

Exportar variable:

```bash
export DISPLAY=:0
```

Desactivar MIT-SHM (importante en Android):

```bash
export MESA_NO_MITSHM=1
```

Verificar:

```bash
echo $DISPLAY
```

Resultado esperado:

```
:0
```

---

## Iniciar PulseAudio (HOST)

Detener instancias anteriores:

```bash
pulseaudio --kill || true
```

Iniciar servidor:

```bash
pulseaudio --start \
--exit-idle-time=-1 \
--daemonize=yes
```

Habilitar conexión TCP (recomendado para proot):

```bash
pactl load-module module-native-protocol-tcp \
auth-ip-acl=127.0.0.1 \
auth-anonymous=1
```

Verificar:

```bash
pactl info
```

Resultado esperado:

```
Server Name: pulseaudio
```

---

## Entrar a Debian (LOGIN recomendado)

Evita problemas con sockets X11 y PulseAudio:

```bash
proot-distro login debian --shared-tmp
```

## Crear usuario NO ROOT (Primera vez solamente)

Después de entrar a Debian por primera vez, crear un usuario normal.

Verificar:

```bash
whoami
````

Debe devolver:

```
root
```

Crear usuario:

```bash
adduser quetzal
```

Seguir instrucciones:

* contraseña
* información opcional (puede omitirse).

---

## Permisos administrativos (sudo)

Instalar sudo:

```bash
apt update
apt install sudo
```

Abrir configuración sudo:

```bash
visudo
```

Agregar al final:

```
quetzal ALL=(ALL) ALL
```

Guardar y salir.

---

## Cambiar usuario

⚠️ No ejecutar pasos posteriores como root.

Usar:

```bash
su - quetzal
```

Verificar:

```bash
whoami
```

Debe devolver:

```
quetzal
```

Nota:

Este método asegura permisos administrativos incluso
en instalaciones Debian minimal o entornos proot.
