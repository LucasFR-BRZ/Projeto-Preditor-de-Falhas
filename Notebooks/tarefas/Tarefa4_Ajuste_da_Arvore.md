# Diário da Tarefa 4 — Ajuste da árvore a partir do erro

**Período:** 05/10/2026 a 11/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:**  
**Scrum Master da tarefa:**  
**Repositório GitHub:**

> O rótulo e o baseline da Tarefa 2 continuam. O corte temporal continua. Esta tarefa **não troca o algoritmo**: ajusta a mesma árvore de decisão com o que a Tarefa 3 errou.
>
> Ajuste permitido: profundidade, mínimo de amostras na folha, critério (Gini ou entropia) e poda. Se entrar coluna nova, ela tem de ser outra métrica **relativa ao baseline** (tendência do `z_robusto` nas janelas anteriores, por exemplo). País, IP, rota e RTT absoluto continuam fora.

### Contrato desta tarefa

| | Artefato | Origem / destino |
|---|---|---|
| **Entra** | Árvore da Tarefa 3, matriz e erros | Tarefa 3 |
| **Entra** | Mesmo baseline, mesmo rótulo, mesmo corte | Tarefa 2 |
| **Sai** | Árvore ajustada e tabela Tarefa 3 → Tarefa 4 (F1 macro e recall de FALHA na validação) | Tarefa 5 usa **esta** árvore como ponto de partida |
| **Sai** | Regras novas em português | Tarefa 5 |
| **Sai** | Dicionário v0.3, se nasceu coluna relativa nova | Tarefa 5 |

**Não sai daqui:** outro tipo de modelo, mudança do teste, recálculo do baseline com o Período B.

- [ ] O corte é o da Tarefa 2
- [ ] Nenhuma coluna de rota entrou

---

## 1. O que o erro pediu

- [ ] Citar os erros da Tarefa 3 (classe confundida, caminho longo ou curto, pico isolado virando FALHA, RISCO sumindo)
- [ ] Cada mudança da árvore responde a um desses erros
- [ ] Parâmetros escolhidos na validação

**Erro da Tarefa 3 → mudança na árvore:**

| Erro observado | Mudança (profundidade, folha, critério ou métrica relativa nova) |
|---|---|
| | |
| | |

## 2. Árvore ajustada

- [ ] Mesmas colunas relativas, mais o que a tabela acima acrescentou
- [ ] Profundidade, folhas e três regras em português
- [ ] Fit só no treino

**Parâmetros finais candidatos (ainda não é o teste):**  
**Regras:**

1.  
2.  
3.  

## 3. Comparação na validação

| | F1 macro | Recall de FALHA | Recall de RISCO |
|---|---|---|---|
| Árvore da Tarefa 3 | | | |
| Árvore desta tarefa | | | |

- [ ] Se não houve ganho, dizer o que foi tentado e descartado
- [ ] Matriz 3×3 da árvore desta tarefa, na validação

**Leitura do ganho (ou da falta de ganho):**

## 4. Revisão com o docente

**O que foi mostrado:**  
**O que foi pedido para ajustar antes da Tarefa 5:**

## 5. Scrum e diário

- [ ] Board atualizado

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
| | | | |

---

## Rubrica — Tarefa 4 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Ajuste ligado ao erro | 1,5 | Cada mudança da árvore cita um erro da Tarefa 3; continua árvore de decisão | | |
| Rota fora do modelo | 1,0 | Nenhuma coluna de rota, país, IP ou RTT absoluto; baseline da Tarefa 2 intacto | | |
| Tabela Tarefa 3 → Tarefa 4 | 1,0 | F1 macro e recall de FALHA e de RISCO na validação, com leitura | | |
| Revisão + diário | 0,5 | Incremento mostrado ao docente; diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
