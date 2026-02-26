## Variables X11 en Debian

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

### Prueba gráfica

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
