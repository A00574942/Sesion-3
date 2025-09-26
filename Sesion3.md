
[PROYECTO_MVP_TKINTER.md](https://github.com/user-attachments/files/22562370/PROYECTO_MVP_TKINTER.md)
# Proyecto Integrador – MVP (tkinter)

Este repositorio contiene un **MVP de escritorio en Python** usando `tkinter` y `ttk` con ventanas modulares para: bienvenida, formulario con validación y guardado a archivo, lista con CRUD básico, tabla con `Treeview` y un lienzo `Canvas` para dibujo simple.

> **Objetivo:** servir como base didáctica para cursos/entregables donde se requiere una app GUI mínima pero ordenada y extensible.

---

## Tabla de contenido
1. [Arquitectura](#arquitectura)
2. [Requisitos](#requisitos)
3. [Ejecución](#ejecución)
4. [Estructura de datos](#estructura-de-datos)
5. [Módulos y funciones](#módulos-y-funciones)
6. [Flujo de UI](#flujo-de-ui)
7. [Validaciones y manejo de errores](#validaciones-y-manejo-de-errores)
8. [Extensión y personalización](#extensión-y-personalización)
9. [Problemas comunes](#problemas-comunes)
10. [Checklist de prueba manual](#checklist-de-prueba-manual)

---

## Arquitectura

- **`main.py`**: Punto de entrada. Crea la ventana raíz y botones para abrir cada sub‑ventana (modular).
- **Ventanas modulares** (cada una en su propio archivo):
  - `win_home.py` → pantalla de bienvenida y prueba de `messagebox`.
  - `win_form.py` → formulario con validación y guardado a archivo de texto.
  - `win_list.py` → CRUD básico en `Listbox` (agregar/eliminar/limpiar).
  - `win_table.py` → tabla `Treeview` que lee un CSV externo.
  - `win_canvas.py` → ejemplo de figuras en `Canvas` (rectángulo, óvalo, línea, texto).

Cada módulo expone una función `open_<nombre>(parent: tk.Tk)` que abre un `Toplevel` (ventana hija) sobre la raíz.

---

## Requisitos

- Python 3.8+ (recomendado 3.10+).
- `tkinter` y `ttk` (incluidos con la instalación oficial de Python en la mayoría de plataformas).
- Para `win_table.py`: un archivo CSV de ejemplo en `data/sample.csv` con encabezados `nombre,valor1,valor2`.

> **Nota:** En Windows, si `tkinter` no está disponible, reinstala Python desde [python.org](https://www.python.org) marcando “tcl/tk and IDLE”.

---

## Ejecución

```bash
# Opción A: si los módulos están en el mismo directorio
python main.py

# Opción B: si usas un paquete "app/"
# Estructura esperada:
# app/
#   __init__.py
#   win_home.py
#   win_form.py
#   win_list.py
#   win_table.py
#   win_canvas.py
# main.py  (importa desde app.XXX)
# Ajusta PYTHONPATH o ejecuta desde la raíz del proyecto:
python main.py
```

### Importaciones
El `main.py` muestra importaciones del tipo `from app.win_home import open_win_home`.  
Si **NO** tienes la carpeta `app/` y los archivos están al lado de `main.py`, cambia las importaciones a:
```python
from win_home import open_win_home
from win_form import open_win_form
from win_list import open_win_list
from win_table import open_win_table
from win_canvas import open_win_canvas
```

---

## Estructura de datos

`win_table.py` asume un CSV en `data/sample.csv` **dos niveles arriba** del archivo (usando `Path(__file__).resolve().parents[2] / "data" / "sample.csv"`).  
Ejemplo de contenido:

```csv
nombre,valor1,valor2
Elemento A,10,20
Elemento B,5,8
Elemento C,7,3
```

Si el archivo **no existe**, la ventana mostrará una advertencia y no poblará la tabla.

---

## Módulos y funciones

### `main.py`
- **Responsabilidad:** Ventana raíz, navegación a sub‑ventanas.
- **Claves de diseño:**
  - `ttk.Frame` con `padding=16` para consistencia visual.
  - Botones 1–5 para abrir cada ventana mediante `command=lambda: open_win_xxx(root)`.
  - Botón **Salir** que invoca `root.destroy()`.
- **Entrada/Salida:** Sin I/O de disco; solo UI.

### `win_home.py`
- **`open_win_home(parent)`**: crea `Toplevel` 360×220 con:
  - Etiquetas de bienvenida.
  - Botón **Mostrar mensaje** → `messagebox.showinfo("Info", "¡Equipo listo!")`.
  - Botón **Cerrar** → `win.destroy()`.

### `win_form.py`
- **`open_win_form(parent)`**: ventana 420×260 con:
  - Campos `Nombre` (`Entry`) y `Edad` (`Entry`).
  - `validar_y_guardar()`:
    - Valida `nombre` no vacío.
    - Valida `edad` numérica (`str.isdigit()` ⇒ enteros positivos).
    - Abre diálogo `filedialog.asksaveasfilename` y guarda un `.txt` con los datos.
    - Usa `messagebox` para errores/confirmaciones.
- **Notas:** Si requieres edades negativas o con decimales, cambia la validación.

### `win_list.py`
- **`open_win_list(parent)`**: ventana 420×300 con:
  - `Listbox` con `rowconfigure/columnconfigure` para expansión.
  - `Entry` para capturar ítems.
  - Acciones:
    - **Agregar:** Inserta texto no vacío; avisa si está vacío.
    - **Eliminar seleccionado:** Borra ítem en índice seleccionado.
    - **Limpiar:** Borra toda la lista.
- **Patrones usados:** separación de UI (definición de widgets) y lógica (funciones internas).

### `win_table.py`
- **`open_win_table(parent)`**: ventana 480×300 con:
  - `Treeview` con columnas `("nombre", "valor1", "valor2")` y encabezados capitalizados.
  - Carga CSV mediante `csv.DictReader` y `Path` (robusto a rutas relativas).
  - Si no existe el CSV, muestra `messagebox.showwarning` y retorna (no rompe la app).
- **Extensión:** soporta ordenar columnas, edición in‑place y filtrado con pocas líneas extra.

### `win_canvas.py`
- **`open_win_canvas(parent)`**: ventana 480×340 con `Canvas` 440×240 en fondo blanco.
- Dibuja ejemplos:
  - `create_rectangle`, `create_oval`, `create_line`, `create_text`.
- **Uso didáctico:** punto de partida para graficar, animar o capturar clics.

---

## Flujo de UI

1. El usuario ejecuta `main.py` → aparece la ventana raíz con el menú de opciones.
2. Selecciona una opción (1–5) → se abre una ventana hija (`Toplevel`).
3. Cada ventana es **independiente** y puede cerrarse sin afectar la raíz.
4. Se puede abrir más de una sub‑ventana a la vez (útil para comparar).

---

## Validaciones y manejo de errores

- **Formulario**: valida campos y restringe edad a entero positivo (por `isdigit()`).
- **Tabla**: si el CSV no existe, **avisa** y no intenta leer (evita excepciones).
- **Lista**: alerta con `messagebox.showwarning` cuando se intenta agregar un ítem vacío.
- **General**: las acciones destructivas son explícitas (eliminar/limpiar).

---

## Extensión y personalización

- **Tema/estilos:** aplica un `ttk.Style()` global y cambia fuentes/espaciados.
- **Internacionalización:** extrae textos a constantes o a un archivo JSON.
- **Datos persistentes:** cambia `win_list.py` para leer/escribir JSON en disco.
- **Tabla avanzada:** agrega ordenamiento por columna, búsqueda y paginación.
- **Canvas interactivo:** captura eventos de ratón (`<Button-1>`, `<B1-Motion>`) para dibujar.
- **Estructura de paquetes:** mueve los *wins* a un paquete `app/` con `__init__.py`.

Ejemplo de ordenamiento por columna en `Treeview` (idea general):
```python
def sort_by(tv, col, reverse=False):
    items = [(tv.set(k, col), k) for k in tv.get_children("")]
    items.sort(reverse=reverse, key=lambda x: x[0])
    for index, (_, k) in enumerate(items):
        tv.move(k, "", index)
    tv.heading(col, command=lambda: sort_by(tv, col, not reverse))
# Luego, al crear headings:
for c in cols:
    tv.heading(c, text=c.capitalize(), command=lambda c=c: sort_by(tv, c))
```

---

## Problemas comunes

- **ImportError desde `main.py`:**  
  Si ves `ModuleNotFoundError: No module named 'app'`, revisa:
  - Que exista la carpeta `app/` con los archivos `win_*.py`.
  - O cambia las importaciones como se muestra en [Ejecución](#ejecución).

- **CSV no encontrado en `win_table.py`:**
  - Crea `data/sample.csv` en la ruta esperada (dos niveles arriba del módulo).
  - O ajusta la línea que construye la ruta para tu estructura real de carpetas.

- **`tkinter` no disponible:**
  - Reinstala Python asegurando la opción de `tcl/tk`.

---

## Checklist de prueba manual

- [ ] Abrir cada opción (1–5) desde la ventana principal.
- [ ] `Home`: ver mensaje “¡Equipo listo!”.
- [ ] `Formulario`: ingresar nombre/edad válidos, guardar `.txt` y verificar contenido.
- [ ] `Formulario`: probar validaciones (nombre vacío, edad no numérica).
- [ ] `Lista`: agregar varios ítems, eliminar seleccionado, limpiar lista.
- [ ] `Tabla`: con CSV válido, ver filas cargadas; sin CSV, ver advertencia.
- [ ] `Canvas`: verificar que se dibujan las figuras de ejemplo.

---

## Licencia

Uso educativo/didáctico. Adáptalo libremente a tu curso o proyecto.
