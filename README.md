# Wine ARM Setup

Este proyecto documenta cómo instalar y ejecutar Wine en ARM usando Termux y Debian con proot.

## Paquetes iniciales en Termux

```pkg update
pkg upgrade
pkg install x11-repo
pkg install termux-x11-nightly
pkg install tur-repo
pkg install pulseaudio
pkg install proot-distro
pkg install wget
pkg install git```

## Instalación de Debian con proot

```proot-distro install debian
proot-distro login debian```

Ahora estarás dentro de un entorno Debian completo en tu dispositivo ARM.

## Referencias

- https://github.com/LinuxDroidMaster/Termux-Desktops/blob/main/Documentation/proot/debian_proot.md
- https://ivonblog.com/en-us/posts/termux-proot-box86-box64/
