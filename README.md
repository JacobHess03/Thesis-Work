# Thesis Work: Rilevamento Anomalie nei Registri di Sistema attraverso il Deep Learning
**Nota:** Il lavoro di Tesi è stato di studio e modifica ed adattabilità del modello degli autori, valutando a seguito delle modifiche i risultati.
Questo progetto implementa DeepLog, un modello di rete neurale profonda basato su LSTM (Long Short-Term Memory) per il rilevamento delle anomalie nei registri di sistema. L'approccio DeepLog tratta i log di sistema come sequenze di linguaggio naturale, apprendendo automaticamente i pattern durante il normale funzionamento e segnalando anomalie quando i nuovi log si discostano dai pattern appresi.

---

## Descrizione

I registri di sistema sono una risorsa fondamentale per il debug e il monitoraggio delle prestazioni di un sistema informatico. In questo lavoro, DeepLog:
- Apprende i pattern dei log in condizioni normali.
- Rileva anomalie quando i log non seguono i pattern appresi.
- Permette un aggiornamento incrementale e online del modello per adattarsi ai nuovi pattern emergenti.
- Genera flussi di lavoro dai log per diagnosticare efficacemente le anomalie e analizzare le cause.

Il modello è implementato in **PyTorch** e include funzionalità per:
- Caricamento e pre-elaborazione dei log (da file locali o da un bucket S3).
- Addestramento del modello LSTM in modalità distribuita (supporta training multi-GPU/multi-nodo).
- Salvataggio e caricamento del modello addestrato.
- Funzioni per serializzare input e output per un deployment, ad esempio su Amazon SageMaker.

---

## Struttura del Progetto

- **Importazioni e configurazione**:  
  Gestione dei log e configurazione del livello di logging per il debug.

- **Definizione del modello** (`class Model`):  
  Implementa un modello LSTM con strati lineari per il rilevamento degli eventi anomali nei log.

- **Classe Generate**:  
  Responsabile del caricamento dei log, che possono essere letti da file locali o da un bucket AWS S3.  
  - `generate()`: Converte le sequenze di log in dataset adatti all’addestramento.
  - `init_line()` e `readline()`: Gestiscono l’accesso ai log a seconda della modalità di esecuzione.

- **Data Loader e Funzioni ausiliarie**:  
  - `_get_train_data_loader()`: Prepara il DataLoader per il training.
  - `_average_gradients()`: Funzione ausiliaria per il training distribuito.

- **Funzioni di training e salvataggio**:
  - `train()`: Funzione principale per addestrare il modello.
  - `save_model()`: Salva il modello e le informazioni necessarie per il ripristino.
  - `model_fn()`: Funzione per il caricamento del modello in fase di inference.

- **Funzioni per l’inferenza**:
  - `input_fn()`: Deserializza i dati di input.
  - `predict_fn()`: Predice il prossimo evento e determina se è anomalo.
  - `output_fn()`: Serializza l’output della predizione.

- **Script principale**:  
  Utilizza `argparse` per definire i parametri (batch size, numero di epoche, dimensione della finestra, ecc.) e avvia il training del modello.

---

## Requisiti

- **Python 3.7+**
- **PyTorch**
- **boto3** (se si utilizzano log da AWS S3)
- **Argparse** e altri moduli standard di Python


---

## Istruzioni per l'Esecuzione

1. **Configurare l'ambiente**:  
   Assicurarsi che tutte le dipendenze siano installate e che, se si utilizzano log da AWS S3, le credenziali siano correttamente configurate.

2. **Avviare l'addestramento**:  
   Eseguire lo script principale passando i parametri desiderati. Ad esempio:
   ```bash
   python deep_log.py --batch-size 64 --epochs 50 --window-size 10 --input-size 1 --hidden-size 64 --num-layers 2 --num-classes <NUM_CLASSES> --num-candidates <NUM_CANDIDATES> --local True

