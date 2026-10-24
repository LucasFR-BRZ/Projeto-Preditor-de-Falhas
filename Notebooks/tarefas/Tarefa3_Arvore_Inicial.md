# Diário da Tarefa 3 — Primeira árvore de decisão

**Período:** 17/10/2026 a 18/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:**  Grupo 10   
**Scrum Master da tarefa:**  Andrea Ono Sakai   
**Repositório GitHub:** 

> Tarefa 1 coletou. Tarefa 2 fez o baseline e o rótulo. Esta tarefa **não refaz** a ficha nem a tabela de classes.
>
> O modelo é uma **árvore de decisão**. O entregável é a árvore: atributos de cada divisão, profundidade, folhas e as regras lidas em português. Não é um desfile de algoritmos.
>
> A árvore oficial usa só métricas relativas ao baseline do fluxo. Uma segunda árvore, de contraste, pode ver a rota ou o RTT absoluto — só para mostrar que ela aprende distância. Essa árvore de contraste **não** é o modelo do projeto.

### Contrato desta tarefa

| | Artefato | Origem / destino |
|---|---|---|
| **Entra** | Ficha de baseline, dataset rotulado, corte temporal | Tarefa 2 — **os mesmos** |
| **Sai** | Árvore treinada só no treino, com profundidade e critério registrados | Tarefa 4 mexe nesta árvore, não no rótulo |
| **Sai** | Regras da árvore em texto e matriz 3×3 (OK, RISCO, FALHA) | Tarefa 4, para atacar o erro |
| **Sai** | Árvore de contraste (com rota ou RTT absoluto) e a frase do que ela aprendeu | Prova de que o modelo oficial não segue esse atalho |

**Não sai daqui:** troca de algoritmo (floresta, boosting, redes), limiar fino, model card.

- [X] Treino, validação e teste são os da Tarefa 2
- [X] A ficha de baseline não foi recalculada com o Período B

---

## 1. Colunas que a árvore oficial pode usar

Entram: `z_robusto`, `aumento_pct`, `jitter_relativo`, `perda_pct`, `timeout_atual`, `n5_timeout`, `n5_aumento80`, `n5_risco`.

Não entram: país, IP, `rota_id`, `fluxo_id`, nome do destino, RTT em milissegundos no lugar das métricas relativas.

- [X] Lista de colunas do treino colada no diário
- [X] Conferido que a classe (`OK` / `RISCO` / `FALHA`) é o alvo, não uma feature

**Colunas usadas:**  
z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80 e n5_risco.

A variável classe foi utilizada exclusivamente como alvo da classificação. As colunas de identificação do fluxo, o endereço IP e o RTT absoluto não foram utilizados pela árvore oficial.

## 2. A árvore

- [X] Uma árvore de decisão (CART: Gini ou entropia — declarar qual)
- [X] `max_depth` e `min_samples_leaf` escolhidos olhando **só a validação**, não o teste
- [X] Semente registrada se o treino for estocástico
- [X] Ajuste (`fit`) só no treino
- [X] Profundidade real, número de folhas e as primeiras divisões descritas
- [X] Pelo menos três regras no formato “se métrica ≤ limiar e … então classe”, copiadas da árvore (não inventadas)

**Critério, profundidade máxima pedida, profundidade obtida, folhas:** 

Foi utilizado o algoritmo CART com critério Gini. A profundidade máxima foi selecionada por comparação entre os valores 3, 5, 7 e 10, utilizando exclusivamente os resultados da validação. A profundidade máxima escolhida foi 5, pois apresentou o mesmo F1 macro que as profundidades 7 e 10, com uma árvore menor.
- Profundidade máxima: 5
- Profundidade real: 5
- Número de folhas: 14
- min_samples_leaf: 50
- Semente aleatória: 42
- Ajuste do modelo: realizado exclusivamente com os dados de treino.

**Regras lidas da árvore:**

1.  Se z_robusto > 3,4999, então a classe prevista é FALHA.
2.  Se z_robusto ≤ 3,4999, jitter_relativo ≤ 3,0010, z_robusto ≤ 2,0005, perda_pct ≤ 16,6667 e n5_aumento80 ≤ 1,5, então a classe prevista é OK.
3.  Se z_robusto ≤ 3,4999, jitter_relativo ≤ 3,0010, z_robusto > 2,0005 e n5_risco > 1,5, então a classe prevista é RISCO.

