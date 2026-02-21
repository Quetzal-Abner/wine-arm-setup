# Debian Proot - Instalación y configuración

Este documento explica cómo preparar Debian en Termux usando proot, para luego instalar Wine en ARM.

## Paquetes iniciales en Termux

pkg update
pkg upgrade
pkg install x11-repo
pkg install termux-x11-nightly
pkg install tur-repo
pkg install pulseaudio
pkg install proot-distro
pkg install wget
pkg install git

## Instalar Debian

proot-distro install debian
proot-distro login debian

## Próximos pasos dentro de Debian

- Actualizar paquetes: apt update && apt upgrade
- Instalar dependencias para Wine
- Guardar errores en logs/errores-y-soluciones.md
