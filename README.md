# By-Catch
Moodle-Interactive-Group-Quiz
# 🥨 BY-Catch Moodle Quiz (Realtime Gamification System)

Ein hochperformantes, echtzeitfähiges Web-Quiz-System, das nativ auf den Standard-Vorlagen (Templates) von **Moodle-Datenbank-Aktivitäten** aufbaut. Es ermöglicht webbasiertes Gamification im Unterricht über Smartphones, inklusive einer dynamischen, automatischen **Präsentationsansicht (Beamer-Fenster)** mit Live-Ranking und automatischer Punkte-Kalkulation.

---

## 🛠️ Technische Kontaktdaten (Support & Feedback)

Für Fragen, Fehlerberichte (Issues) oder Funktionserweiterungen wende dich bitte an:

* **Entwickler:** Christian
* **Mastodon (Fediverse):** [@Christian_Augsburg@mastodon.social](https://mastodon.social/@Christian_Augsburg)
* **Lizenz:** Veröffentlicht und geschützt unter den Bedingungen der **GNU General Public License v3.0 (GPL-3.0)**.

---

## 💻 Systemarchitektur & Funktionsweise

Das System benötigt **zwei getrennte Moodle-Datenbanken** im selben Kursbereich, um die Last zu verteilen und die Fragen-Templates (Dozenten-Archiv) sauber von der volatilen Spiel-Schnittstelle (Schüler-Antworten) zu trennen:

### 1. Die Programmdatenbank (Live-Schnittstelle)
Diese Datenbank fungiert als hochfrequenter Datenspeicher (Data-Bus) für das laufende Spiel. Über asynchrone `fetch()`-Abfragen im Hintergrund kommunizieren die Smartphones der Schüler, das Dashboard des Spielleiters und das geöffnete Beamer-Fenster in Echtzeit miteinander.
* Der Spielleiter injiziert hierüber den Status (`Aktiv`, `Auswertung`) und die Runden-ID.
* Die Smartphones lesen die aktive Frage aus, stoppen die Reaktionszeit lokal und senden die Antwort-Strings zurück an das LMS.

### 2. Die Schatzkistendatenbank (Content-Archiv)
Dies ist dein privater Fragenpool. Hier speicherst du deine fertigen Quiz-Sets strukturiert ab. Über den eingebauten Kopier-Button können Dozenten die Fragen mit einem Klick in die Zwischenablage laden und direkt in das Live-Dashboard der Programmdatenbank einspeisen.

---

## 🗄️ Erforderliche Datenbankfelder in Moodle

Erstelle in deinen beiden Moodle-Datenbanken exakt die folgenden Felder (Achte penibel auf Groß- und Kleinschreibung bei den Kurznamen!):

### Felder für die „Programmdatenbank“:
| Feldname | Feld-Typ | Beschreibung / Funktion |
| :--- | :--- | :--- |
| `RundenID` | Kurzer Text | Verbindet Fragen und Schülerantworten (z.B. `Q1`) |
| `Text` | Textbereich | Die eigentliche Quizfrage bzw. die Statusphase |
| `Typ` | Kurzer Text | Unterscheidet Datensätze (`Frage`, `Status`, `Schueler`) |
| `OptA` | Kurzer Text | Antwortmöglichkeit A |
| `OptB` | Kurzer Text | Antwortmöglichkeit B |
| `OptC` | Kurzer Text | Antwortmöglichkeit C |
| `OptD` | Kurzer Text | Antwortmöglichkeit D |
| `Wert` | Kurzer Text | Speichert die korrekte Lösung (z.B. `A,C`) oder Schülerwahl |
| `Zeitstempel` | Kurzer Text | Das Zeitlimit der Frage in Sekunden bzw. Antwortzeit in ms |
| `Status` | Kurzer Text | Aktueller Zustand des Eintrags (`Bereit`, `Verwendet`, `Aktiv`) |
| `SpielerName` | Kurzer Text | Name des Teilnehmenden für das Leaderboard |
| `BildURL` | Textbereich | Speichert den Profil-Emoji oder die Avatar-Grafik |

### Felder für die „Schatzkistendatenbank“:
| Feldname | Feld-Typ | Beschreibung / Funktion |
| :--- | :--- | :--- |
| `Thema` | Kurzer Text | Der Name/Titel des Quiz-Sets (z.B. *Steuerlehre - Grundlagen*) |
| `Fragen_Input` | Textbereich | Mehrzeiliges Textfeld für den formatierten KI-Output |

---

## 📐 Strukturierte Moodle-Vorlagen (Templates)

Kopiere die folgenden Code-Blöcke eins zu eins in die jeweiligen HTML-Ansichten deiner Moodle-Datenbank-Aktivitäten.

---

### PRESET I: VORLAGEN FÜR DIE PROGRAMMDATENBANK

#### Vorlage für Einträge hinzufügen:
```html
<div id="defaulttemplate-addentry">
    <div class="mb-3 pt-3">
        <div class="fw-bold">RundenID</div>
        [[RundenID]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">Text</div>
        [[Text]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">Typ</div>
        [[Typ]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">OptA</div>
        [[OptA]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">OptB</div>
        [[OptB]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">OptC</div>
        [[OptC]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">OptD</div>
        [[OptD]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">Wert</div>
        [[Wert]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">Zeitstempel</div>
        [[Zeitstempel]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">Status</div>
        [[Status]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">SpielerName</div>
        [[SpielerName]]
    </div>
    <div class="mb-3 pt-3">
        <div class="fw-bold">BildURL</div>
        [[BildURL]]
    </div>
    ##otherfields##
</div>

```



#### Vorlage Einzelansicht:
```HTML

<div id="defaulttemplate-single" class="my-5">
    <div class="defaulttemplate-single-body my-5">
        <div class="row my-6">
            <div class="col-auto">##userpicture##</div>
            <div class="col">
                <div class="row h-100">
                    <div class="col-3 align-self-center">
                        ##user##<br/><span class="data-timeinfo">##timeadded##</span>
                    </div>
                    <div class="col-4 col-md-6 text-end align-self-center data-timeinfo">
                        <span class="fw-bold ">Zuletzt bearbeitet&nbsp;</span>##timemodified##
                    </div>
                    <div class="col-4 col-md-3 ms-auto align-self-center d-flex flex-row-reverse">
                        <div>##actionsmenu##</div>
                        <div class="ms-auto my-auto ##approvalstatusclass##">##approvalstatus##</div>
                    </div>
                </div>
            </div>
        </div>
        <hr/>
        <div class="my-6">
            <div class="mt-4"><span class="fw-bold">RundenID</span><p class="mt-2">[[RundenID]]</p></div>
            <div class="mt-4"><span class="fw-bold">Text</span><p class="mt-2">[[Text]]</p></div>
            <div class="mt-4"><span class="fw-bold">Typ</span><p class="mt-2">[[Typ]]</p></div>
            <div class="mt-4"><span class="fw-bold">OptA</span><p class="mt-2">[[OptA]]</p></div>
            <div class="mt-4"><span class="fw-bold">OptB</span><p class="mt-2">[[OptB]]</p></div>
            <div class="mt-4"><span class="fw-bold">OptC</span><p class="mt-2">[[OptC]]</p></div>
            <div class="mt-4"><span class="fw-bold">OptD</span><p class="mt-2">[[OptD]]</p></div>
            <div class="mt-4"><span class="fw-bold">Wert</span><p class="mt-2">[[Wert]]</p></div>
            <div class="mt-4"><span class="fw-bold">Zeitstempel</span><p class="mt-2">[[Zeitstempel]]</p></div>
            <div class="mt-4"><span class="fw-bold">Status</span><p class="mt-2">[[Status]]</p></div>
            <div class="mt-4"><span class="fw-bold">SpielerName</span><p class="mt-2">[[SpielerName]]</p></div>
            <div class="mt-4"><span class="fw-bold">BildURL</span><p class="mt-2">[[BildURL]]</p></div>
            ##otherfields##
        </div>
    </div>
</div>
```

###Vorlage Listenansicht (Dreiteilig):

Kopfzeile:
```HTML

<div style="background: #f8f9fa; padding: 15px; border: 2px solid #005a9c; border-radius: 8px; margin-bottom: 15px;">
    <div style="display: flex; align-items: center; justify-content: space-between;">
        <div>
            <input type="checkbox" id="gh-select-all" style="cursor:pointer; transform: scale(1.5); margin-right: 12px; margin-left: 10px;">
            <label for="gh-select-all" style="cursor:pointer; font-weight:bold; font-size: 1.1rem; margin-bottom: 0;">Alle auswählen</label>
        </div>
        <button id="gh-delete-btn" style="padding: 8px 20px; background: #dc3545; color: white; border: none; border-radius: 6px; cursor: pointer; font-weight: bold;">
            🗑️ Markierte Einträge lautlos löschen
        </button>
    </div>
</div>
<script>
document.addEventListener("DOMContentLoaded", function() {
    const selAll = document.getElementById('gh-select-all');
    const delBtn = document.getElementById('gh-delete-btn');
    if(selAll) { selAll.addEventListener('change', function() { document.querySelectorAll('.gh-entry-cb').forEach(cb => cb.checked = selAll.checked); }); }
    if(delBtn) {
        delBtn.addEventListener('click', async function(e) {
            e.preventDefault();
            const cbs = document.querySelectorAll('.gh-entry-cb:checked');
            if(cbs.length === 0) return alert("Bitte wähle zuerst Kästchen aus!");
            if(!confirm(cbs.length + " Einträge wirklich löschen?")) return;
            delBtn.disabled = true; delBtn.innerText = "Lösche..."; delBtn.style.background = "#6c757d";
            let count = 0;
            for(let i=0; i<cbs.length; i++) {
                const link = cbs[i].closest('.db-reihe').querySelector('.delete-wrapper a');
                if(link && link.href) {
                    try { await fetch(link.href + "&confirm=1"); cbs[i].closest('.db-reihe').style.opacity = "0.2"; count++; } catch(err) {}
                }
            }
            alert(count + " Einträge gelöscht!"); location.reload();
        });
    }
});
</script>
<div id="gh-list-container">
```

Wiederholter Eintrag:
```HTML

<div class="db-reihe gh-entry-row" style="border:1px solid #ccc; margin-bottom:5px; padding:10px; background:#fff; border-radius:5px; display:flex; justify-content:space-between; align-items:center;">
    <div style="display:flex; align-items:center;">
        <input type="checkbox" class="gh-entry-cb" style="transform: scale(1.5); margin-right:15px; margin-left:5px;">
        <span style="display:inline-block; width:75px; font-weight:bold; color:#005a9c; background:#e9ecef; text-align:center; border-radius:4px; padding:2px;">[[Typ]]</span>
        <strong style="margin-left:10px;">[[RundenID]]</strong>
        <span style="color:#555; margin-left:10px;">[[Status]]</span>
        <span class="db-userpicture" style="margin-left:15px; margin-right:5px;">##userpicture##</span>
        <span style="color:#888; font-style:italic;">[[SpielerName]]</span>
    </div>
    <div style="display:flex; gap:15px; align-items:center;">
        <span class="db-edit">##edit##</span> <span class="delete-wrapper">##delete##</span>
    </div>
    <div style="display:none;" class="gh-hidden-data">
        <span class="db-typ">[[Typ]]</span>
        <span class="db-rundenid">[[RundenID]]</span>
        <span class="db-text">[[Text]]</span>
        <span class="db-opta">[[OptA]]</span>
        <span class="db-optb">[[OptB]]</span>
        <span class="db-optc">[[OptC]]</span>
        <span class="db-optd">[[OptD]]</span>
        <span class="db-wert">[[Wert]]</span>
        <span class="db-zeitstempel">[[Zeitstempel]]</span>
        <span class="db-status">[[Status]]</span>
        <span class="db-spielername">[[SpielerName]]</span>
        <span class="db-bildurl">[[BildURL]]</span>
    </div>
</div>
```

###Fußzeile:
```HTML

</div>
<div style="text-align:center; padding:10px; color:#888; font-size:0.9rem;">BY-Catch Quiz Live-Datenbank</div>
```
###PRESET II: VORLAGEN FÜR DIE SCHATZKISTENDATENBANK (ARCHIV)
###Vorlage für Einträge hinzufügen:

```HTML
<div class="container-fluid bycatch-add-wrapper p-4" style="background-color: #f8f9fa !important; border-radius: 12px; color: #333; border: 1px solid #dee2e6;">
    <h2 class="text-center mb-4" style="color: #005a9c; font-weight: 800; letter-spacing: 1px;">🥨 NEUES BY-CATCH QUIZ SCHMIEDEN</h2>

    <div class="card mb-4" style="background-color: #fff !important; border: 1px solid #005a9c !important;">
        <div class="card-body">
            <h5 class="card-title" style="color: #005a9c;"><span class="badge me-2" style="background-color: #005a9c; color: #fff;">1</span> Thema festlegen</h5>
            <div class="moodle-field-wrapper mt-3">
                [[Thema]]
            </div>
            <small class="text-muted">z.B. "Steuerlehre - Grundlagen" oder "Wirtschaftskunde"</small>
        </div>
    </div>

    <div class="card mb-4" style="background-color: #e7f3ff !important; border: 1px solid #0dcaf0 !important; border-left: 5px solid #0dcaf0 !important;">
        <div class="card-body">
            <h5 style="color: #055160 !important; font-weight: bold;">🤖 KI-REZEPT (ZUM KOPIEREN)</h5>
            <p id="promptText" style="font-family: sans-serif; color: #055160; font-size: 0.95rem; background: rgba(255,255,255,0.5); padding: 15px; border-radius: 8px; border: 1px dashed #0dcaf0;">
                Erstelle 5 Multiple-Choice-Fragen zum Thema <strong>[THEMA]</strong>. Regeln: 1. Keine Kopfzeile, kein Markdown. 2. Trenne Spalten mit "|". 3. Spalten: RundenID (z.B. Q1) | Frage | Antwort A | Antwort B | Antwort C | Antwort D | Lösung (z.B. A,C) | Zeit (immer 20). 4. Bei Wahr/Falsch setze bei C und D ein "-".
            </p>
            <button type="button" class="btn w-100 fw-bold shadow-sm" style="background-color: #0dcaf0; color: #fff;" onclick="copyByCatchPrompt('promptText')">PROMPT KOPIEREN 📋</button>
        </div>
    </div>

    <div class="card mb-4" style="background-color: #fff !important; border: 1px solid #26890c !important;">
        <div class="card-body">
            <h5 class="card-title" style="color: #26890c;"><span class="badge me-2" style="background-color: #26890c; color: #fff;">2</span> KI-Output einfügen</h5>
            <div class="moodle-field-wrapper mt-3">
                [[Fragen_Input]]
            </div>
            <small class="text-muted">Füge hier den generierten Text (ID | Frage | A | B | C | D | Lösung | Zeit) ein.</small>
        </div>
    </div>

    <div class="d-flex justify-content-center gap-3 mt-4">
        ##save## ##cancel##
    </div>
</div>
<script>
function copyByCatchPrompt(elementId) {
    const text = document.getElementById(elementId).innerText;
    navigator.clipboard.writeText(text);
    alert("BY-Catch Prompt kopiert!");
}
</script>
```

### Einzelansicht:
```HTML

<div class="container py-4" style="max-width: 800px;">
    <div class="mb-3">##list##</div>
    
    <div class="card shadow-lg" style="border-radius: 15px; border: none; border-top: 8px solid #005a9c;">
        <div class="card-header bg-white p-4">
            <small class="text-uppercase text-muted fw-bold">Quiz Vorlage</small>
            <h2 style="color: #005a9c; font-weight: 800;">[[Thema]]</h2>
        </div>
        <div class="card-body p-4">
            <h5 class="fw-bold">Fragen-Daten:</h5>
            <div id="fullContent" style="background: #212529; color: #00ff00; padding: 20px; border-radius: 10px; font-family: monospace; white-space: pre-wrap;">[[Fragen_Input]]</div>
            
            <button class="btn btn-lg w-100 mt-4 fw-bold" style="background: #26890c; color: white;" onclick="navigator.clipboard.writeText(document.getElementById('fullContent').innerText); alert('Kopiert!')">
                ALLES KOPIEREN 📋
            </button>
        </div>
        <div class="card-footer bg-light d-flex justify-content-end">
            ##edit## ##delete##
        </div>
    </div>
</div>
```

### Listenansicht:

Kopfzeile:
```HTML

<div class="container-fluid bycatch-list-wrapper p-4" style="background-color: #f0f2f5; min-height: 100vh;">
    <h1 class="text-center mb-4" style="color: #005a9c; font-weight: 900; text-transform: uppercase;">🥨 BY-CATCH ARCHIV</h1>

    <div class="row mb-4 justify-content-center">
        <div class="col-md-8">
            <input type="text" id="bycatchSearch" class="form-control shadow-sm" style="border-radius: 25px; border: 2px solid #005a9c; padding: 12px 20px;" placeholder="🔍 Quiz-Thema suchen..." onkeyup="filterByCatchCards()">
        </div>
    </div>

    <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4" id="bycatchGrid">
```
###Wiederholter Eintrag:
```HTML

<div class="col bycatch-card-item" data-search="[[Thema]]">
    <div class="card h-100 shadow-sm" style="border: none; border-top: 5px solid #005a9c; border-radius: 10px; overflow: hidden; background: white;">
        <div class="card-body d-flex flex-column">
            <div class="d-flex justify-content-between align-items-start mb-2">
                <h4 style="color: #005a9c; font-weight: bold; margin: 0; font-size: 1.2rem;">[[Thema]]</h4>
                <div class="moodle-mini-tools text-muted">
                    ##edit## ##delete##
                </div>
            </div>
            
            <div class="preview-box flex-grow-1 my-3" style="background: #f8f9fa; border: 1px solid #eee; padding: 10px; border-radius: 8px; max-height: 120px; overflow: hidden; font-size: 0.85rem; color: #666;">
                [[Fragen_Input]]
            </div>

            <div class="d-grid gap-2">
                <button type="button" class="btn fw-bold" style="background-color: #26890c; color: white; border-radius: 8px;" onclick="copyByCatchData(this)">
                    DATEN KOPIEREN 🚀
                </button>
                <div class="text-center">
                    ##more##
                </div>
            </div>
        </div>
    </div>
</div>
```
### Fußzeile:
```HTML

     </div> 
</div>

<script>
function filterByCatchCards() {
    const query = document.getElementById('bycatchSearch').value.toLowerCase();
    const cards = document.querySelectorAll('.bycatch-card-item');
    cards.forEach(card => {
        const content = card.getAttribute('data-search').toLowerCase();
        card.style.display = content.includes(query) ? "block" : "none";
    });
}

function copyByCatchData(btn) {
    const text = btn.closest('.card-body').querySelector('.preview-box').innerText;
    navigator.clipboard.writeText(text);
    alert("Quiz-Daten kopiert! Füge sie jetzt im Gamemaster-Import ein.");
}
</script>
```
