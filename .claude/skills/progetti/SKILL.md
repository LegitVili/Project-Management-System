---
name: progetti
description: Tracker personale dei progetti di Andrea (app "Progetti di Andrea" su claude.ai). Aggiunge e chiude compiti, annota attività svolte, aggiunge asset, chiude la giornata e dà la panoramica dei progetti. Usare quando Andrea parla dei suoi progetti o del suo lavoro ("ho finito X", "domani devo Y", "aggiungi questo link al progetto Z", "chiudi la giornata", "come stanno i miei progetti?"), non per il CRM di CloudSherpa.
---

# Progetti di Andrea

App: https://claude.ai/artifact/RN7JWvzUg4RxWEqKVP4nfi
Schema dati completo e regole dell'inserimento rapido: `schema.md` (leggilo prima di scrivere).

Rispondi in italiano, breve. Oggi = data in Europe/Rome.

## Come leggere e scrivere
Tool `ArtifactData` con `url` = link sopra (caricalo con ToolSearch `select:ArtifactData` se serve).
- Lettura: `list` di `areas`, `projects`, `tasks`, `assets` (pochi documenti: leggi tutto, `limit` 1000). Per `days` usa `query` con `order_by` `date` `desc` e `limit` 14.
- Scrittura: `set` per creare (id leggibile minuscolo con `-`, es. `t-chiamare-marco-1102`), `update` per modificare con `if_version` = versione appena letta. Più di due scritture → un solo `batch`.
- Ogni attività su un progetto: aggiorna anche `projects/<id>.updatedAt`.
- Mai cancellare senza conferma esplicita. Mai inventare progetti, scadenze o compiti che Andrea non ha detto.

## Cosa fare

**"Ho fatto X" / "fatto X"** → trova il compito aperto corrispondente (titolo simile, progetto citato). Se c'è: `status: done`, `doneAt: now`. Se non c'è: aggiungi una voce `{text, projectId, areaId}` a `days/<oggi>.extra` (crea il documento se manca, con `done: [], note: "", tomorrow: [], tomorrowDate: null, closedAt: null`). Conferma in una riga.

**"Domani devo Y #progetto" / nuovo compito** → crea in `tasks` con le stesse regole della barra (date relative, `#progetto`, `@attesa`). Se il progetto è ambiguo chiedi; se manca, compito senza progetto.

**Asset** ("salva questo link nel progetto Z") → crea in `assets`, tipo dedotto dall'URL (Google Doc → `Doc`, Sheets → `Foglio`, Slides → `Slide`, cartella Drive → `Cartella`, GitHub → `Repo`, Notion → `Notion`, Figma → `Design`, Miro → `Board`, .pdf → `PDF`, altro → `Link`).

**Nuovo progetto** → chiedi solo ciò che manca tra nome, area, obiettivo, scadenza; `status: attivo`, `hill: 0`. "Ho capito come farlo" / "ora è solo esecuzione" → `hill` almeno 55.

**"Chiudi la giornata"**
1. Mostra i compiti con `doneAt` di oggi e chiedi cos'altro ha fatto (una riga per cosa, con il progetto se c'è).
2. Elenca i compiti aperti con `due <= oggi` e proponi di spostarli al prossimo giorno lavorativo; chiedi le 3 priorità di domani.
3. Dopo la risposta, un `batch`: aggiorna i compiti, scrivi `days/<oggi>` con `done`, `extra`, `note`, `tomorrow` (max 3 id), `tomorrowDate` (prossimo giorno lavorativo), `closedAt`. Se il documento esiste già, conserva le voci `extra` presenti.
4. Conferma in una riga e ricorda che il registro è nella vista Registro dell'app.

**"Come stanno i miei progetti?" / panoramica**
Per area, solo progetti `attivo` / `in pausa`:
```
CloudSherpa
- Assistente CRM CloudSherpa · in discesa · scade 20/11 (tra 18 g) · prossima: rivedere report
Fireflow
- Sito Fireflow · in salita · nessuna scadenza · ⚠ nessuna prossima azione
```
Poi in fondo, solo se ci sono: scadenze entro 7 giorni, progetti fermi da più di 7 giorni (ultima attività = max tra `updatedAt`, `doneAt`/`createdAt` dei compiti, date dei `days.extra` del progetto), progetti attivi senza compito aperto, compiti in ritardo, cose in attesa da più di 5 giorni. Chiudi con una domanda sola: cosa vuole sbloccare per primo.

## Modifiche all'app
Il sorgente è `app.html`: si modifica lì e si ripubblica con l'Artifact tool sullo stesso `file_path` (in un'altra sessione passando `url`), così il link non cambia e i dati restano. Aggiorna `schema.md` se cambia lo schema.
