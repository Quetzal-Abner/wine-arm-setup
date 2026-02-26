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

Cambiar usuario:

```bash
su - quetzal
```