## 3. Leitura do erro

No bloco de **validação** (o teste fica fechado até a Tarefa 5; se o grupo já mediu o teste aqui, registra e **não** escolhe profundidade de novo olhando esse número):

- [X] Matriz 3×3 com contagem
- [X] Precisão, recall e F1 de OK, RISCO e FALHA
- [X] F1 macro
- [X] Acurácia sozinha não decide: com maioria OK, ela fica alta mesmo errando FALHA
- [X] Dois erros concretos: uma medição de caminho longo estável que tenha caído em FALHA, ou uma FALHA de caminho curto que tenha caído em OK. Se não houver, dizer que o caso não apareceu na validação

**Matriz e F1 macro (validação):**  

A avaliação foi realizada com o conjunto de validação, sem utilizar o conjunto de teste.

Matriz de confusão — linhas representam a classe verdadeira e colunas representam a classe prevista.

Classe verdadeira	Previsto OK	Previsto RISCO	Previsto FALHA
OK	31.777	0	0
RISCO	7	4.053	0
FALHA	8	26	6.904

Métricas por classe:
Classe	Precisão	Recall	F1
OK	99,95%	100,00%	99,98%
RISCO	99,36%	99,83%	99,59%
FALHA	100,00%	99,51%	99,75%

F1 macro: 99,78%.

Acurácia: 99,90%.

Foram registradas 41 classificações incorretas em 42.775 registros de validação. Embora os resultados sejam elevados, eles indicam principalmente a capacidade de reproduzir os rótulos heurísticos criados na Tarefa 2. Não comprovam, por si só, a previsão de falhas reais de rede.

**Erros concretos (fluxo, timestamp, métricas, classe verdadeira, classe da árvore):**

Durante a validação, foram identificados 41 registros classificados incorretamente pela árvore de decisão. Dois exemplos são os fluxos 6935|200.5.63.109 e 7827|80.243.198.243, ambos rotulados como FALHA por apresentarem perda de pacotes de 66,67% e 33,33%, respectivamente, mas classificados pela árvore como OK. Outro exemplo é o fluxo 7578|200.9.157.207, rotulado como RISCO e classificado como OK, associado ao critério de risco atual e à ocorrência de pelo menos duas situações de risco na janela recente (n5_risco >= 2). Esses casos demonstram que, apesar do F1 macro elevado, a árvore ainda pode deixar de identificar medições classificadas como RISCO ou FALHA pelas regras de rotulagem.


## 4. Árvore de contraste (não é o modelo)

Treinar outra árvore, no mesmo treino, acrescentando a rota ou o RTT absoluto.

- [X] Dizer qual divisão apareceu perto da raiz (em geral a rota ou um corte fixo de milissegundos)
- [X] Declarar que essa árvore fica fora da entrega final

**O que a árvore de contraste usou na raiz:**

Experimento de contraste: foi treinada uma segunda árvore CART, com critério Gini, profundidade máxima 5, mínimo de 50 amostras por folha e semente 42. Ela utilizou os oito atributos oficiais acrescidos de rtt_ms.

F1 macro na validação: 99,78%, igual ao da árvore oficial.

Primeira divisão: z_robusto ≤ 3,4999, e não rtt_ms.

Conclusão: a inclusão do RTT absoluto não alterou o F1 macro nem o atributo utilizado na raiz neste experimento. Portanto, não foi possível demonstrar o atalho da distância com esses resultados. A árvore de contraste não será utilizada como modelo oficial.

## 5. Scrum e diário

- [ ] Board atualizado

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
| Pedro Gabriel Castro da Silva | Treinamento e avaliação inicial da árvore de decisão, análise dos erros de validação e comparação com RTT absoluto. | Interpretar os erros de classificação e avaliar a influência do RTT absoluto. | Manter a separação entre treino, validação e teste e registrar os resultados dos experimentos. |
| | | | |
| | | | |
| | | | |
| | | | |

---

## Rubrica — Tarefa 3 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Árvore oficial | 1,5 | Uma árvore, critério e profundidade declarados, regras em português, fit só no treino | | |
| Métricas de três classes | 1,0 | Matriz 3×3, F1 por classe e F1 macro; acurácia não é o critério | | |
| Independência da rota | 1,0 | Colunas relativas apenas; árvore de contraste mostra o atalho da distância e é descartada | | |
| Scrum + diário | 0,5 | Board e diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
