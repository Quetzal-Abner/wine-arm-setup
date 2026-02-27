# Arquitectura del sistema

Este proyecto utiliza múltiples capas para ejecutar aplicaciones Linux
y Wine ARM dentro de Android usando Termux.

## Capas del sistema

Android

└── Termux (HOST)

    ├── PulseAudio (servidor de audio)
    ├── Termux:X11 (servidor gráfico)
    └── proot-distro

        └── Debian (GUEST)

            ├── Aplicaciones X11
            ├── ALSA → PulseAudio bridge
            ├── FluidSynth (MIDI)
            └── Wine ARM


## Audio

Debian utiliza ALSA.

El audio se redirige mediante TCP hacia PulseAudio ejecutándose
en Termux.

Flujo:

Debian ALSA → PulseAudio TCP → Termux → Android Audio.


## Gráficos

Las aplicaciones X11 dentro de Debian utilizan el socket compartido
en `/tmp`.

Flujo:

Debian X11 → socket compartido `/tmp/.X11-unix`
→ Termux:X11 → Superficie Android.