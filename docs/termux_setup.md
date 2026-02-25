# Preparación inicial de Termux

Actualización base del entorno Termux antes de instalar proot.

## Actualizar paquetes

```pkg update
pkg upgrade```

## Repositorios adicionales

```pkg install x11-repo
pkg install tur-repo```

## Paquetes necesarios

```pkg install termux-x11-nightly
pkg install pulseaudio
pkg install proot-distro
pkg install wget
pkg install git```

## Notas

Instalación limpia para documentar el proceso completo desde cero.
