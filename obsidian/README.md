# 🔮 Obsidian — UI Visual para la Memoria de Maximus

**Estado:** Idea documentada, no urgente  
**Esfuerzo estimado:** 2-4 horas  
**Dependencias:** Ninguna (solo instalar Obsidian Desktop)  
**Prioridad:** Media — implementar cuando la memoria crezca mucho

---

## ¿Qué es Obsidian?

Obsidian es una aplicación de notas que trabaja directamente sobre archivos Markdown locales. No necesita servidor, no necesita base de datos. Abre una carpeta y ya.

Lo importante: **la memoria de Maximus YA es archivos `.md`.** Obsidian los lee sin ningún cambio.

---

## ¿Qué problema resuelve?

Hoy, si Jose quiere leer o editar la memoria de Maximus, tiene que:
1. Entrar al VPS por SSH
2. Navegar a `/app/data/memory/`
3. Abrir archivos con `cat` o `nano`

Con Obsidian:
1. Abrir Obsidian en la laptop
2. Ver toda la memoria como un grafo visual interactivo
3. Editar con un editor bonito con preview

También resuelve:
- **Búsqueda visual**: buscar cualquier concepto en toda la memoria
- **Graph view**: ver cómo conectan los temas entre sí
- **Edición fácil**: Jose puede editar el canon de Maximus sin terminal

---

## ¿Cómo lo implementaríamos?

### Opción A: Obsidian + rclone (recomendada)

```
VPS (/app/data/memory/)
         ↕ sync cada 5 min
    rclone (en laptop)
         ↕
   Obsidian Vault (local)
```

**Implementación:**

1. Instalar `rclone` en la laptop de Jose
2. Configurar sync con el VPS por SFTP:
```bash
rclone sync user@vps:/app/data/memory/ ~/obsidian-maximus/ --sftp-host VPS_IP
```
3. Crear un cron en la laptop que sincronice cada 5 min:
```
*/5 * * * * rclone sync user@vps:/app/data/memory/ ~/obsidian-maximus/
```
4. Abrir `~/obsidian-maximus/` como vault en Obsidian

**Resultado:** Memoria sincronizada en tiempo casi-real, editable desde laptop.

---

### Opción B: Obsidian Sync (más caro, más simple)

Obsidian tiene su propio servicio de sync ($4/mes).

```
VPS → escribe archivos → GitHub/Obsidian Sync → Obsidian en laptop
```

Funciona igual pero tiene costo. No necesaria para nuestro caso.

---

### Opción C: Obsidian en el VPS con port-forward (técnica)

```bash
# En el VPS, instalar Obsidian (Linux AppImage)
# Port-forward la pantalla por SSH con X11
ssh -X user@vps obsidian
```

Funciona pero es incómodo. No recomendada.

---

## Estructura del Vault

La memoria de Maximus ya tiene la estructura correcta para Obsidian:

```
memory/
├── canon/           → Notas principales del vault
│   ├── quien-es-maximus.md
│   ├── jose-preferences.md
│   └── sistema-memoria.md
├── journal/         → Diario diario (cronológico)
│   └── 2026-04-18.md
├── decisions/       → Decisiones importantes
│   └── tech-decisions.md
├── projects/        → Estado de proyectos
│   └── maximus-telegram.md
└── inbox/           → Auto-memorias de Maximus
    └── pending/
```

**Con Obsidian**, se pueden agregar links entre archivos:
```markdown
En este proyecto usamos [[sistema-memoria]] y las preferencias de [[jose-preferences]].
```

Obsidian genera automáticamente el **Graph View** — un mapa visual de cómo conectan todos los conceptos.

---

## Beneficios concretos

| Beneficio | Sin Obsidian | Con Obsidian |
|-----------|-------------|--------------|
| Leer memoria | SSH + terminal | Click en archivo |
| Editar canon | nano/vim en VPS | Editor rico con preview |
| Buscar concepto | grep por SSH | Cmd+F global instantáneo |
| Ver conexiones | Imposible | Graph view visual |
| Historial cambios | Git log | Git log + diff visual |

---

## Lo que NO necesita cambio en el código

- **Nada de `memory.js`** — los archivos ya son `.md`
- **Nada de `bot.js`** — Maximus sigue leyendo igual
- **Nada del Docker** — Obsidian corre en laptop, no en VPS

Solo se necesita el sync de archivos. El sistema de Maximus no se toca.

---

## ¿Cuándo implementarlo?

**Señales de que ya es tiempo:**
- Canon tiene más de 20 archivos
- Jose quiere editar la memoria frecuentemente
- Se quiere hacer un "audit" visual de qué sabe Maximus

**Por qué esperar:**
- Hoy la memoria es manejable sin UI
- Agregar complejidad antes de necesitarla no tiene sentido
- Prioridad menor vs Linear y otras integraciones activas

---

## Recursos

- [Obsidian.md](https://obsidian.md) — descarga gratuita
- [rclone.org](https://rclone.org) — sync con VPS
- [Obsidian SFTP Sync Plugin](https://github.com/nicholaswilde/obsidian-remote-sync) — alternativa

---

*Documentado por Maximus el 2026-04-18*
