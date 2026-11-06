# Teletrasporto quantistico su hardware IBM Quantum

Esecuzione del protocollo di teletrasporto su un processore quantistico reale di IBM
e confronto con la simulazione ideale. Corrisponde al **Capitolo 5** della
[tesi](../tesi/Tesi_Cesarini_Teletrasporto.pdf).

← [Torna al repository principale](../README.md) · [Simulazione ideale](../simulazione/)

## Notebook

| File | Contenuto |
|---|---|
| `IBM.ipynb` | Circuito con misura differita, traspilazione per il processore, lettura delle calibrazioni, esecuzione su hardware e analisi dei risultati |

I primi passi (preparazione dello stato, circuito e verifica su simulatore) sono
identici a quelli della [simulazione](../simulazione/), con lo stesso stato
$\lvert\psi\rangle$ (seme 42).

## Dalla simulazione all'hardware: la misura differita

Nella simulazione Bob applica le correzioni in base ai bit classici misurati da Alice
(operazioni condizionate con `if_test`). Sull'hardware si usa invece il **principio
della misura differita**, come nel Qiskit Textbook: le correzioni classiche vengono
sostituite da porte quantistiche controllate, e nessun bit classico viene trasmesso.

| | Simulazione | Hardware |
|---|---|---|
| Correzione $X$ | $X$ su B se $m_A = 1$ | CNOT con controllo A e bersaglio B |
| Correzione $Z$ | $Z$ su B se $m_C = 1$ | CZ tra C e B |
| Misure | C, A e B | solo B |

Alla fine si applica a B l'inverso della preparazione, $R_Z(-\varphi)$ e poi
$R_Y(-\theta)$, e lo si misura: se il teletrasporto è riuscito, l'esito è **0**.

## Il processore

| | |
|---|---|
| **Processore** | `ibm_marrakesh` (156 qubit), scelto con `service.least_busy(...)` |
| **Qubit fisici usati** | 5, 6, 7 |
| **Layout** (q0, q1, q2) | iniziale [7, 6, 5], finale [7, 5, 6] |
| **Circuito traspilato** | 18 `rz`, 16 `sx`, 5 `cz` · profondità 30 |

La traspilazione (`optimization_level=3`, `seed_transpiler=42`) traduce il circuito
nelle porte native del processore (`rz`, `sx`, `cz`), sceglie i qubit fisici e
rispetta la connettività del chip: tutti e tre i qubit interagiscono tra loro, ma sul
chip 5–6–7 sono collegati in fila, quindi durante il circuito i qubit logici vengono
permutati (il layout finale è diverso da quello iniziale).

### Calibrazioni al momento dell'esecuzione

Lette dall'API di IBM subito prima dell'invio del job:

| Qubit fisico | $T_1$ [μs] | $T_2$ [μs] | Errore di lettura | Errore `sx` |
|:---:|:---:|:---:|:---:|:---:|
| 5 | 283 | 186 | 0.79% | 0.022% |
| 6 | 272 | 189 | 0.33% | 0.020% |
| 7 | 258 | 198 | 1.51% | 0.069% |

| Porta a due qubit | Errore | Durata |
|:---:|:---:|:---:|
| CZ (5, 6) | 0.136% | 68 ns |
| CZ (6, 7) | 0.224% | 68 ns |

## Risultati

Job `datsf4dvr3kc73em0ip0`, 1024 shot:

| Esito della misura di B | Conteggi |
|:---:|:---:|
| 0 (teletrasporto riuscito) | 990 |
| 1 (errore) | 34 |

**Tasso di errore sperimentale: 3.32%**, contro lo 0% della simulazione ideale.
La differenza è dovuta al rumore del processore reale: errori delle porte (in
particolare quelle a due qubit), decoerenza ed errori di lettura. L'analisi completa
è nel capitolo 5 della tesi.

> [!NOTE]
> Rieseguendo il notebook i risultati non saranno identici: il processore scelto,
> i qubit fisici e le calibrazioni cambiano nel tempo.

## Dati salvati

Per rendere l'esperimento documentabile, il notebook salva tutto ciò che lo descrive:

| Percorso | Contenuto |
|---|---|
| `risultati_reali/esperimento_<JOB_ID>.json` | Processore, versioni dei pacchetti, stato, layout, calibrazioni, conteggi, tasso di errore e metriche del job |
| `risultati_reali/circuiti_<JOB_ID>.qpy` | Circuito logico e traspilato, rileggibili con `qpy.load` |
| `risultati_reali/proprieta_complete_ibm_marrakesh.json` | Proprietà di tutti i qubit del processore |
| `immagini_reali/circuito_misura_differita` | Circuito con misura differita |
| `immagini_reali/circuito_traspilato` | Circuito traspilato sui qubit fisici |
| `immagini_reali/istogramma_reale` | Istogramma dei risultati su hardware |

## Esecuzione

Serve un account IBM Quantum. Salva le credenziali **una sola volta** sul tuo computer
(la cella "UNA TANTUM" nel notebook, da decommentare) e non lasciare mai il token
scritto nel notebook:

```python
from qiskit_ibm_runtime import QiskitRuntimeService
QiskitRuntimeService.save_account(channel="ibm_quantum_platform",
                                  token="IL_TUO_TOKEN", instance="LA_TUA_ISTANZA",
                                  set_as_default=True)
```

Poi:

```bash
pip install -r ../requirements.txt
jupyter notebook IBM.ipynb
```

Il job può restare in coda a lungo. Se nel frattempo il kernel si riavvia, riesegui le
celle fino al NEW STEP 12 e scrivi l'ID del job in `JOB_ID_DA_RECUPERARE`: i dati
salvati all'invio vengono ricaricati automaticamente.
