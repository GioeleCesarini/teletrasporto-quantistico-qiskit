# Teletrasporto quantistico con Qiskit

Codice della tesi triennale in Fisica (Università degli Studi di Genova, a.a. 2025/2026): implementazione del teletrasporto quantistico con Qiskit, simulazione ideale ed esecuzione su hardware IBM Quantum.

## Struttura

| Cartella | Contenuto | Tesi |
|---|---|---|
| `simulazione/` | Costruzione del circuito, verifica sul vettore di stato e verifica statistica su simulatore ideale | Capitolo 4 |
| `hardware/` | Esecuzione dello stesso circuito su un processore IBM Quantum e confronto con la simulazione | Capitolo 5 |


## Requisiti

- Python 3.10.12
- Qiskit 2.5.2, Qiskit Aer 0.17.2
- NumPy, Matplotlib, pylatexenc, Jupyter

## Esecuzione

    python -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt
    jupyter notebook locale_completo.ipynb

Poi eseguire tutte le celle in ordine (Kernel → Restart & Run All). Le figure vengono salvate nella cartella `immagini/`.

## Riproducibilità

I semi sono fissati (`SEME_STATO = SEME_SIMULATORE = 42`) e ogni circuito viene eseguito `N_SHOTS = 1024` volte. Con questi valori si ottengono:

| (m_C, m_A) | Correzione di Bob | Conteggi | Errori |
|---|---|---|---|
| (0, 0) | I | 239 | 0 |
| (0, 1) | X | 278 | 0 |
| (1, 0) | Z | 254 | 0 |
| (1, 1) | ZX | 253 | 0 |

## Fonti

Il protocollo da me implementato segue il più possibile il capitolo sul teletrasporto del [Qiskit Textbook](https://github.com/Qiskit/textbook/blob/main/notebooks/ch-algorithms/teleportation.ipynb), adattato alla versione attuale di Qiskit e alla notazione della tesi (qubit C, A, B e bit classici m_C, m_A).

Nota sulle fonti: la preparazione dello stato usa rotazioni RY/RZ invece di `Initialize`, perché con Qiskit 2.5.2 + Qiskit-aer 0.17.2 la combinazione `Initialize` + `gates_to_uncompute()` causava il crash del kernel.

## Uso di strumenti di IA

L'aggiornamento del codice alle API correnti di Qiskit e la correzione del problema di compatibilità con l'ambiente locale sono stati sviluppati con l'assistenza di un modello linguistico (Claude, Anthropic), sotto la supervisione e la verifica dell'autore.
