# Project Management System

Tracker personale dei progetti di Andrea: progetti, aree, compiti, asset e registro delle giornate.

- **App**: "Progetti di Andrea", artifact privato su claude.ai → https://claude.ai/artifact/RN7JWvzUg4RxWEqKVP4nfi
- **Sorgente**: `app.html` (pagina unica, JS senza build). Si ripubblica con l'Artifact tool: in una sessione nuova passare `url` = link sopra, così il link resta lo stesso e i dati nel database restano.
- **Dati**: database dell'artifact (capability `db`, lettura e scrittura solo owner). Schema e regole dell'inserimento rapido in `schema.md`.
- **Claude nell'app**: pannello "Chiedi a Claude" (tasto `I`, o pulsante tondo su telefono), capability `sample`. Usa l'account di chi guarda, legge i dati e li modifica con strumenti della pagina (mai cancellazioni; ogni modifica ha Annulla). Capabilities dichiarate: `db` (regola owner) + `sample`; in un nuovo publish ometterle per mantenerle.
- **Skill**: `.claude/skills/progetti` per aggiornare il tracker dalla chat ("ho fatto X", "chiudi la giornata", "come stanno i miei progetti?").

Preferenze: risposte brevi in italiano; mai cancellare dati senza conferma; non inventare progetti, scadenze o compiti.
