# Entorno gráfico y audio — Termux + Debian Proot

Preparación del entorno gráfico y audio antes de instalar Wine ARM.

Sistema objetivo:

* Android
* Termux (HOST)
* Debian dentro de proot

Este documento prepara:

* Servidor X11
* Audio PulseAudio
* OpenGL
* MIDI (FluidSynth)
* HTTPS y DNS

---

## 1 — Preparación en Termux (HOST)

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

## 2 — Iniciar PulseAudio (HOST)

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

## 3 — Iniciar servidor gráfico X11

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

## 4 — Entrar a Debian (LOGIN recomendado)

Evita problemas con sockets X11 y PulseAudio:

```bash
proot-distro login debian --shared-tmp
```

Cambiar usuario:

```bash
su - quetzal
```

---

## 5 — Variables X11 en Debian

Exportar:

```bash
export DISPLAY=:0

export MESA_NO_MITSHM=1
export GDK_DISABLE_MIT_SHM=1
export X11_NO_MITSHM=1
```

Verificar socket X11:

```bash
ls /tmp/.X11-unix
```

Debe existir:

```
X0
```

Si no existe:

Termux:X11 no está ejecutándose.

---

## 6 — Configuración audio Debian (MUY IMPORTANTE)

Android no expone hardware ALSA dentro de proot.

Debe utilizarse PulseAudio del HOST.

Actualizar paquetes:

```bash
sudo apt update
```

Instalar bridge ALSA → PulseAudio:

```bash
sudo apt install \
libasound2-plugins \
pulseaudio-utils
```

Crear configuración ALSA:

```bash
nano ~/.asoundrc
```

Contenido:

```
pcm.!default {
 type pulse
}

ctl.!default {
 type pulse
}
```

Exportar variables:

```bash
export PULSE_SERVER=tcp:127.0.0.1
export SDL_AUDIODRIVER=pulse
```

Verificar conexión:

```bash
pactl info
```

Resultado esperado:

```
Is Local: no
Server String: tcp:127.0.0.1
```

---

## 7 — Prueba gráfica

Instalar utilidades:

```bash
sudo apt install x11-apps mesa-utils
```

Ejecutar:

```bash
xeyes
```

Debe seguir el cursor.

Si los ojos aparecen enormes:

Problema DPI Android.

Solución:

```bash
export GDK_SCALE=1
```

Verificar OpenGL:

```bash
glxinfo | grep OpenGL
```

---

## 8 — Pruebas audio

Instalar:

```bash
sudo apt install alsa-utils sox
```

Mono:

```bash
speaker-test -c1 -t sine
```

Stereo:

```bash
speaker-test -c2 -t sine
```

Debe alternar:

```
Left
Right
```

Prueba WAV:

```bash
sox -n prueba.wav synth 2 sine 440

aplay prueba.wav
```

---

## 9 — MIDI

Importante para:

* DOSBox
* Juegos antiguos de Windows
* Wine

Android **no expone dispositivos ALSA dentro de Proot**, así que se debe usar **PulseAudio**.

---

### 9.1 — Instalar sintetizador MIDI

```bash
sudo apt update
sudo apt install fluidsynth fluid-soundfont-gm midicsv
```

* `fluidsynth` → sintetizador MIDI
* `fluid-soundfont-gm` → SoundFont General MIDI
* `midicsv` → para crear archivos MIDI de prueba desde CSV

---

### 9.2 — Iniciar FluidSynth

Para iniciar el sintetizador **en segundo plano** y conectado a PulseAudio:

```bash
nohup fluidsynth -ni -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 > fluidsynth.log 2>&1 &
```

Verificar que está corriendo:

```bash
ps aux | grep fluidsynth
```

> Nota: El mensaje
> `open /dev/snd/seq failed`
> es normal en Proot y no afecta la reproducción MIDI.

---

### 9.3 — Crear archivo MIDI de prueba

En vez de descargarlo (los enlaces suelen fallar), podemos crear un MIDI **válido** usando CSV:

```bash
echo "0, 0, Header, 1, 1, 480
1, 0, Start_track
1, 0, Note_on_c, 0, 60, 90
1, 480, Note_off_c, 0, 60, 0
1, 480, End_track
0, 0, End_of_file" > ~/Downloads/prueba.csv

csvmidi ~/Downloads/prueba.csv ~/Downloads/prueba.mid
```

Verificar archivo:

```bash
file ~/Downloads/prueba.mid
```

Resultado esperado:

```
Standard MIDI data (format 1) using 1 track at 1/480
```

---

### 9.4 — Probar MIDI (renderizado a WAV)

Como no se puede reproducir directamente en consola sin GUI, se puede **renderizar a WAV** y luego reproducirlo con PulseAudio:

```bash
fluidsynth -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 \
-F ~/Downloads/prueba.wav ~/Downloads/prueba.mid

aplay -D pulse ~/Downloads/prueba.wav
```

---

### 9.5 — Reproducción directa de MIDI

Una vez que FluidSynth está configurado con PulseAudio, puedes reproducir archivos MIDI directamente, sin convertirlos a WAV:

```bash
fluidsynth -i -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 prueba.mid
````

Notas:

* El mensaje `Failed to create the MIDI thread; no MIDI input` es normal en Termux/Proot, ya que no hay dispositivos MIDI físicos accesibles.
* Esto **no afecta la reproducción**, el sonido debe escucharse correctamente a través de PulseAudio.

---

## 10 — HTTPS y DNS

Certificados HTTPS:

```bash
sudo apt install ca-certificates
```

HTTPS:

```bash
wget https://google.com
```

DNS:

```bash
ping google.com
```

---

## References

- [Debian Proot on Termux – LinuxDroidMaster](https://github.com/LinuxDroidMaster/Termux-Desktops/blob/main/Documentation/proot/debian_proot.md)
- [Termux Proot, Box86 & Box64 – IvonBlog](https://ivonblog.com/en-us/posts/termux-proot-box86-box64/)