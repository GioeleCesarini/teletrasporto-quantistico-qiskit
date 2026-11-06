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
4. Bob riceve i due bit e applica al qubit **B** la correzione $Z^{m_C} X^{m_A}$: a quel punto **B** si trova nello stato iniziale di **C**.

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
git clone https://github.com/GioeleCesarini/teletrasporto-quantistico-qiskit
