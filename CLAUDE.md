# Guida per l'IA: Knuckles 4

Leggi tutto prima di fare qualsiasi cosa. Sono regole di Sonic, il proprietario.
Rispondi sempre in italiano, con frasi semplici. Se qualcosa non è chiaro, chiedi a Sonic.

## 1. Come è fatto

Knuckles 4 è un progetto TUTTO SUO. Non ha niente in comune con Knuckles Adventure
(niente repo, sito, launcher, codice, immagini o stile in comune).

Tutto sta in questo repo `patata3213-art/knuckles4-sito` (pubblico), ma il gioco è CIFRATO con la password.

| Ramo | Cosa contiene |
|---|---|
| `main` | il sito: `index.html`, `media/`, `launcher.bin` (il launcher cifrato), questa guida |
| `gioco` | il gioco cifrato: `Knuckles4Coop.exe.k4`, `Knuckles4Coop.pck.k4`, `patches/*.pck.k4`, `controllo.k4`, `version.json` (in chiaro) |

Sito: https://patata3213-art.github.io/knuckles4-sito/ (GitHub Pages dal ramo `main`).

**La password la sa solo Sonic. NON scriverla MAI** in file, commit, codice, README o messaggi pubblici.
Quando ti serve (per cifrare), chiedila a Sonic e passala solo come variabile d'ambiente.

## 2. Come funziona

1. Il giocatore apre il sito: c'è una pagina che chiede la password.
   Il sito controlla la password provando a decifrare un pezzo di dati (`PAYLOAD` in `index.html`).
2. Dopo la password vede il sito e il pulsante "Scarica il launcher":
   il browser scarica `launcher.bin` e lo decifra con la password -> `Knuckles4Launcher.exe`.
3. Il launcher chiede la password (una volta, la salva in `Knuckles4/password.cfg`).
4. Legge `version.json` dal ramo `gioco`, scarica i file `.k4`, li decifra e li mette in `Knuckles4/`.
5. AGGIORNA scarica solo le patch nuove. GIOCA avvia `Knuckles4/Knuckles4Coop.exe`.

La password si usa sempre in minuscolo (il sito e il launcher la trasformano da soli).

Due tipi di cifratura:
- **sito** (`launcher.bin` e `PAYLOAD`): AES-GCM con chiave PBKDF2-SHA256, 600000 giri, `SALT` in `index.html`.
- **gioco** (`*.k4`): formato di Godot `FileAccess.open_encrypted_with_pass`. Si crea con
  `knuckles4/cifra/cifra.gd` (nei file del progetto):
  `K4_PASS=<password minuscola> godot --headless --path knuckles4/cifra -s cifra.gd -- <file> <file>.k4`

## 3. Pubblicare una patch (caso normale)

Prima chiedi l'ok a Sonic. Mai patch senza il suo ok.

1. Parti dal sorgente giusto (l'ultimo approvato da Sonic; oggi `knuckles4/sorgente/knuckles4_0.7.2.bundle`, ramo `v071`, che va sopra `knuckles4_0.7.0_pubblica.bundle`).
   Alza la versione in `version.json` e in `project.godot` (`config/version`), es. 0.7.3.
2. Esporta con Godot 4.3 e crea la patch `.pck` con `tools/make_patch.py` (solo i file cambiati, sotto 100 MB).
3. Cifra la patch: `patches/0.7.3.pck` -> `patches/0.7.3.pck.k4` (vedi sopra).
4. Ramo `gioco`: aggiungi `patches/0.7.3.pck.k4`; in `version.json` cambia `"version"`,
   aggiungi `{"version": "0.7.3", "file": "patches/0.7.3.pck.k4"}` in fondo a `"patches"`, scrivi `"notes"`.
   Non toccare `base`, `runtime`, `exe`, `pck`. Commit e push.
5. (Facoltativo) per mostrare la versione nuova sul sito va rifatto il `PAYLOAD` in `index.html`
   (stessa password e stesso SALT): `{"version":"0.7.3","base":"0.7.0","patches":["0.7.1","0.7.2","0.7.3"]}`.

## 4. Cambiare il sito

- Tutto è in `index.html`. Immagini e video in `media/`. Stile "2.5D" tutto suo: tramonto ad Angel Coast,
  checkpoint che si accendono, HUD con tempo e anelli, testi estrusi (`.x3d`), lastre (`.slab`),
  anelli 3D (`.ring`), parallasse (`data-depth`), inclinazione (`data-tilt`).
- Immagini nuove: solo screenshot o render del NOSTRO gioco.
- Non copiare nulla dal sito di Knuckles Adventure (né codice, né frasi, né stile).
- Prova la pagina su computer e su telefono (390 px) prima del push.

## 5. Nuovo launcher (raro)

Sorgente in `knuckles4/launcher4_sorgente/` (nei file del progetto, Godot 4.3; leggi il suo LEGGIMI per l'icona).
Alza `LAUNCHER_VERSION`, esporta "Windows", cifra l'exe in `launcher.bin` con la cifratura del SITO
(stesso SALT e password), sostituisci `launcher.bin` nel ramo `main`, e alza `"launcher"` in `gioco/version.json`.

## 6. COSE DA NON FARE MAI

- **Non toccare Knuckles Adventure** (`patata3213-art/knuckles-adventure-download` e il suo sito). Solo guardare, se serve.
- **Mai scrivere la password** da nessuna parte.
- **Mai mettere file NON cifrati del gioco** in questo repo: niente `.exe`/`.pck` in chiaro, sorgenti, file `.patch`,
  progetto Godot, modelli, audio sciolti, Blender, backup.
- Niente hub (Station Square, Mystic Ruins) e niente livelli presi da giochi SEGA.
- Non usare i repo `Knuckles-4` e `Knuckles-4-download`: sono vecchi e restano privati.
- Non cancellare o cambiare le patch vecchie. Niente force push, niente rami cancellati.
