# Riepilogo della sessione di progettazione — 16 aprile 2026
## Pilota di automazione granulometrica — GeoLabor s.a.s.

---

## Di cosa si tratta

Oggi abbiamo completato la fase di progettazione del sistema di automazione per
gli ensayos granulometrici di GeoLabor. Prima di scrivere una sola riga di codice,
il progetto è stato analizzato da tre intelligenze artificiali diverse in modo
indipendente — DeepSeek, Gemini e Claude — per identificare rischi nascosti,
validare le decisioni di architettura e garantire che il sistema sia solido dal
punto di vista tecnico, legale e operativo.

Il risultato è un progetto completamente documentato, con ogni decisione
giustificata e ogni rischio identificato e mitigato. Quello che segue è un
riepilogo di tutto ciò che è stato considerato.

---

## Il problema che risolve

Il processo attuale richiede che il tecnico trascriva manualmente i pesi trattenuti
da ogni setaccio dalla minuta scritta a mano a un foglio Excel. Questo processo
consuma tempo ripetitivo e introduce un rischio reale di errore umano in dati che
vanno a rapporti tecnici ufficiali.

Il sistema proposto automatizza questa trascrizione: il tecnico scansiona la minuta,
la carica in un'interfaccia web locale, e il sistema estrae i dati tramite
intelligenza artificiale visiva, li propone per la revisione, e genera
automaticamente il file Excel corretto.

---

## Cosa abbiamo scoperto durante l'analisi

La scoperta più importante è emersa dall'analisi dei file Excel reali del
laboratorio: non esiste un unico modello di foglio, ma almeno tre varianti con
logiche di calcolo fondamentalmente diverse. Nella variante con quartatura, i pesi
non si inseriscono direttamente nel Foglio2 come si pensava inizialmente, ma nel
Foglio4, dove le formule di Excel gestiscono il riscalaggio automatico. Se il
sistema avesse scritto nella posizione sbagliata, avrebbe prodotto percentuali
sistematicamente errate in modo silenzioso — il tipo di errore più pericoloso
perché sembra corretto.

La seconda scoperta importante, identificata dalla revisione dei rischi legali,
riguarda la catena di custodia delle prove. In geotecnica, i dati di un saggio
granulometrico possono diventare prove in una perizia se si verifica un cedimento
strutturale successivo in un'opera dove quei dati sono stati utilizzati. Se il
sistema elabora l'immagine, il tecnico conferma i dati, viene generato il file
Excel, ma l'immagine originale non viene conservata e collegata al file, si rompe
la catena di custodia. Il foglio Excel esiste, ma è stato prodotto da un sistema
automatizzato che potrebbe aver commesso un errore. Senza l'immagine originale
collegata, non è possibile dimostrare che il file Excel è fedele alla minuta
manoscritta.

---

## Le decisioni di progettazione prese

**Selettore manuale del tipo di prova.** Invece di far indovinare al sistema il
tipo di saggio dall'immagine — il che introdurrebbe un rischio alto di
classificazione errata — l'interfaccia presenta un menu a tendina dove il tecnico
sceglie il tipo prima di caricare la scansione. Questa scelta è scalabile: aggiungere
un nuovo tipo di saggio in futuro richiede solo aggiungere un'opzione al menu e
fornire il modello Excel corrispondente, senza modificare la logica centrale del
sistema.

**Archivio delle prove con identificatore univoco.** Ogni immagine elaborata viene
salvata sul server con un nome determinístico che include un identificatore unico
(UUID), la data e il tipo di prova. Questo identificatore viene anche scritto in
una cella nascosta del file Excel generato. Il database registra separatamente i
dati estratti dall'intelligenza artificiale prima di qualsiasi modifica del tecnico,
e i dati confermati dopo la revisione. Questo permette di ricostruire in qualsiasi
momento futuro cosa ha estratto il sistema e cosa ha corretto il tecnico — due
momenti di evidenza distinti con rilevanza legale diversa.

**Validazione in due livelli obbligatori e sequenziali.** La schermata di conferma
non è solo visiva. Prima di tutto, il sistema calcola automaticamente la somma di
tutti i pesi registrati e la confronta con la massa netta secca che il tecnico
inserisce manualmente. Se la differenza supera l'1%, il sistema mostra un avviso
rosso e blocca la conferma finché il tecnico non risolve la discrepanza. Solo dopo
aver superato questa verifica algoritmica, il tecnico può dare l'approvazione
finale confrontando visivamente i valori con l'immagine originale della minuta.
Le due fasi sono complementari: l'algoritmo rileva ciò che l'occhio umano non vede
con affidabilità, cioè le discrepanze numeriche accumulate; il tecnico rileva ciò
che l'algoritmo non può determinare, ovvero gli errori di contesto e i valori
numericamente coerenti ma visivamente sbagliati.

**Triangolo DIN 18123 incluso nel pilota.** Il sistema genera automaticamente il
punto classificato nel triangolo DIN 18123, che attualmente il laboratorio produce
con un software esterno. La tecnica adottata è una soluzione elegante: il triangolo
viene disegnato una volta sola in un modello Excel come immagine di sfondo, con un
grafico scatter già configurato. Il sistema scrive solo i tre valori percentuali
(ghiaia, sabbia, fini) nelle celle di input; il grafico posiziona il punto
automaticamente all'apertura del file in Excel Desktop. Nota importante: il file
deve essere aperto con Microsoft Excel Desktop, non con visualizzatori web come
Google Drive, perché i grafici vengono aggiornati all'apertura del file, non
durante la scrittura programmatica.

**Perimetro del pilota gratuito definito.** Il pilota gratuito copre
esclusivamente la granulometria semplice (un solo intervallo di setacci, pesi nel
Foglio2) e il triangolo DIN 18123. Le varianti con sedimentazione,
i limiti di Atterberg, la classificazione USCS completa e l'integrazione con
KORN.LAB sono esplicitamente fuori dal perimetro del pilota e costituirebbero
un progetto separato. 

---

## La documentazione prodotta oggi

Oltre alla progettazione, oggi sono stati prodotti undici documenti di architettura
che accompagneranno il sistema per tutto il suo ciclo di vita. Tra questi, quattro
decisioni architetturali formali specifiche del progetto GeoLabor — le cosiddette
ADR (Architecture Decision Records) — che documentano ogni scelta significativa
con il suo contesto, le alternative considerate e le conseguenze accettate.

Tre di questi documenti sono pensati per essere consegnati al laboratorio insieme
al sistema: la decisione sull'architettura generale del pilota, la decisione
sull'archivio di prove e trazabilità, e la decisione sul sistema di validazione
in due livelli. Il quarto documento riguarda la scelta dello strumento tecnico
per la generazione del file Excel ed è a uso interno.

---

## Prossimi passi

Prima di scrivere codice, completare una tabella di mappatura
tecnica che specifica per ogni setaccio della variante semplice qual è la cella
esatta del Foglio2 dove va scritto il peso. Con questi due documenti completati,
la costruzione del sistema può iniziare senza ambiguità.

---

*Documento preparato il 16 aprile 2026*
