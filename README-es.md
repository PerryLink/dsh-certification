<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-hero-en-dark.webp">
  <img src="docs/assets/dsh-certification-hero-en-light.webp" width="100%" alt="dsh-certification: un registro de atestaciones de cinco dimensiones — data/attestations.json registra 1 repositorio atestado (PerryLink/dsh-auto-review, grado A, snapshot 2026-11-01) en manifest, higiene de build, cadena de suministro (Scorecard 4.1), integridad de release y humo de instalación; check-freshness.mjs verifica frescura, el sha256 del espejo dsh-cert-mcp y ambos marcadores del README">
</picture>

# dsh-certification

Registro comunitario de certificación para plugins de DeepSeek Harness: especificación pública, modelo de puntuación verificable por máquina y un registro independiente con badges.

[English](README.md) · [简体中文](README-zh.md) · [Español](README-es.md) · [Português](README-pt.md) · [हिन्दी](README-hi.md)

</div>

> Este archivo es la entrada localizada en español; el cuerpo técnico completo vive en el [README.md](README.md) en inglés y no se repite aquí.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-flow-v5-dark.svg">
  <img src="docs/assets/dsh-certification-flow-v5-light.svg" width="100%" alt="Diagrama de estructura de dsh-certification: las cinco dimensiones A–E (manifest, higiene de build, cadena de suministro, integridad de release, humo de instalación) alimentan data/attestations.json; scripts/check-freshness.mjs verifica frescura ≤ 30 días, el sha256 del espejo dsh-cert-mcp y los marcadores del README; scripts/badge.mjs genera badges/<owner>__<repo>.svg y .github/workflows/registry.yml los re-renderiza a diario">
</picture>

*Estructura: cinco dimensiones → data/attestations.json → puertas check-freshness → badges. Las etiquetas del diagrama son nombres de script, rutas y archivos de workflow reales, por eso una sola imagen sirve a los cinco idiomas.*

## Las cinco dimensiones

![dsh-certification: ejecución real de node scripts/check-freshness.mjs — freshness ok (generatedAt más antiguo 2026-11-01, 20 días, límite 30), mirror ok (sha256 bab74bc817b1b511… idéntico), roster-count 98 ok, attested-count 1 ok y la prosa baseline-batch 98/87/8/3/1 ok, terminando en check-freshness: ok](docs/assets/dsh-certification-demo.gif)

*Animación: node scripts/check-freshness.mjs re-ejecutado el 2026-11-21 con DSH_KIT_ROSTER apuntando al checkout del roster dsh-kit — stdout real, sin llamadas de red.*

A–E cubren manifest, higiene de build, cadena de suministro (OpenSSF Scorecard), integridad de release (npm provenance) y humo de instalación (registros de `dsh-test-drive`, cuatro estados `ok` / `load-fail` / `install-fail` / `skip`); los criterios completos están en la sección The five dimensions del [README.md](README.md) en inglés.

## Grados

`A` / `B` / `C` / `D` más un `no-data` gris: E debe ser `ok`, cualquier puerta dura fallida es D, y un veto de seguridad lleva a D de inmediato; un E bloqueado por el entorno registra `environment-blocked` y conserva B; las reglas completas están en la sección Grades del README en inglés.

## Disciplina de evidencia (estructural)

Cada puntuación debe venir de una ejecución real y reproducible — resultado de sonda, ejecución de Scorecard, verificación de provenance o registro de test-drive; la evidencia ausente es `no-evidence`, nunca una suposición; cada dimensión guarda enlaces de auditoría con fecha de snapshot.

## Registro y badges

`data/attestations.json` es el registro completo y `badges/<owner>__<repo>.svg` lo genera `scripts/badge.mjs` (el registro es la única fuente de verdad); `.github/workflows/registry.yml` los re-renderiza a diario; la URL de badge pegable y el aviso de 404 están en el README en inglés.

## Puertas

`node scripts/check-freshness.mjs` verifica tres cosas: evidencia de no más de 30 días, sha256 idéntico entre este registro y el espejo de `dsh-cert-mcp`, y los marcadores roster-count/attested-count del README derivados de `dsh-kit/data/repos.json`; la ejecución real está en la animación de arriba.

## Relación con herramientas existentes

`dsh-test-drive` aporta los registros de la dimensión E, `dsh-score` consume los registros de certificación, `dsh-skill-pack-security` aporta el escrutinio de patrones maliciosos de la dimensión B y OpenSSF Scorecard aporta la dimensión C completa; el solapamiento con `dsh-plugin-scorecard` y otros se discute en el issue #1.

## Relación con los niveles DSH

`A`/`B`/`C`/`D` es un grado de calidad y evidencia; `DSH-ENTRY` / `DSH-FLOW` / `DSH-BOOST` es profundidad de adopción. Los dos ejes son ortogonales: este registro no anota resultados G0–G5 ni J1–J8; el crosswalk completo está en el README en inglés y en `STANDARDS.md`.

## Hoja de ruta

Spec v1 y la CI de registro/badges ya están; el lote base de la familia son 98 repositorios no-infra (87 active / 8 frozen / 3 retired), con 1 atestado; los números los afirma la puerta, no la prosa; la hoja de ruta de 5 pasos está en el README en inglés.

## Familia de plugins PerryLink DSH

Este repositorio forma parte de la familia de plugins de DeepSeek Harness de [PerryLink](https://github.com/PerryLink); el roster completo (desplegable) está en el [README.md](README.md) en inglés.

## Licencia

MIT. Véase [LICENSE](./LICENSE).

## Datos HCB

`dsh-hcb` 0.12.0 es la fuente canónica de compatibilidad, tracks y riesgo declarados (136 checked + 7 tested, 3 FLOW `manual`, gate 2 blocked); la certificación A–D y la puerta HCB son ortogonales; véase la sección HCB data del README en inglés.

[Privacy](PRIVACY.md) · [Security](SECURITY.md) · [Contributing](CONTRIBUTING.md) · [Changelog](CHANGELOG.md)
