# Entorno gráfico y audio — Termux + Debian Proot

Preparación del entorno gráfico, audio y red antes de instalar Wine ARM.

---

## Sistema objetivo

- Android
- Termux (HOST)
- Debian dentro de proot

---

## Documentación

Guías paso a paso para preparar el entorno:

1. [Preparación del Host (Termux)](docs/01-termux-host.md)
2. [Configuración gráfica X11](docs/02-x11-graphics.md)
3. [Configuración de audio (Puente PulseAudio)](docs/03-audio-pulseaudio.md)
4. [Configuración MIDI (FluidSynth)](docs/04-midi-fluidsynth.md)
5. [Red (HTTPS y DNS)](docs/05-network-https-dns.md)
6. [Box86 y Box64](docs/06-box86-box64.md)

Toda la documentación se encuentra dentro del directorio [`docs/`](docs/).

---

## Documentación del proyecto

Información técnica adicional:

- [Arquitectura](docs/architecture.md)
- [Solución de problemas (Troubleshooting)](docs/troubleshooting.md)
- [Referencias](docs/references.md)
- [Historial de cambios (Changelog)](CHANGELOG.md)