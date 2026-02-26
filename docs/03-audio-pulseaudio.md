## Configuración audio Debian (MUY IMPORTANTE)

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

### Pruebas audio

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