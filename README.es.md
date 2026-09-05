# beacon-2026

Anclaje de emisiones del año.

Este es un repositorio de **anclaje**: guarda pruebas criptográficas con fecha, y todo su valor
depende de que **nada de lo que entra se modifique después**.

> 🇬🇧 In English: [`README.md`](README.md)

## Qué hay adentro

Cada turno deja dos archivos firmados:

```
emissions/AAAA/MM/DD/HHMM-commitment.jws   el compromiso, publicado ANTES de que el insumo exista
emissions/AAAA/MM/DD/HHMM-emission.jws     el resultado, publicado después
```

Cuando un turno no se puede completar, un `-failure.jws` ocupa el lugar del `-emission`. Dice por
qué, y revela la semilla para que cualquiera pueda calcular cuál habría sido el resultado.

## Cómo comprobar que esto solo agrega

No hace falta creernos. Preguntá:

```bash
# La rama está protegida — sin credenciales
curl -s https://api.github.com/repos/utc24h/beacon-2026/branches/main

# Nada se modificó después de publicado: solo agregados
git log --diff-filter=M --oneline -- emissions/
```

El segundo comando **tiene que no devolver nada**. Si devuelve algo, un archivo se tocó después de
firmado.

## Cómo comprobar que el compromiso fue primero

La prueba no es nuestro reloj. Es que el compromiso ya estaba guardado **antes de que existiera la
ronda de drand que nombra**. Dos preguntas, sin credenciales:

```bash
# 1. ¿Cuándo guardó el proveedor el compromiso? Esa fecha es suya, no nuestra.
curl -s "https://api.github.com/repos/utc24h/beacon-2026/commits?path=emissions/2026/01/01/0000-commitment.jws&per_page=1"

# 2. ¿Cuándo nació esa ronda de drand? Hora nominal = genesis_time + (ronda - 1) x period
curl -s https://api.drand.sh/8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce/info
```

La primera fecha tiene que ser **anterior** a la segunda. Cambiá la ruta por el turno que estés
mirando; el número de ronda está adentro del archivo, en `drand_round`.

El `signed_at_utc` que va adentro del archivo es nuestro, y a vos no te prueba nada. Sirve para
medir, nunca como evidencia.

## Clonar en Windows

El `.gitattributes` de la raíz ya se ocupa. No cambies `core.autocrlf` y no conviertas los archivos:
los `.jws` se verifican **byte por byte**, y un solo final de línea convertido rompe la firma.

---

Creado por `provision/`, no a mano.
