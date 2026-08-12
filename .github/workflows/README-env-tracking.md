# Env tracking automático (PR → Environment de la HU)

Automatiza el "¿dónde va el cambio?" (**Dev/QA/Staging/Prod**) en el GitHub Project #3,
sin ensuciar el board con tarjetas de PR. Cada PR que se mergea actualiza el campo
**Environment** (y **Status=Done** en `main`) de la HU que referencia.

## Cómo funciona
1. En el PR (de cualquier `*-db` / backend / front), en el título o cuerpo, referencia la HU:
   ```
   Closes code-sena/design-software-docs#42
   ```
2. Al mergear el PR:
   - a `develop` → HU **Environment = Dev**
   - a `qa`      → HU **Environment = QA**
   - a `staging` → HU **Environment = Staging**
   - a `main`    → HU **Environment = Prod** + **Status = Done**
3. En el board ves, por HU, hasta dónde promovió — automático.

## Instalación (una vez)

### 1) Secret de organización `PROJECTS_TOKEN`
El `GITHUB_TOKEN` por defecto **no** puede escribir en Projects de la org, así que se usa un PAT:
- Crea un **Fine-grained PAT** (o classic con scope `project` + `read:org`) de una cuenta con
  acceso de escritura al Project #3 (p. ej. `ariel5253`).
- Org **code-sena** → Settings → Secrets and variables → Actions → **New organization secret**
  → nombre **`PROJECTS_TOKEN`** → valor = el PAT → *Repository access* = los repos `design-software-*`.

### 2) Copiar el workflow a cada repo de código
Copia `env-tracking.yml` a `.github/workflows/env-tracking.yml` en cada repo
(`*-db`, y a futuro backend/front). **Importante:** para que dispare al mergear a cada rama,
el archivo debe existir en **esa rama base** (develop/qa/staging/main).

### 3) Rollout por git-flow (modelo de rama hija)
Como es un cambio **aditivo**, se propaga igual que cualquier otro: rama hija por ambiente
+ PR a su padre (ver skill `design-software-git-flow`). Sugerido: **piloto** en un repo
(`scheduling-db`, rama `develop`), verificar que corre, y luego replicar a los 4 ambientes
de los 9 repos.

## Notas
- Los IDs de campo/opción están embebidos en el workflow (Project #3, campos Environment y
  Status). Si se recrean esos campos, actualizar los IDs (ver skill `design-software-project`
  → `references/github-project.md`).
- No requiere que los PRs sean ítems del board — actúa directo sobre la HU.
- Extensible: se puede añadir Status=In Review al mergear a develop, etc.
