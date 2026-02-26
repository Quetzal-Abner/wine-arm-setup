## MIDI

Importante para:

* DOSBox
* Juegos antiguos de Windows
* Wine

Android **no expone dispositivos ALSA dentro de Proot**, así que se debe usar **PulseAudio**.

---

### Instalar sintetizador MIDI

```bash
sudo apt update
sudo apt install fluidsynth fluid-soundfont-gm midicsv
```

* `fluidsynth` → sintetizador MIDI
* `fluid-soundfont-gm` → SoundFont General MIDI
* `midicsv` → para crear archivos MIDI de prueba desde CSV

---

### Iniciar FluidSynth

Para iniciar el sintetizador **en segundo plano** y conectado a PulseAudio:

```bash
nohup fluidsynth -ni -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 > fluidsynth.log 2>&1 &
```

Verificar que está corriendo:

```bash
ps aux | grep fluidsynth
```

> Nota: El mensaje
> `open /dev/snd/seq failed`
> es normal en Proot y no afecta la reproducción MIDI.

---

### Crear archivo MIDI de prueba

En vez de descargarlo (los enlaces suelen fallar), podemos crear un MIDI **válido** usando CSV:

```bash
echo "0, 0, Header, 1, 1, 480
1, 0, Start_track
1, 0, Note_on_c, 0, 60, 90
1, 480, Note_off_c, 0, 60, 0
1, 480, End_track
0, 0, End_of_file" > ~/Downloads/prueba.csv

csvmidi ~/Downloads/prueba.csv ~/Downloads/prueba.mid
```

Verificar archivo:

```bash
file ~/Downloads/prueba.mid
```

Resultado esperado:

```
Standard MIDI data (format 1) using 1 track at 1/480
```

---

### Probar MIDI (renderizado a WAV)

Como no se puede reproducir directamente en consola sin GUI, se puede **renderizar a WAV** y luego reproducirlo con PulseAudio:

```bash
fluidsynth -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 \
-F ~/Downloads/prueba.wav ~/Downloads/prueba.mid

aplay -D pulse ~/Downloads/prueba.wav
```

---

### Reproducción directa de MIDI

Una vez que FluidSynth está configurado con PulseAudio, puedes reproducir archivos MIDI directamente, sin convertirlos a WAV:

```bash
fluidsynth -i -a pulseaudio /usr/share/sounds/sf2/FluidR3_GM.sf2 prueba.mid
````

Notas:

* El mensaje `Failed to create the MIDI thread; no MIDI input` es normal en Termux/Proot, ya que no hay dispositivos MIDI físicos accesibles.
* Esto **no afecta la reproducción**, el sonido debe escucharse correctamente a través de PulseAudio.
