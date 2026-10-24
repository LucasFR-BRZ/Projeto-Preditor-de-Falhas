
# Network Failure Predictor

## Objetivo

Desenvolver um sistema capaz de analisar dados de comunicação de rede obtidos por meio de medições ICMP e identificar possíveis sinais de degradação ou falha no envio de pacotes.

O projeto utiliza métricas de rede e técnicas de aprendizado de máquina para classificar medições de acordo com padrões associados aos estados `OK`, `RISCO` e `FALHA`.

## Descrição do Projeto

O projeto utiliza dados coletados por meio da plataforma RIPE Atlas, que realiza medições de conectividade e desempenho da internet com sondas distribuídas geograficamente.

A partir dessas medições, são analisadas características como latência, perda de pacotes, jitter e variações no tempo de resposta.

Os dados são organizados em dois períodos:

- **Período A:** utilizado para calcular o baseline de cada fluxo, que representa o comportamento de referência da conexão.
- **Período B:** utilizado para calcular as métricas, atribuir os rótulos heurísticos e preparar os conjuntos de treino, validação e teste.

Com base nessas informações, foi desenvolvido um modelo de árvore de decisão para classificar as medições em três categorias:

- `OK`: medição que não atende aos critérios definidos de risco ou falha.
- `RISCO`: medição que atende aos critérios definidos de risco recorrente.
- `FALHA`: medição que atende a pelo menos um dos critérios definidos de falha.

As classificações são geradas por regras heurísticas estabelecidas no projeto. Portanto, o modelo aprende a reproduzir essas classificações, que não representam necessariamente falhas reais confirmadas na infraestrutura de rede.

## Integrantes do Grupo

- Lucas Felix Romero — 41969286
- Pedro Gabriel Castro da Silva — 42832152
- Ricardo Aguilar Arapa — 4262815
- Luiz Eduardo dos Reis — 42973759
- Rodrigo Camargo Vieira — 48802565

## Informações da Entrega

- **Curso:** Ciência da Computação
- **Semestre:** 4º semestre
- **Período:** Noturno
- **Grupo:** 10
- **Repositório:** [Projeto-Preditor-de-Falhas](https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas)
- **Branch principal:** `main`

## Tecnologias Utilizadas

- **RIPE Atlas:** fonte das medições de rede.
- **Python:** processamento dos dados e desenvolvimento do modelo.
- **Google Colab:** ambiente utilizado para execução dos notebooks.
- **Pandas:** organização e manipulação dos dados.
- **Scikit-learn:** treinamento e avaliação da árvore de decisão.
- **Visual Studio Code:** edição dos arquivos e organização do projeto.
- **Git e GitHub:** controle de versão e armazenamento do código.

## Estrutura do Projeto

A organização do repositório contempla a documentação, os notebooks e os dados utilizados no desenvolvimento.

```text
Projeto-Preditor-de-Falhas/
├── DATA/
│   └── Arquivos JSON e CSV das medições coletadas
├── Doc/
│   └── memorando_de_decisao_grupo10_2.md
├── Notebooks/
│   ├── Coleta_de_dados.ipynb
│   └── Notebooks de preparação e modelagem
├── dicionario.md
└── README.md
```

Os nomes e a localização dos arquivos devem corresponder aos arquivos efetivamente presentes no repositório.

## Processamento dos Dados

O processamento dos dados envolve as seguintes etapas:

1. Coleta das medições de ping disponibilizadas pelo RIPE Atlas.
2. Organização dos registros por sonda e endereço IP de destino.
3. Separação dos dados nos Períodos A e B.
4. Cálculo do baseline a partir das medições do Período A.
5. Cálculo das métricas de rede e aplicação das regras heurísticas no Período B.
6. Divisão cronológica dos dados em treino, validação e teste.
7. Treinamento, seleção dos parâmetros e avaliação da árvore de decisão.

Para a preparação do modelo, foi utilizado provisoriamente o mínimo de 20 RTTs válidos por fluxo no cálculo do baseline, devido à quantidade disponível na coleta.

## Modelo de Aprendizado de Máquina

O modelo final utiliza o algoritmo `DecisionTreeClassifier`, da biblioteca Scikit-learn.

### Entradas do modelo

A árvore de decisão utiliza oito métricas:

- `z_robusto`
- `aumento_pct`
- `jitter_relativo`
- `perda_pct`
- `timeout_atual`
- `n5_timeout`
- `n5_aumento80`
- `n5_risco`

O modelo oficial não utiliza endereços IP, países, rotas ou RTT absoluto como entradas. A identificação do fluxo é utilizada para organizar as medições, não como característica de entrada da árvore.

### Parâmetros finais

| Parâmetro | Valor |
|---|---|
| Algoritmo | `DecisionTreeClassifier` |
| Critério | `entropy` |
| Profundidade máxima | 7 |
| Mínimo de amostras por folha | 10 |
| Semente aleatória | 42 |
| Profundidade real | 7 |
| Número de folhas | 22 |

## Resultados da Avaliação

A avaliação final foi realizada no conjunto de teste, que contém 66.552 registros.

| Métrica | Resultado |
|---|---:|
| Registros de teste | 66.552 |
| Classificações corretas | 66.539 |
| Erros de classificação | 13 |
| F1 macro | 0,999611 |
| Recall da classe `FALHA` | 0,998949 |

Os resultados indicam alta concordância entre as previsões do modelo e os rótulos heurísticos no conjunto de teste.

Entretanto, esses valores não comprovam, isoladamente, a capacidade de prever falhas reais futuras, pois os rótulos utilizados no treinamento e na avaliação foram gerados por regras baseadas nas próprias métricas de rede.

A avaliação realizada corresponde à classificação do estado de cada medição segundo os critérios adotados, e não a uma previsão futura de falhas validada por registros independentes de incidentes.

## Limitações

- Os rótulos `OK`, `RISCO` e `FALHA` são definidos por regras heurísticas.
- Não foi realizada validação independente dos rótulos com registros reais de incidentes.
- A verificação de generalização para um fluxo não visto, com pelo menos 1.500 RTTs válidos no Período A, não pôde ser realizada porque nenhum fluxo atendeu a todos os critérios definidos.
- O baseline pode deixar de representar adequadamente uma conexão caso o comportamento da rota mude.
- As medições do RIPE Atlas não representam necessariamente o comportamento de uma rede local específica.
- Os resultados de classificação não devem ser interpretados como garantia de previsão de falhas futuras.

## Documentação

O projeto conta com os seguintes documentos:

- **Dicionário de Dados:** descreve os campos coletados, as métricas calculadas, os rótulos e as entradas do modelo.
- **Memorando de Decisão:** registra as decisões relacionadas à coleta e ao desenvolvimento do projeto.
- **Notebooks:** contêm os procedimentos de coleta, preparação dos dados e desenvolvimento do modelo.

## Observações

- O repositório deve permanecer acessível para consulta.
- Todos os integrantes devem estar identificados neste README.
- Os arquivos e códigos desenvolvidos devem estar disponíveis e organizados no repositório.
- Os nomes e caminhos apresentados na estrutura devem ser conferidos com a organização real dos arquivos.
- Não devem ser incluídas senhas, tokens, chaves de API ou outros dados confidenciais no repositório.
