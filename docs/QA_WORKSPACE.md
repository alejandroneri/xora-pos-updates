# Política transversal — Workspace QA local

**Aplicación:** todos los agentes y personas que ejecuten pruebas o creen checkouts para este repositorio. Esta política organiza artefactos; **no ejecuta migraciones, borra carpetas ni autoriza releases**.

## Ubicaciones obligatorias en Windows

Raíz general: `$HOME\QA-Workspace` (ejemplo `C:\Users\Aleja\QA-Workspace`).

Raíz de este proyecto: `$HOME\QA-Workspace\XoraPOS`.

```text
QA-Workspace/
  XoraPOS/
    herramientas/                      # scripts de QA, plantillas
    repos/
      xora-pos-updates/
        checkouts/
          PR-000-SHA12/                # working copy aislada, sin logs
        evidencias/
          PR-000/
            AAAAMMDD-HHMMSS-SHA12-ID/
              metadata.json
              resultados.json
              tests.log
              analyze.log
              git-status.txt
        comparaciones/
          PR-000-vs-main/
```

**Prohibido como convención:** crear nuevos directorios `*-QA`, `test-output`, `resultados`, `integrado.txt` o temporales sueltos en `$HOME`, `Desktop`, `Downloads` o en la raíz del repositorio. Herramientas que obligatoriamente generen `build/`, `.dart_tool/` u otros caches propios mantienen esos artefactos dentro del checkout, conforme a `.gitignore`; no se confunden con evidencia.

## Flujo de cada ejecución

1. El Coordinador especifica `TAREA_ID`, `PR`, `SHA_CANDIDATO`, nombre de rama y comandos.
2. Se crea un checkout nuevo por candidato en `checkouts/PR-NNN-SHA12/`. Verificar `git rev-parse HEAD` y `git status --short` antes de probar. Si existe, **no** hacer `git reset --hard`, `git clean`, `pull` ni cambiar rama silenciosamente.
3. Antes de cada ejecución, crear un directorio único bajo `evidencias/PR-NNN/`, registrar `metadata.json` (repo, SHA, rama, SO, SDK, fecha, comando, responsable, origen de los datos) y `resultados.json` (exit codes, estado, rutas de logs).
4. Escribir logs de tests, análisis estático, builds y cualquier reporte a esa carpeta. Comparaciones contra `main` requieren su **propio checkout** con SHA base explícito y registros en `comparaciones/`.
5. Anotar en el PR/issue el SHA exacto, comando, alcance, exit code y ubicación relativa; `PASS_LOCAL`, `NO_EJECUTADO`, `FAIL`, `BLOQUEADO` son distintos. QA independiente reabre el mismo commit, no infiere PASS por la existencia de tests.

## Seguridad y conservación

Conservar los checkouts y logs históricos dispersos hasta acordar su inventario, respaldo y traslado; **no mover, borrar ni limpiar automáticamente**. Antes de compartir logs, eliminar secretos, tokens, UID, finanzas privadas y datos personales. No versionar `QA-Workspace`, binarios, bases reales ni logs crudos. Esta es una convención para nuevas ejecuciones, no una afirmación de que las carpetas ya existan físicamente.

**Regla para agentes:** consultar este documento antes de sugerir comandos PowerShell o ejecutar QA. Para proyectos con varios repositorios, cada uno tendrá su propio subdirectorio bajo `repos/`. Mantener la misma política en todos los repositorios del proyecto.
