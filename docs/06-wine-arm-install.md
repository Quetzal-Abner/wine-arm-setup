# Wine ARM — Preparación de instalación

Este documento prepara Debian dentro de proot para instalar Wine ARM.

Actualmente no instala Wine.

---

## Actualizar sistema

sudo apt update
sudo apt upgrade

---

## Herramientas necesarias

sudo apt install build-essential git wget curl

---

## Multiarch

sudo dpkg --add-architecture armhf

sudo apt update

---

## Verificación

dpkg --print-foreign-architectures
