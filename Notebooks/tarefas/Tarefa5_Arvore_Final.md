# Diário da Tarefa 5 — Árvore final e teste único

**Período:** 17/10/2026 a 25/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)

**Equipe:**  Grupo 10   
**Scrum Master da tarefa:**  Andrea Ono Sakai   
**Repositório GitHub:** https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas   

> A árvore que entra aqui é a da Tarefa 4. O baseline e o rótulo são os da Tarefa 2. O teste, guardado desde a Tarefa 2, é medido **uma vez**.
>
> Continua sendo uma árvore de decisão. O grupo entrega as regras, a matriz e o que a árvore faz num fluxo que ela não viu — desde que esse fluxo tenha a própria ficha de baseline, calculada só no período inicial dele.

### Contrato desta tarefa

| | Artefato | Origem / destino |
|---|---|---|
| **Entra** | Corte da Tarefa 2 e ficha de baseline | Tarefa 2 |
| **Entra** | Árvore ajustada e regras | Tarefa 4 |
| **Sai** | Teste único: matriz 3×3, F1 por classe, F1 macro | Entrega |
| **Sai** | Checagem de fluxo não visto, com baseline próprio | Entrega |
| **Sai** | Ficha da árvore (abaixo) e dicionário v0.4 igual às colunas usadas | Entrega |

Não há tarefa seguinte.

- [ ] A profundidade não foi escolhida de novo olhando o teste
- [ ] O baseline do teste não usou medição do próprio teste

---

## 1. Árvore que será medida

- [X] Critério, profundidade, mínimo de amostras na folha, colunas
- [X] Três regras em português, as mesmas da Tarefa 4 ou as revistas **antes** de abrir o teste
- [X] Confirmação de que país, IP, rota e RTT absoluto não estão na árvore

**Parâmetros:** 

Algoritmo: árvore de decisão CART.

Critério: entropia (entropy).

Profundidade máxima: 7.

Profundidade real: 7.

Mínimo de amostras por folha: 10.

Quantidade de folhas: 22.

Semente aleatória: 42.

Treinamento: conjunto B_treino.csv.

Avaliação: conjunto B_teste.csv, utilizado para a avaliação final.

Seleção dos parâmetros: realizada anteriormente com o conjunto de validação, sem reajuste a partir do teste.

**Colunas:**  

z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80 e n5_risco.

**Regras:**

1.  Quando o z_robusto ultrapassa aproximadamente 3,5, a árvore classifica a medição como FALHA.    
2.  Quando o z_robusto não ultrapassa aproximadamente 3,5, n5_risco é menor ou igual a 1 e a perda de pacotes ultrapassa aproximadamente 16,67%, a árvore classifica a medição como FALHA.  
3.  Quando o z_robusto não ultrapassa aproximadamente 3,5, n5_risco é maior que 1,5, jitter_relativo ultrapassa aproximadamente 3, n5_aumento80 é menor ou igual a 1,5 e a perda de pacotes não ultrapassa aproximadamente 16,67%, a árvore classifica a medição como RISCO.    

## 2. Teste temporal (uma vez)

Fluxos conhecidos, período mais recente do Período B.

- [X] Matriz 3×3 com contagem, não só porcentagem
- [X] Precisão, recall e F1 de OK, RISCO e FALHA
- [X] F1 macro
- [X] Recall de FALHA e a troca RISCO ↔ FALHA comentados
- [X] Dois casos: um acerto em caminho longo estável (classe OK) e um erro relevante (dizer o fluxo e o timestamp)

A avaliação foi realizada no conjunto de teste temporal, composto por 66.552 medições.

**Matriz:**  

As linhas representam a classe real e as colunas representam a classe prevista. 

|Classe real | Previsto OK | Previsto RISCO |  Previsto FALHA  |
|---|---|---|---|
| OK | 49.768  | 0 | 0 |
| RISCO | 0  | 6.320 | 2 |
| FALHA | 7 | 4 | 10.451 |

Métricas por classe:
---
|Classe real | Precisão | Recall |  F1-score  |Suporte |
|---|---|---|---|---|
| OK | 1,000000  | 1,000000 | 1,000000 |  49.768  |
| RISCO | 0,999684  | 0,999684 | 0,999684 | 6.322 |
| FALHA | 0,999809 | 0,998949 | 0,999379 | 10.462 | 

Observação: os valores por classe foram calculados a partir da matriz de confusão; o relatório original do Colab os apresentou arredondados para duas casas decimais.

**F1 macro no teste:**  

F1 macro no teste: 0,999611.

Recall de FALHA: 0,998949.

**Casos:**

- Acerto em fluxo com baseline elevado: o fluxo 7623|200.9.157.207, no timestamp 1792334991, apresentou mediana de baseline de aproximadamente 562,51 ms e foi classificado corretamente como OK. Isso demonstra que uma mediana de baseline elevada não levou automaticamente à classificação como RISCO ou FALHA.
- Erro relevante: o fluxo 7679|200.5.63.109, no timestamp 1792333758, foi rotulado como FALHA, mas a árvore previu OK. A perda registrada foi de 0% e o z_robusto foi aproximadamente 0,093. Esse caso mostra uma situação em que a árvore não reproduziu o rótulo atribuído pelas regras heurísticas.
Leitura dos resultados:

