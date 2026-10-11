# Parameter_estimation

Comissionamento (estimação de parâmetros) e controle sensorless de um motor de indução acionado por um
inversor multinível **CHB-NPC**, simulado no **Altair PSIM 2026.1**.

## Sistema

- **Inversor:** CHB-NPC, 3 fases × 2 células por fase (ponte H com braços NPC 3L), 322 V por célula (fontes CC ideais).
- **Motor:** indução 200 kW, 690 V, 60 Hz, 4 polos, J = 5,93 kg·m² (máquina de 500 hp de Krause escalada em p.u.).
- **Controle:** bloco C do PSIM (`SCB1`), tarefa a cada 500 µs, PWM PD com portadora de 2 kHz, injeção de 3º harmônico
  (1/6), pulso mínimo de 10 µs, referência quantizada na grade do PWM (a tensão aplicada é conhecida exatamente).
- **Modelo da máquina:** Γ-invertido (Rs, Lσ, LM, RR).

## Simulações

| Arquivo | O que faz |
|---|---|
| `chb_npc_comissionamento_srivc.psimsch` | Comissionamento: ensaio parado (CC + PRBS no eixo α) com RLS online (só visualização) e **SRIVC** em lote no fim (float32, discretização exata, soma de Kahan); depois ensaio a vazio V/f para verificar LM |
| `chb_npc_comissionamento_rivc_recursivo.psimsch` | Comissionamento só com **RIVC recursivo** (pré-filtro fixo, modelo auxiliar atualizado a cada 50 ms), sem buffers |
| `chb_npc_foc_sensorless_hinkkanen.psimsch` | **FOC sensorless**: observador de fluxo de ordem reduzida com adaptação de Rs (Hinkkanen, Harnefors & Luomi, IEEE TPEL 2010), controle de corrente IMC, PI de velocidade, carga ativa `MLOAD_EXT` |
| `chb_npc_esquematico_original.psimsch` | Esquemático de partida |

O código de controle de cada simulação está no bloco C `SCB1` do respectivo esquemático.
Simulação com passo de 1 µs, tempo total de 5 s e print step 50.

## Referências principais

- P. C. Young, *Recursive Estimation and Time-Series Analysis*, 2ª ed., Springer, 2011.
- H. Garnier, L. Wang (eds.), *Identification of Continuous-time Models from Sampled Data*, Springer, 2008.
- M. Hinkkanen, L. Harnefors, J. Luomi, "Reduced-Order Flux Observers With Stator-Resistance Adaptation for
  Speed-Sensorless Induction Motor Drives", *IEEE Trans. Power Electron.*, 25(5), 2010.
- L. Harnefors, H.-P. Nee, "Model-based current control of AC machines using the internal model control method",
  *IEEE Trans. Ind. Appl.*, 34(1), 1998.
