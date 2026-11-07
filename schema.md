# Tracker progetti: schema dati

App: **Progetti di Andrea** → https://claude.ai/artifact/RN7JWvzUg4RxWEqKVP4nfi
Sorgente: `app.html` (si ripubblica con l'Artifact tool sullo stesso file, l'URL resta uguale).

Dati nel database dell'artifact (capability `db`), regola unica `{path: "", read: "owner", write: "owner"}`: solo Andrea legge e scrive. Claude ci accede come lui con il tool `ArtifactData` (`url` = link sopra).

Date: stringhe `YYYY-MM-DD` (ora locale Europe/Rome). Timestamp: ISO 8601 (`createdAt`, `updatedAt`, `doneAt`, `closedAt`). ID: stringhe alfanumeriche (l'app usa `Date.now().toString(36)` + random; da chat vanno bene id leggibili tipo `sito-fireflow`, solo lettere minuscole, cifre e `-`).

## Collezioni

### `areas`
| Campo | Tipo | Note |
|---|---|---|
| `name` | string | es. CloudSherpa, Fireflow, Personale |
| `color` | string | uno di `#3a5bd6 #c47a14 #2a8c58 #b23c72 #6d52cc #13899a #8b6b3b #c2412e` |
| `order` | number | ordine in barra laterale |
| `createdAt` | ISO | |

### `projects`
| Campo | Tipo | Note |
|---|---|---|
| `name` | string | |
| `areaId` | string \| null | id di `areas` |
| `goal` | string | obiettivo: cosa deve essere vero a progetto finito |
| `status` | `attivo` \| `in pausa` \| `completato` \| `archiviato` | |
| `deadline` | date \| null | scadenza |
| `hill` | number 0–100 | hill chart: <50 in salita (da capire), ≥50 in discesa (da eseguire), 100 finito |
| `notes` | string | |
| `createdAt`, `updatedAt` | ISO | aggiornare `updatedAt` a ogni attività sul progetto |

### `tasks`
| Campo | Tipo | Note |
|---|---|---|
| `title` | string | |
| `projectId` | string \| null | se c'è, `areaId` è null (l'area si ricava dal progetto) |
| `areaId` | string \| null | per compiti di un'area senza progetto |
| `due` | date \| null | quando farlo. Compare in **Oggi** se `due <= oggi` |
| `status` | `open` \| `waiting` \| `done` | |
| `waitingFor` | string | chi si aspetta, se `waiting` |
| `note` | string | |
| `createdAt` | ISO | |
| `doneAt` | ISO \| null | impostato quando `status = done` |

### `assets`
| Campo | Tipo | Note |
|---|---|---|
| `name` | string | |
| `type` | `Doc` `Foglio` `Slide` `Cartella` `File` `PDF` `Design` `Board` `Repo` `Notion` `Link` `Nota` | |
| `url` | string | può essere vuoto |
| `projectId` / `areaId` | string \| null | uno dei due, o nessuno |
| `note` | string | |
| `createdAt` | ISO | |

### `days` (id = `YYYY-MM-DD`)
Un documento per giornata: registro e chiusura.
| Campo | Tipo | Note |
|---|---|---|
| `date` | date | uguale all'id |
| `done` | `[{id, title, projectId, areaId}]` | compiti completati quel giorno, copiati alla chiusura |
| `extra` | `[{text, projectId, areaId}]` | altre attività svolte (anche annotate dal dettaglio progetto) |
| `note` | string | nota del giorno |
| `tomorrow` | `[taskId]` | max 3 priorità per il giorno lavorativo successivo |
| `tomorrowDate` | date \| null | il giorno a cui valgono le priorità |
| `closedAt` | ISO \| null | null se il documento esiste solo per qualche annotazione |

**Attività svolte di un progetto** = compiti `done` con quel `projectId` + voci `extra` di tutti i `days` con quel `projectId`.

## Inserimento rapido (barra in alto)
- `#nome` → progetto o area (match parziale, senza spazi e accenti; vince il nome esatto)
- date: `oggi`, `domani`, `dopodomani`, `lunedì`…`domenica`, `venerdì prossimo`, `tra 3 giorni`, `tra 2 settimane`, `prossima settimana` (= lunedì), `fine mese`, `fine settimana` (= venerdì), `15/11`, `15 nov`, `il 20`
- `@attesa Luca` → compito in attesa di Luca
- link + `+asset` → asset invece di compito
- In vista Oggi un compito senza data prende la data di oggi; dentro un progetto o un'area si collega lì.

## Claude nell'app
Pannello "Chiedi a Claude" (capability `sample`, sull'account di chi guarda). A ogni messaggio la pagina manda regole, calendario dei prossimi giorni, dati attuali e la chat (ultimi 16 messaggi); la chat resta solo nel browser (`localStorage`, chiave `pda-chat-v1`).
Strumenti della pagina: `add_task`, `update_task`, `log_activity`, `add_asset`, `add_project`, `update_project`. Nessuno cancella; ogni modifica compare nella chat con **Annulla**.

## Scorciatoie
`N` o `Ctrl/Cmd+K` nuovo · `/` vai a · `G` poi `O/P/R/A/S/L` Oggi/Prossimi/pRogetti/Aree/aSset/registro · `J` `K` scorri · `X` fatto · `E` modifica · `C` chiudi la giornata · `I` chiedi a Claude · `Esc` chiudi
