# Guida Tecnica: Animazione Caduta Pillole

## Descrizione Generale

Animazione di caduta e accumulo di elementi SVG (`#past`, `#pillola`, `#pillola2`, `#xanax`) all'interno di un contenitore (`#poster`). Gli elementi cadono dall'alto, si accumulano sul fondo riempiendo progressivamente l'area, e dopo un tempo prestabilito (30 secondi) cadono tutti verso il basso con effetto cascata e scompaiono. Segue una pausa di 3 secondi, poi il ciclo ricomincia automaticamente.

## Struttura HTML Richiesta

```html
<div id="poster">
    <!-- Contenitore dove cadono le pillole -->
    <div id="rain-layer"></div>
</div>
```

- `#poster`: Contenitore principale con `position: relative`, `overflow: hidden`.
- `#rain-layer`: Layer interno con `position: absolute`, `top: 0`, `left: 0`, `width: 100%`, `height: 100%`, `overflow: hidden`.

## Stili CSS Essenziali

```css
/* Layer pioggia */
#rain-layer {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    pointer-events: none;
    z-index: 10;
}

/* Stato base pillola (prima della caduta) */
.pill {
    position: absolute;
    top: -120px; /* Fuori dallo schermo, sopra */
    will-change: top, transform, opacity;
    transition: none; /* Nessuna transizione di default */
    transform: rotate(var(--start-rot, 0deg));
}

/* Stato caduta (durante l'animazione di discesa) */
.pill.falling {
    transition: top var(--dur) cubic-bezier(0.25, 0.1, 0.25, 1),
                transform var(--dur) cubic-bezier(0.25, 0.1, 0.25, 1);
    top: var(--end-top);     /* Posizione di atterraggio */
    transform: rotate(var(--end-rot)); /* Rotazione finale */
}

/* Stato flush (caduta verso il basso fuori schermo) */
.pill.flushing {
    transition: top 2s cubic-bezier(0.55, 0, 0.45, 1), opacity 1.5s ease-out;
    top: 120% !important;   /* Esce dal basso del poster */
    opacity: 0 !important;   /* Scompare gradualmente */
}
```

### Variabili CSS Usate

| Variabile | Descrizione | Valore tipico |
|-----------|-------------|---------------|
| `--dur` | Durata della caduta iniziale | `1s` - `2.5s` |
| `--end-top` | Posizione Y finale di atterraggio | calcolata in JS |
| `--start-rot` | Rotazione iniziale | `0deg` - `360deg` |
| `--end-rot` | Rotazione finale durante caduta | `startRot ± 360deg` |

## Logica JavaScript Completa

### Array delle Sorgenti

```javascript
const pills = ['SVG/past.svg', 'SVG/pillola.svg', 'SVG/pillola2.svg', 'SVG/xanax.svg'];
```

Questo array contiene i percorsi degli SVG. Può essere modificato per usare qualsiasi altro elemento.

### Stato dell'Animazione

```javascript
let isFlushing = false;   // true = in fase di svuotamento
let fillTimer = null;     // Timer del countdown 30s
let dropTimer = null;     // Timer della generazione pillole
let remainingTime = 30000; // Millisecondi rimanenti (30 secondi)
```

### Funzione `startFill()` - Riempimento

```javascript
function startFill() {
    isFlushing = false;
    remainingTime = 30000; // Resetta a 30 secondi
    
    // Genera una nuova pillola ogni 20ms (50 al secondo)
    dropTimer = setInterval(function() {
        if (isFlushing) return;
        
        const el = document.createElement('img');
        el.src = pills[Math.floor(Math.random() * pills.length)];
        el.className = 'pill';
        
        // POSIZIONE X: copre TUTTA la larghezza, anche oltre i bordi (-15% a 115%)
        el.style.left = (-15 + Math.random() * 115) + '%';
        
        // DIMENSIONI: piccole (10% - 18%) per massima densita
        el.style.width = (10 + Math.random() * 8) + '%';
        
        const h = poster.clientHeight;
        
        // GROUND: sale progressivamente dal basso verso l'alto
        // timeProgress va da 0 (inizio) a 1 (fine dei 30 secondi)
        const timeProgress = 1 - (remainingTime / 30000);
        const currentGround = h * 0.95 * timeProgress; // Sale fino al 95% dell'altezza
        
        // DURATA CADUTA: 1 - 2.5 secondi (casuale per ogni pillola)
        const dur = 1 + Math.random() * 1.5;
        
        // POSIZIONE FINALE: ground + variazione casuale per effetto naturale
        const endTop = h - currentGround - (Math.random() * 25) + (Math.random() * 15);
        
        // ROTAZIONE
        const startRot = Math.random() * 360;
        const endRot = startRot + (Math.random() * 720 - 360);
        
        // Imposta variabili CSS
        el.style.setProperty('--dur', dur + 's');
        el.style.setProperty('--end-top', Math.max(endTop, h * 0.05) + 'px');
        el.style.setProperty('--start-rot', startRot + 'deg');
        el.style.setProperty('--end-rot', endRot + 'deg');
        
        rain.appendChild(el);
        
        // FORZA REFLOW: necessario per attivare la transizione CSS
        el.offsetHeight;
        
        // Avvia la caduta nel prossimo frame
        requestAnimationFrame(function() {
            el.classList.add('falling');
        });
        
    }, 20); // 20ms = 50 pillole al secondo
    
    // Timer di countdown: dopo 30 secondi attiva il flush
    fillTimer = setInterval(function() {
        remainingTime -= 100;
        if (remainingTime <= 0) {
            clearInterval(fillTimer);
            clearInterval(dropTimer);
            flushPills();
        }
    }, 100);
}
```

