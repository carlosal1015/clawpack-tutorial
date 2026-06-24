# Agents Configuration — Clawpack Tutorial

Este directorio contiene la configuración de agentes de IA (Gemini CLI / Antigravity)
para el proyecto **Clawpack Tutorial**, incluyendo instrucciones de proyecto, reglas de
comportamiento y documentación de la configuración de Codespaces.

---

## 📁 Estructura

```
.agents/
├── README.md      ← este archivo (documentación general)
└── AGENTS.md      ← prompt/reglas del agente para el proyecto Clawpack
```

---

## 🔑 Configuración de `GEMINI_API_KEY` en GitHub Codespaces

El proyecto está configurado para que Gemini CLI funcione automáticamente dentro de
un Codespace. El flujo es:

```
GitHub Secret  →  Host de Codespaces  →  containerEnv  →  ~/.gemini/.env
(GEMINI_API_KEY)   (inyectado auto)      (disponible en     (leído por Gemini CLI
                                          el contenedor)      automáticamente)
```

### ✅ Lo único que debes hacer (1 solo paso)

Ve a: **https://github.com/settings/codespaces** → **New secret**

| Campo                 | Valor                              |
|-----------------------|------------------------------------|
| **Name**              | `GEMINI_API_KEY`                   |
| **Value**             | Tu API key de Gemini (`AIzaSy...`) |
| **Repository access** | seleccionar `clawpack-tutorial`    |

> Los secretos de Codespaces se inyectan automáticamente como variables de entorno
> del host antes de que se cree el contenedor. La sintaxis `${localEnv:GEMINI_API_KEY}`
> en `containerEnv` los transfiere al interior del contenedor.

### Cómo obtener la API key

1. Ve a [Google AI Studio](https://aistudio.google.com/apikey)
2. Crea o copia tu API key
3. Pégala como valor del secreto en Codespaces

Referencia oficial de autenticación: https://geminicli.com/docs/get-started/authentication

---

## 🔧 Cómo funciona el `devcontainer.json`

El archivo `.devcontainer/devcontainer.json` realiza tres acciones automáticas
al crear el Codespace:

1. **`containerEnv`**: lee `GEMINI_API_KEY` del host de Codespaces y la expone al
   contenedor como variable de entorno.

2. **`postCreateCommand`**: crea `~/.gemini/.env` con la API key para que Gemini CLI
   la detecte automáticamente:
   ```bash
   mkdir -p ~/.gemini
   [ -n "$GEMINI_API_KEY" ] \
     && echo "GEMINI_API_KEY=$GEMINI_API_KEY" > ~/.gemini/.env \
     && echo '✅ GEMINI_API_KEY saved to ~/.gemini/.env' \
     || echo '⚠️  GEMINI_API_KEY not set — add it as a Codespaces secret'
   ```

3. **`image`**: usa la imagen Docker pre-configurada con Clawpack instalado:
   ```
   ghcr.io/cm032/images/python-clawpack:latest
   ```

### Verificación dentro del Codespace

```bash
# Confirmar que la variable está disponible
echo $GEMINI_API_KEY

# Confirmar que Gemini CLI puede autenticarse
gemini --version
```

---

## 🔄 Reconstruir el contenedor

Si ya tenías el Codespace abierto cuando agregaste el secreto, reconstruye el
contenedor para que los cambios surtan efecto:

- **VS Code**: `Ctrl+Shift+P` → `Codespaces: Rebuild Container`
- **GitHub web**: el Codespace → `⋯` → **Stop Codespace** → volver a abrirlo

---

## 🤖 Prompt de inicialización del agente

Ver [`AGENTS.md`](./AGENTS.md) para el prompt del agente especializado en Clawpack
que puedes usar con `gemini` CLI o con Antigravity desde el Codespace.
