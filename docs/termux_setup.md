# Preparación inicial de Termux

Actualización base del entorno Termux antes de instalar proot.

## Actualizar paquetes

```bash
pkg update
pkg upgrade
```

## Repositorios adicionales

```bash
pkg install x11-repo
pkg install tur-repo
```

## Paquetes necesarios

```bash
pkg install termux-x11-nightly
pkg install pulseaudio
pkg install proot-distro
pkg install wget
pkg install git
```

## Notas

Instalación limpia para documentar el proceso completo desde cero.
