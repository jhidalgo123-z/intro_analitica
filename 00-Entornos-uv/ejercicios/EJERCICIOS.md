# Ejercicios: Módulo 00, Entornos con `uv`

## Cómo trabajamos en las salas de Zoom

- **Equipos de 3 personas.** Son los mismos equipos del proyecto final, así que conviene armarlos hoy.
- **Roles que rotan en cada módulo:**
  - **Piloto:** comparte pantalla y escribe.
  - **Copiloto:** lee las instrucciones, revisa errores y busca en la documentación.
  - **Relator:** toma notas y presenta en la plenaria.
- Al volver a la sala principal, cada equipo tiene **2 minutos** para contar qué logró y qué le costó.
- Si se bloquean más de 5 minutos, escriban en el chat de Zoom o usen "Pedir ayuda".

---

## Parte 1: Calentamiento individual (15 min, primera parte de la clase)

Cada persona, en su computador:

1. Instala `uv` y verifica con `uv --version`.
2. Clona el repositorio del curso y ejecuta `uv sync`.
3. Verifica la instalación con `uv run python -V` (debe mostrar Python 3.14) y con
   `uv run python -c "import pandas, sklearn; print(pandas.__version__, sklearn.__version__)"`.
4. Abre `notebooks/01_mi_primer_entorno.ipynb` con el kernel `.venv` y ejecútalo completo.

> **Listo cuando:** los dos comandos anteriores corren sin errores y el notebook se ejecuta completo.

---

## Parte 2: Salas de Zoom, el entorno del proyecto del equipo (45 min)

> **Objetivo:** crear el proyecto de Python **del equipo**. Será la base de todo lo que construyan en el semestre.

### Paso 1: El piloto crea el proyecto (10 min)

```bash
uv init proyecto-<nombre-equipo> --python 3.14
cd proyecto-<nombre-equipo>
uv add pandas numpy matplotlib seaborn scikit-learn jupyter ipykernel
uv add --dev pytest ruff
```

Luego organicen la estructura base que usarán todo el curso. Usen el comando de su sistema operativo:

**macOS / Linux (y Git Bash en Windows)**

```bash
mkdir -p data/raw data/processed notebooks src docs models
touch data/raw/.gitkeep data/processed/.gitkeep models/.gitkeep
```

**Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Force -Path data/raw, data/processed, notebooks, src, docs, models
New-Item -ItemType File -Force -Path data/raw/.gitkeep, data/processed/.gitkeep, models/.gitkeep
```

**Windows (Símbolo del sistema, CMD)**

```cmd
mkdir data\raw data\processed notebooks src docs models
type nul > data\raw\.gitkeep
type nul > data\processed\.gitkeep
type nul > models\.gitkeep
```

**Alternativa para cualquier sistema (con Python, mediante uv)**

```bash
uv run python -c "from pathlib import Path; [Path(d).mkdir(parents=True, exist_ok=True) for d in ['data/raw', 'data/processed', 'notebooks', 'src', 'docs', 'models']]; [Path(f).touch() for f in ['data/raw/.gitkeep', 'data/processed/.gitkeep', 'models/.gitkeep']]"
```

> **¿Para qué sirve `.gitkeep`?** Git no guarda carpetas vacías. Un archivo vacío llamado `.gitkeep` hace que la carpeta se suba al repositorio aunque todavía no tenga contenido.

### Paso 2: Revisar lo que se generó (10 min)

Abran `pyproject.toml` y `uv.lock` y respondan **entre los tres**:

1. ¿Qué versión exacta de `pandas` quedó en `uv.lock`? ¿Qué rango quedó en `pyproject.toml`?
version pandas = name = "pandas"
en el uv.lock version = "3.0.6"
en el pyproject.toml pandas>=3.0.6

En pyproject.toml aparece pandas>=3.0.6 porque se permite instalar la versión 3.0.6 o cualquier versión superior compatible. Sin embargo, en uv.lock queda registrada la versión exacta instalada, lo que garantiza que todos los integrantes del equipo trabajen con las mismas dependencias y obtengan los mismos resultados.

2. ¿Cuántos paquetes hay en `uv.lock`? (pista: `uv tree` o buscar `[[package]]`). ¿Por qué son muchos más que los que instalaron?
En uv.lock hay 138 paquetes. Esto es mucho más que las librerías que instalamos directamente (pandas, numpy, matplotlib, seaborn, scikit-learn, jupyter, etc.) porque cada una de ellas necesita otras librerías para funcionar. Estas dependencias adicionales se instalan automáticamente y también quedan registradas en uv.lock.

Por ejemplo, pandas depende de otras librerías como numpy, y herramientas como jupyter e ipykernel requieren numerosos paquetes adicionales para gestionar notebooks, kernels y componentes interactivos. Por eso el número total de paquetes crece rápidamente.

La función de uv.lock es registrar todas las dependencias directas e indirectas, garantizando que cualquier persona del equipo instale exactamente el mismo entorno y obtenga los mismos resultados
3. ¿Qué pasa si borran `.venv/` y ejecutan `uv run python -c "import pandas"`?
Al borrar .venv, se elimina el entorno virtual. Sin embargo, al ejecutar uv run, uv puede recrearlo automáticamente e instalar las dependencias definidas en el proyecto, permitiendo seguir trabajando con el mismo entorno
### Paso 3: Probar la reproducibilidad (15 min)

1. El piloto comprime la carpeta **sin `.venv/`** y la comparte por el chat de Zoom. Mientras no tengan GitHub, esta es la forma de pasarse el proyecto; en el módulo 01 lo reemplazamos por un repositorio.
2. El copiloto y el relator la descomprimen y ejecutan `uv sync`.
3. Cada uno ejecuta:
   ```bash
   uv run python -c "import pandas, sklearn; print(pandas.__version__, sklearn.__version__)"
   ```
4. **Comparen:** ¿a los tres les salieron las mismas versiones?

### Paso 4: Documentar (10 min)

El relator escribe en el `README.md` del proyecto:

```markdown
# Proyecto <nombre del equipo>

## Integrantes
- Nombre 1
- Nombre 2
- Nombre 3

## Cómo ejecutar
uv sync
uv run jupyter lab
```

### Entregable (lo usaremos en el módulo 01)

La carpeta `proyecto-<nombre-equipo>/` con:

- [ ] `pyproject.toml` con las dependencias
- [ ] `uv.lock`
- [ ] `.python-version` con `3.14`
- [ ] Estructura de carpetas `data/`, `notebooks/`, `src/`, `docs/`, `models/`
- [ ] `README.md` con integrantes y cómo ejecutar

### Reto opcional (si les sobra tiempo)

1. Agreguen un script `src/hola.py` que imprima la versión de Python y de pandas, y ejecútenlo con `uv run python src/hola.py`.
2. Prueben `uv add "pandas<3"` y miren qué cambió en `uv.lock`. Después vuelvan a `uv add "pandas>=3"`.
3. Investiguen: ¿qué hace `uv export --format requirements-txt`? ¿Cuándo sería útil?

---

## Preguntas para la plenaria

1. ¿Por qué `.venv/` no se comparte pero `uv.lock` sí?
el .venv es un entorno y es muy pesado esto imposibilita la facil descarga del modelo
2. ¿Qué error les salió y cómo lo resolvieron? (esto ayuda a todo el grupo)

3. En una frase: ¿qué ventaja tiene `uv` frente a `pip install` global?

## Para la próxima sesión

Traigan la carpeta del proyecto del equipo. En el **módulo 01** la convertiremos en un **repositorio de GitHub** compartido.