### Funzione `flushPills()` - Svuotamento a Cascata

```javascript
function flushPills() {
    isFlushing = true;
    const allPills = Array.from(rain.children);
    
    // Effetto CASCATA: ogni pillola inizia a cadere con un ritardo progressivo
    allPills.forEach(function(pill, index) {
        setTimeout(function() {
            // Rimuovi la classe falling (ferma la posizione attuale)
            pill.classList.remove('falling');
            // Aggiungi flushing (inizia la caduta verso il basso)
            pill.classList.add('flushing');
        }, index * 12); // 12ms di ritardo tra ogni pillola
    });
    
    // Calcola il tempo totale necessario
    const cascadeTime = allPills.length * 12 + 2500; // Ritardi + durata transizione
    
    // Dopo che tutte sono cadute:
    setTimeout(function() {
        rain.innerHTML = '';      // Svuota completamente il DOM
        isFlushing = false;
        
        // PAUSA DI 3 SECONDI prima di ricominciare
        setTimeout(startFill, 3000);
    }, cascadeTime);
}
```

### Avvio

```javascript
startFill();
```

## Parametri Configurabili

| Parametro | Valore Attuale | Effetto | Come Modificarlo |
|-----------|---------------|---------|------------------|
| `30000` | 30 secondi | Durata riempimento | Cambia `remainingTime` |
| `20` | 20ms | Intervallo generazione | Modifica nel `setInterval` di `startFill` |
| `10-18%` | 10% + random 8% | Dimensione pillole | Modifica `el.style.width` |
| `-15 / 115` | Posizione X | Copertura laterale | Modifica `el.style.left` |
| `0.95` | 95% | Altezza massima riempimento | Modifica `h * 0.95` |
| `1-2.5s` | 1 + random 1.5 | Velocita caduta | Modifica `dur` |
| `12ms` | Ritardo cascata | Ritardo flush | Modifica `index * 12` |
| `3000` | 3 secondi | Pausa tra cicli | Modifica `setTimeout(startFill, 3000)` |

## Come Applicare ad Altri Elementi

### 1. Cambiare le sorgenti

```javascript
const pills = ['percorso/tuo-elemento1.svg', 'percorso/tuo-elemento2.png', ...];
```

### 2. Cambiare il selettore del contenitore

```javascript
const rain = document.getElementById('id-tuo-layer');
const poster = document.getElementById('id-tuo-poster');
```

### 3. Regolare la densita

- **Piu denso**: Riduci l'intervallo del `dropTimer` (es. da 20ms a 10ms)
- **Meno denso**: Aumenta l'intervallo (es. da 20ms a 50ms)
- **Pillole piu grandi**: Aumenta la larghezza (es. 20-30%)
- **Pillole piu piccole**: Riduci la larghezza (es. 5-12%)

### 4. Regolare la durata del ciclo

```javascript
let remainingTime = 45000; // 45 secondi invece di 30
```

## Note Tecniche Importanti

1. **Forza Reflow (`el.offsetHeight`)**: Essenziale per attivare la transizione CSS. Senza questa linea, le pillole apparirebbero istantaneamente alla fine senza animazione.

2. **`requestAnimationFrame`**: Necessario per applicare la classe `falling` nel frame successivo, così il browser registra il cambio di stato e applica la transizione.

3. **`overflow: hidden` sul poster**: Critico per nascondere le pillole che escono dai bordi durante la caduta iniziale.

4. **Variabili CSS**: Permettono di avere durate e posizioni diverse per ogni pillola, controllate via JavaScript ma applicate via CSS.

5. **Sistema a Ground Progressivo**: Il ground sale dal basso verso l'alto in base al tempo trascorso, non al numero di pillole. Questo garantisce un riempimento uniforme indipendentemente dalla velocita della macchina.

## File di Riferimento nel Progetto

- **File animazione completa**: `index.html`
- **SVG usati**: `SVG/past.svg`, `SVG/pillola.svg`, `SVG/pillola2.svg`, `SVG/xanax.svg`
- **Questa guida**: `docs/animation-pills-guide.md`

---

*Documento creato per replicabilita dell'animazione di caduta e accumulo.*
