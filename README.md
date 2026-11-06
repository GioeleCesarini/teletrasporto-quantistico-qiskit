# Teletrasporto quantistico con Qiskit

![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![Qiskit](https://img.shields.io/badge/Qiskit-2.5.2-6929C4?logo=qiskit&logoColor=white)
![IBM Quantum](https://img.shields.io/badge/IBM_Quantum-hardware_reale-052FAD?logo=ibm&logoColor=white)
![Licenza](https://img.shields.io/badge/licenza-MIT-green)

Implementazione del protocollo di teletrasporto quantistico con Qiskit, verificata su
simulatore ideale ed eseguita su un processore quantistico reale di IBM.
Il repository contiene il codice e la tesi triennale in cui il lavoro è descritto.

> **Teletrasporto Quantistico: simulazione e realizzazione su quantum computer IBM**
> Prova finale, Corso di Laurea in Fisica, Università degli Studi di Genova, a.a. 2025/2026
> Candidato: Gioele Cesarini · Relatrice: Prof.ssa Marina Putti

📄 **[Leggi la tesi (PDF)](tesi/Cesarini_Gioele_tesi.pdf)**

---

## Il protocollo

Il teletrasporto quantistico trasferisce lo stato di un qubit da Alice a Bob usando
una coppia di qubit entangled e due bit classici, senza spostare fisicamente il qubit.
Nella notazione della tesi:

1. Alice possiede il qubit **C**, che contiene lo stato da teletrasportare, e il qubit **A**; Bob possiede il qubit **B**.
2. I qubit **A** e **B** vengono preparati in uno stato di Bell.
3. Alice misura **C** e **A** nella base di Bell e ottiene i bit classici $m_C$ e $m_A$.
4. Bob riceve i due bit e applica al qubit **B** la correzione giusta: a quel punto **B** si trova nello stato iniziale di **C**.

## Contenuto del repository

| Cartella | Contenuto | Tesi |
|---|---|---|
| `simulazione/` | Costruzione del circuito, verifica sul vettore di stato e verifica statistica su simulatore ideale | Capitolo 4 |
| `hardware/` | Esecuzione dello stesso circuito su un processore IBM Quantum e confronto con la simulazione | Capitolo 5 |
| `tesi/` | La tesi completa in PDF | |

## Risultati

### Simulatore ideale

I semi sono fissati (`SEME_STATO = SEME_SIMULATORE = 42`) e ogni circuito viene
eseguito 1024 volte, quindi i risultati sono riproducibili esattamente:

| $(m_C, m_A)$ | Correzione di Bob | Conteggi | Errori |
|:---:|:---:|:---:|:---:|
| (0, 0) | $I$ | 239 | 0 |
| (0, 1) | $X$ | 278 | 0 |
| (1, 0) | $Z$ | 254 | 0 |
| (1, 1) | $ZX$ | 253 | 0 |

Tutti e quattro gli esiti della misura di Alice compaiono con frequenze compatibili
con 1/4, e in nessun caso lo stato ricevuto da Bob differisce da quello di partenza.

### Hardware IBM Quantum

> [!NOTE]
> I risultati su hardware reale non sono riproducibili esattamente: il rumore del
> processore (decoerenza, errori di gate e di lettura) varia nel tempo e da una
> calibrazione all'altra. L'analisi completa e il confronto con la simulazione
> sono nel capitolo 5 della tesi.

## Come eseguire il codice

````bash
git clone https://github.com/GioeleCesarini/teletrasporto-quantistico-qiskit.git
cd teletrasporto-quantistico-qiskit
pip install -r requirements.txt
jupyter notebook
````

I notebook in `simulazione/` girano in locale senza account. Per quelli in `hardware/`
serve un account IBM Quantum: salva il tuo token una sola volta sul tuo computer con
`QiskitRuntimeService.save_account(...)`, come indicato nella documentazione IBM,
e non scriverlo mai direttamente nei notebook.

### Requisiti

- Python 3.10.12
- Qiskit 2.5.2, Qiskit Aer 0.17.2, Qiskit IBM Runtime 0.49.0
- NumPy, Matplotlib, pylatexenc, Jupyter

## Differenze rispetto al Qiskit Textbook

Il protocollo segue il più possibile il capitolo sul teletrasporto del
[Qiskit Textbook](https://github.com/Qiskit/textbook/blob/main/notebooks/ch-algorithms/teleportation.ipynb),
aggiornato alla versione attuale di Qiskit e adattato alla notazione della tesi
(qubit C, A, B e bit classici $m_C$, $m_A$). Le modifiche principali sono:

1. **Preparazione dello stato**: si usano rotazioni `RY`/`RZ` invece di `Initialize`, perché con Qiskit 2.5.2 e Qiskit Aer 0.17.2 la combinazione `Initialize` + `gates_to_uncompute()` causava il crash del kernel nell'ambiente locale.
2. **Accesso a IBM Quantum**: il pacchetto `qiskit-ibmq-provider`, non più disponibile, è sostituito da `qiskit-ibm-runtime`.
3. **Scelta del backend**: `least_busy` di `qiskit.providers.ibmq` è sostituito da `service.least_busy(operational=True, simulator=False, min_num_qubits=3)`.
4. **Monitoraggio dei job**: `qiskit.tools` è stato rimosso; bastano `job.status()` e `job.result()`.
5. **Esecuzione**: i circuiti vengono eseguiti con la primitiva `Sampler`.
6. **Versioni**: `qiskit.tools.jupyter` non esiste più; le versioni dei pacchetti vengono stampate e salvate nel file dei risultati.

## Uso di strumenti di IA

L'aggiornamento del codice alle API correnti di Qiskit e la correzione del problema
di compatibilità con l'ambiente locale sono stati sviluppati con l'assistenza di un
modello linguistico (Claude, Anthropic), sotto la supervisione e la verifica dell'autore.

## Come citare

````bibtex
@thesis{cesarini2026teletrasporto,
  author      = {Cesarini, Gioele},
  title       = {Teletrasporto Quantistico: simulazione e realizzazione su quantum computer IBM},
  type        = {Tesi di laurea triennale},
  institution = {Università degli Studi di Genova},
  year        = {2026}
}
````

## Licenza

- **Codice**: licenza [MIT](LICENSE). Puoi usarlo, modificarlo e riutilizzarlo liberamente, anche in altri progetti, mantenendo la nota di copyright.
- **Tesi** (`tesi/`): © 2026 Gioele Cesarini, licenza [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/). Puoi leggerla, condividerla e citarla, ma non modificarla né usarla a scopo commerciale.

## Autore

**Gioele Cesarini**, [GitHub](https://github.com/GioeleCesarini)
