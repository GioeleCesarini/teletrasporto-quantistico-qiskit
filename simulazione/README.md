# Simulazione ideale del teletrasporto quantistico

Verifica del protocollo di teletrasporto su un simulatore ideale (senza rumore),
con Qiskit e Qiskit Aer. Corrisponde al **Capitolo 4** della [tesi](../tesi/Tesi_Cesarini_Teletrasporto.pdf).

← [Torna al repository principale](../README.md)

## Notebook

| File | Contenuto |
|---|---|
| `locale_completo.ipynb` | Preparazione dello stato, costruzione del circuito, verifica esatta sul vettore di stato e verifica statistica su 1024 ripetizioni |

## Notazione

| Qubit | Indice | Ruolo |
|:---:|:---:|---|
| **C** | `q0` | Qubit di Alice con lo stato $\lvert\psi\rangle$ da teletrasportare |
| **A** | `q1` | Qubit di Alice, metà della coppia entangled |
| **B** | `q2` | Qubit di Bob, metà della coppia entangled |

La coppia entangled $\lvert\Phi^+\rangle_{AB} = (\lvert 00\rangle + \lvert 11\rangle)/\sqrt{2}$
viene preparata da una terza parte, Telamon. I bit classici $m_C$ e $m_A$ sono gli
esiti della misura di Alice.

## Cosa fa il notebook

### 1. Stato da teletrasportare

Lo stato $\lvert\psi\rangle = \alpha\lvert 0\rangle + \beta\lvert 1\rangle$ è scelto a
caso con `random_statevector` (seme 42), ottenendo
$\alpha \approx 0.188 + 0.463i$ e $\beta \approx -0.642 + 0.581i$.
Dagli angoli sulla sfera di Bloch

$$\theta = 2\arccos\lvert\alpha\rvert, \qquad \varphi = \arg\beta - \arg\alpha$$

lo stato viene costruito sul qubit C con una rotazione $R_Y(\theta)$ seguita da $R_Z(\varphi)$.

### 2. Il circuito

1. **Preparazione**: $R_Y(\theta)$ e $R_Z(\varphi)$ su C.
2. **Entanglement**: $H$ su A e CNOT da A a B, che producono $\lvert\Phi^+\rangle_{AB}$.
3. **Misura di Bell**: CNOT da C ad A, $H$ su C, misura di C e A in $m_C$ e $m_A$.
4. **Correzione di Bob**: $X$ su B se $m_A = 1$, poi $Z$ se $m_C = 1$, tramite operazioni condizionate (`if_test`).

### 3. Verifica esatta (vettore di stato)

Il circuito viene eseguito una volta salvando il vettore di stato completo dopo la
misura e la correzione. La sfera di Bloch del qubit B coincide con quella di
$\lvert\psi\rangle$, qualunque sia l'esito casuale della misura di Alice.

### 4. Verifica statistica (1024 shot)

Dopo la correzione si applica a B l'inverso della preparazione, $R_Z(-\varphi)$ e poi
$R_Y(-\theta)$. Se il teletrasporto è riuscito, B torna in $\lvert 0\rangle$ e la
sua misura dà **sempre 0**. Ogni esito 1 sarebbe un errore.

## Risultati

Con `SEME_STATO = SEME_SIMULATORE = 42` e 1024 shot:

| $(m_C, m_A)$ | Correzione di Bob | Conteggi | Errori |
|:---:|:---:|:---:|:---:|
| (0, 0) | $I$ | 239 | 0 |
| (0, 1) | $X$ | 278 | 0 |
| (1, 0) | $Z$ | 254 | 0 |
| (1, 1) | $ZX$ | 253 | 0 |

**Tasso di errore: 0.000%**, come atteso su un simulatore senza rumore.

Gli esiti della misura di Alice sono compatibili con una distribuzione 50/50.
Per una binomiale con $n = 1024$ e $p = 1/2$ ci si aspetta $512 \pm 16$:

| Bit | Esiti "1" osservati | Scarto |
|:---:|:---:|:---:|
| $m_C$ | 507 / 1024 | −0.31 σ |
| $m_A$ | 531 / 1024 | +1.19 σ |

Entrambi gli scarti sono entro 2σ.

## Figure prodotte

Il notebook salva le figure nella cartella `immagini/`:

| File | Contenuto |
|---|---|
| `bloch_psi_iniziale` | Sfera di Bloch dello stato iniziale $\lvert\psi\rangle$ |
| `circuito_teletrasporto1` | Circuito del protocollo |
| `bloch_psi_tot` | Sfere di Bloch dei tre qubit dopo il protocollo |
| `circuito_teletrasporto2` | Circuito con l'inversione e la misura finale di verifica |
| `istogramma_conteggi` | Conteggi congiunti dei quattro esiti |
| `istogramma_marginali` | Distribuzioni marginali di $m_C$, $m_A$ e del risultato di Bob |

## Esecuzione

Non serve un account IBM: tutto gira in locale.

```bash
pip install -r ../requirements.txt
jupyter notebook locale_completo.ipynb
```

| Parametro | Valore | Significato |
|---|:---:|---|
| `SEME_STATO` | 42 | Seme per lo stato casuale da teletrasportare |
| `SEME_SIMULATORE` | 42 | Seme per gli esiti delle misure |
| `N_SHOTS` | 1024 | Numero di ripetizioni del circuito |

Cambiando i semi si ottengono uno stato e conteggi diversi, ma il tasso di errore
resta sempre 0%.
