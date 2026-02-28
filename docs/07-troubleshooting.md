# Troubleshooting

Este documento recopila problemas comunes durante la configuración del entorno
Termux + Debian proot utilizado por este proyecto.

Actualmente cubre:

- Servidor gráfico X11
- Audio PulseAudio
- MIDI con FluidSynth
- Red dentro de proot

---

## X11 no abre aplicaciones gráficas

### Síntomas

- Las aplicaciones no muestran ventanas.
- Error relacionado con DISPLAY.

### Verificación

Comprobar DISPLAY:

echo $DISPLAY

Debe mostrar:

:0

### Solución

1. Iniciar Termux:X11.
2. Exportar variables:

export DISPLAY=:0
export MESA_NO_MITSHM=1
export GDK_DISABLE_MIT_SHM=1
export X11_NO_MITSHM=1

Probar conexión:

xeyes

Si funciona, X11 está correctamente configurado.

---

## No hay sonido

### Síntomas

- Archivos WAV reproducen sin error.
- No se escucha audio.

### Verificación

Comprobar PulseAudio:

pactl info

Si falla, PulseAudio no está activo.

### Solución

Iniciar PulseAudio:

pulseaudio --start

Exportar servidor:

export PULSE_SERVER=tcp:127.0.0.1

Probar:

aplay -D pulse archivo.wav

---

## speaker-test funciona pero no se escucha sonido

### Posible causa

PulseAudio no está conectado correctamente al cliente ALSA.

### Solución

Forzar salida PulseAudio:

aplay -D pulse archivo.wav

También verificar:

echo $PULSE_SERVER

---

## Advertencias ALSA en FluidSynth

### Síntomas

Mensajes similares a:

open /dev/snd/seq failed

### Explicación

En entornos proot no existe acceso directo al hardware ALSA.
Esto es normal y no impide la reproducción usando PulseAudio.

### Solución

Usar driver PulseAudio:

fluidsynth -a pulseaudio soundfont.sf2 archivo.mid

---

## Archivos MIDI no producen sonido

### Verificación

Probar reproducción directa:

fluidsynth -i -a pulseaudio soundfont.sf2 archivo.mid

Si funciona, el archivo MIDI es válido.

### Posible causa

Archivo MIDI mal generado.

Si fue creado usando CSV:

csvmidi archivo.csv archivo.mid

Verificar eventos:

- Header
- Start_track
- Note_on_c
- Note_off_c
- End_track

---

## FluidSynth no abre consola interactiva

### Síntomas

Mensajes ALSA al iniciar:

Error opening ALSA sequencer

### Explicación

El secuenciador ALSA no existe dentro de proot.

FluidSynth continuará funcionando usando PulseAudio.

No requiere solución.

---

## Archivos WAV renderizados no tienen sonido

### Síntomas

FluidSynth renderiza correctamente pero no se escucha.

### Verificación

Revisar tamaño:

ls -lh archivo.wav

Si pesa pocos bytes, el MIDI probablemente no contiene notas válidas.

Probar con un MIDI simple.

---

## No hay conexión a Internet dentro de Debian

### Verificación

ping google.com

### Solución

Revisar DNS:

cat /etc/resolv.conf

Si está vacío:

echo "nameserver 8.8.8.8" > /etc/resolv.conf

---

## Verificación rápida del entorno

Gráficos:

xeyes

Audio:

aplay -D pulse archivo.wav

MIDI:

fluidsynth -i -a pulseaudio soundfont.sf2 archivo.mid

Red:

ping google.com

Si todo funciona, el entorno está correctamente configurado.
