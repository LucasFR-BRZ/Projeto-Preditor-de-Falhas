# Diário da Tarefa 4 — Ajuste da árvore a partir do erro

**Período:** 17/10/2026 a 23/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:** Grupo 10    
**Scrum Master da tarefa:** Andrea Ono Sakai      
**Repositório GitHub:** https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas   

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

- [X] Citar os erros da Tarefa 3 (classe confundida, caminho longo ou curto, pico isolado virando FALHA, RISCO sumindo)
- [X] Cada mudança da árvore responde a um desses erros
- [X] Parâmetros escolhidos na validação

**Erro da Tarefa 3 → mudança na árvore:**

| Erro observado | Mudança (profundidade, folha, critério ou métrica relativa nova) |
|---|---|
|Casos de FALHA foram classificados como OK, incluindo registros com perda de pacotes elevada. |Redução de min_samples_leaf de 50 para 10 e comparação entre os critérios Gini e entropia. |
|Casos de RISCO foram classificados como OK. |Testes com diferentes profundidades e critérios para melhorar a separação entre as classes. | 
|A árvore da Tarefa 3 apresentou 41 erros na validação. |Comparação entre profundidades 3, 5, 7 e 10, selecionando a configuração com melhor resultado e menor complexidade. |

## 2. Árvore ajustada

- [X] Mesmas colunas relativas, mais o que a tabela acima acrescentou
- [X] Profundidade, folhas e três regras em português
- [X] Fit só no treino

**Parâmetros finais candidatos (ainda não é o teste):**  

- Algoritmo: árvore de decisão CART.
- Critério: entropia (entropy).
- Profundidade máxima: 7.
- Profundidade real: 7.
- Mínimo de amostras por folha: 10.
- Quantidade de folhas: 22.
- Semente aleatória: 42.
- Treinamento realizado somente com o conjunto de treino.
- Seleção dos parâmetros realizada com o conjunto de validação.  

Foram mantidas as oito métricas relativas utilizadas na Tarefa 3: z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80 e n5_risco. Não foram utilizadas colunas de rota, país, IP ou RTT absoluto no modelo oficial.

**Regras:**

1.  Quando o z_robusto ultrapassa aproximadamente 3,5, o fluxo é classificado como FALHA.   

2.  Quando o z_robusto não ultrapassa aproximadamente 3,5, n5_risco é menor ou igual a 1 e a perda de pacotes ultrapassa aproximadamente 16,67%, o fluxo é classificado como FALHA. 

3.  Quando o z_robusto não ultrapassa aproximadamente 3,5, n5_risco é maior que 1,5, jitter_relativo ultrapassa aproximadamente 3 e a perda de pacotes não ultrapassa aproximadamente 16,67%, com n5_aumento80 menor ou igual a 1,5, o fluxo é classificado como RISCO. 

## 3. Comparação na validação

| | F1 macro | Recall de FALHA | Recall de RISCO |
|---|---|---|---|
| Árvore da Tarefa 3 |0,997751 |0,995099 |0,998276 |
| Árvore desta tarefa |0,999645 |0,999712 |0,999015 |

Matriz de confusão da Tarefa 4
---
|Classe real | Previsto OK | Previsto RISCO | Previsto FALHA |
|---|---|---|---|
| OK |31.777 |0 |0 |
| RISCO |0 |4.056 |4 |
| FALHA |1 |1 |6.936 |

**Leitura do ganho (ou da falta de ganho):**

A árvore ajustada apresentou melhora nas três métricas avaliadas. O F1 macro aumentou de 0,997751 para 0,999645; o recall de FALHA passou de 0,995099 para 0,999712; e o recall de RISCO passou de 0,998276 para 0,999015.

A nova árvore apresentou seis erros na validação: quatro registros RISCO classificados como FALHA e dois registros FALHA classificados incorretamente. A configuração escolhida apresentou o mesmo F1 macro que outra configuração testada com profundidade máxima 10, mas com menos folhas, sendo preferida por sua menor complexidade.

Os resultados indicam maior capacidade de reproduzir os rótulos utilizados no projeto, que são baseados em critérios heurísticos, e não constituem, isoladamente, comprovação de previsão de falhas reais.

## 4. Revisão com o docente

**O que foi mostrado:**  



**O que foi pedido para ajustar antes da Tarefa 5:**

## 5. Scrum e diário

- [ ] Board atualizado

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
|Pedro Gabriel Castro da Silva | Análise dos erros da Tarefa 3, comparação de parâmetros e avaliação da árvore ajustada na validação. |Seleção dos parâmetros sem aumentar desnecessariamente a complexidade do modelo. |Manter as métricas relativas ao baseline e utilizar a validação para selecionar os parâmetros. |
|   |   |   |   |   
|   |   |   |   |   
|   |   |   |   |   
|   |   |   |   |   

---

## Rubrica — Tarefa 4 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Ajuste ligado ao erro | 1,5 | Cada mudança da árvore cita um erro da Tarefa 3; continua árvore de decisão | | |
| Rota fora do modelo | 1,0 | Nenhuma coluna de rota, país, IP ou RTT absoluto; baseline da Tarefa 2 intacto | | |
| Tabela Tarefa 3 → Tarefa 4 | 1,0 | F1 macro e recall de FALHA e de RISCO na validação, com leitura | | |
| Revisão + diário | 0,5 | Incremento mostrado ao docente; diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