A árvore classificou corretamente 66.539 das 66.552 medições avaliadas, apresentando 13 erros. Houve duas medições RISCO classificadas como FALHA, quatro medições FALHA classificadas como RISCO e sete medições FALHA classificadas como OK.

O F1 macro de 0,999611 e o recall de FALHA de 0,998949 indicam alta concordância com os rótulos utilizados. Entretanto, como esses rótulos foram gerados por regras heurísticas baseadas nas mesmas métricas utilizadas pela árvore, o desempenho não comprova, isoladamente, a capacidade de prever falhas reais de rede.


## 3. Fluxo que a árvore não viu

Separar um ou mais `fluxo_id` inteiros que não estejam no treino. A ficha desse fluxo sai só do período inicial dele (o mesmo piso: 1.500 RTT válidos; abaixo disso o fluxo não é avaliado).

- [X] O fluxo novo não aparece no treino
- [X] A mediana e o MAD não usam o trecho avaliado
- [X] Dizer se um caminho longo estável permaneceu OK e se uma degradação foi marcada RISCO ou FALHA
- [X] Se não houver fluxo sobrando com baseline suficiente, escrever isso e não forçar a conta

**Fluxos separados:** 

Nenhum fluxo elegível foi encontrado para a avaliação de fluxo não visto. Foram analisados oito arquivos JSON do Período A, mas nenhum fluxo atendeu simultaneamente aos critérios de possuir pelo menos 1.500 RTTs válidos no baseline, não aparecer no conjunto de treino e estar presente no conjunto de teste.

**Resultado:**

Não foi possível realizar a checagem de generalização em um fluxo não visto com os critérios estabelecidos. Para evitar uma avaliação inadequada, nenhum fluxo foi selecionado artificialmente e não foram utilizados dados do período avaliado para construir o baseline.

A avaliação temporal apresentada na seção 2 permanece válida como resultado do teste final. Entretanto, a generalização para um fluxo totalmente ausente do treino não pôde ser confirmada nesta etapa.

## 4. O que declarar

- [ ] Se a árvore só repete a tabela de rótulo, dizer isso: o rótulo foi feito com as mesmas métricas, então a árvore está aprendendo a política, não um ticket de roteador
- [ ] Se o alvo for o estado 12 minutos à frente, comparar com a regra “repetir a classe atual”. Sem ganho nessa comparação, o entregável é um **detector** do estado atual, não um preditor
- [ ] Limitações: baseline fixo não acompanha troca de rota; rajada curta estima mal o jitter; Anchors não são a rede do campus

## 5. Ficha da árvore

| Campo | Conteúdo |
|---|---|
| Problema | Classe OK, RISCO ou FALHA pelo desvio ao baseline do fluxo |
| Unidade | Uma medição de um `fluxo_id` no Período B |
| Baseline | Período A, com mediana e MAD calculados a partir das medições iniciais. |
| Colunas |z_robusto, aumento_pct, jitter_relativo, perda_pct, timeout_atual, n5_timeout, n5_aumento80, n5_risco. |
| Árvore | CART, critério entropia, profundidade máxima 7, profundidade real 7, 22 folhas, mínimo de 10 amostras por folha e semente 42. |
| Teste (uma vez) | F1 macro: 0,999611; recall de FALHA: 0,998949. |
| O que ela não faz | Não classifica distância e não avalia fluxos sem baseline suficiente. Os resultados não comprovam, isoladamente, previsão de falhas reais. |
| Como reproduzir | Utilizar o notebook do projeto, requirements.txt, arquivos de configuração disponíveis e os conjuntos de dados separados na Tarefa 2. |

## 6. Scrum e diário

- [ ] Board atualizado

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
|Pedro Gabriel Castro da Silva |Treinei a árvore final com os parâmetros definidos na Tarefa 4, executei a avaliação no teste e registrei a matriz de confusão e as métricas. Também verifiquei a disponibilidade de fluxos para a checagem de generalização. |Não foi encontrado fluxo que atendesse aos critérios de 1.500 RTTs válidos, ausência no treino e presença no teste. Também foi necessário interpretar os erros apesar das métricas elevadas. |Manter as métricas relativas ao baseline, preservar a separação temporal e documentar as limitações dos resultados. |
| Rodrigo Camargo Vieira| Análise e revisão da tarefa | Pesquisar sobre todas as informações da tarefa para revisão de dados| Manter os dados que estiverem corretos e ajustar apenas os dados que não estão de acordo com a tarefa.|

---

## Rubrica — Tarefa 5 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Árvore final legível | 1,0 | Regras em português, colunas relativas, parâmetros da Tarefa 4 | | |
| Teste único | 1,5 | Matriz 3×3 em contagem, F1 por classe, F1 macro, casos curto/longo; teste não escolheu a árvore | | |
| Fluxo novo com baseline próprio | 1,0 | Ficha só no período inicial; ou limitação declarada se faltou fluxo | | |
| Ficha + diário | 0,5 | Ficha preenchida e diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
