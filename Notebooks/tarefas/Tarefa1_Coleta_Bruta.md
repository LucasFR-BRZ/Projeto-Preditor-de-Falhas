# Diário da Tarefa 1 — Problema e coleta bruta (sem rótulo)

**Período:** 17/08/2026 a 17/09/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)  
**Modelo desta disciplina:** árvore de decisão. Nesta tarefa não se treina árvore.

**Equipe: 10**  
**Integrantes: Pedro Gabriel Castro da Silva, Lucas Felix Romero, Rodrigo Camargo Vieira, Luiz Eduardo dos Reis, Ricardo Aguilar Arapa**  
**Scrum Master da tarefa: Andrea Ono Sakai**  
**Repositório GitHub: https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/blob/main/Notebooks/Coleta_de_dados_corrigido2.ipynb**

> Esta tarefa entrega o problema e o **dado cru**. Não há classe OK, RISCO ou FALHA. Não há baseline, não há mediana e não há árvore. Quem rotular aqui mistura a coleta com a decisão da Tarefa 2.
>
> A rota entra na coleta só para haver caminhos curtos e longos no mesmo arquivo. RTT alto **não** é falha. País, IP e nome da rota **não** serão coluna da árvore.

### Contrato desta tarefa

| | Artefato | Quem usa depois |
|---|---|---|
| **Entra** | RFC do projeto | — |
| **Sai** | RFC preenchido pelo grupo (problema, horizonte, custo de errar FALHA, fora de escopo) | Tarefas 2 e 5 |
| **Sai** | Dicionário v0.1 só com colunas **brutas** da medição | Tarefa 2 |
| **Sai** | `data/raw/` + `config/` + `requirements.txt` | Tarefa 2 **é obrigada a usar este bruto** |
| **Sai** | Este diário | Tarefas seguintes |

**Não sai daqui:** baseline, rótulo, `z_robusto`, split, árvore, métrica de modelo.

---

## 1. Definição do problema

Responder no diário. A resposta tem de bater com o RFC.

| Pergunta | Resposta do grupo |
|---|---|
| Qual evento a árvore vai classificar? | Degradação do fluxo em relação ao **próprio** normal: OK, RISCO ou FALHA. Não é “rota longa” nem “RTT acima de 100 ms”. |
| O que é um fluxo? | `fluxo_id = probe_id \| dst_addr` (origem, destino e, se existir, `measurement_id`). |
| O que é cada linha do bruto? | Uma medição ICMP desse fluxo, com timestamp. Ainda **sem** classe. |
| Qual horizonte fica para depois? | Detector: estado da medição atual. Preditor: estado 12 minutos à frente. A árvore só entra na Tarefa 3. |
| Quem usa o alerta? | Quem opera o enlace: investigar (FALHA), observar (RISCO) ou não agir (OK). |
| O que está proibido como definição de falha? | Limiar global de RTT, país, continente ou nome da rota. |

- [ ] RFC do grupo preenchido a partir desta tabela
- [ ] Dicionário v0.1 só com variáveis brutas

## 2. O que coletar (e o que não criar)

Fonte: medições públicas já existentes de ping IPv4 (mesh de Anchors do RIPE Atlas, somente `GET`). Não criar medição própria e não gastar crédito.

Cada registro bruto guarda, quando a API trouxer:

| Campo | Unidade | Papel agora |
|---|---|---|
| `timestamp` | UTC | Ordenar o fluxo |
| `measurement_id` | — | Identidade da medição |
| `probe_id` | — | Origem |
| `dst_addr` | — | Destino |
| `fluxo_id` | texto estável | `probe_id\|dst_addr` |
| RTT da rajada (médio; mín/máx se existirem) | ms | Medição. Vazio se não houver resposta. **Nunca 0** |
| enviados, recebidos | contagem | |
| `perda_pct` | % | `(enviados − recebidos) / enviados × 100` |
| `jitter_ms` | ms | Desvio-padrão dos RTT da rajada **somente** com 2 ou mais respostas. Senão, vazio. **Nunca 0 fingindo estabilidade** |
| `timeout_atual` | 0 ou 1 | 1 se não há RTT ou perda = 100% |
| país ou rota | texto | Só auditoria de diversidade. **Fora da futura árvore** |

Regras da coleta:

- [ ] Vários fluxos, com pelo menos um caminho curto e um caminho longo no mesmo período
- [ ] A diversidade geográfica está documentada e **não** virou classe
- [ ] Dois blocos de tempo contíguos, sem amostra nos dois: Período A (só para o baseline da Tarefa 2) e Período B (medições que serão rotuladas). Referência do projeto: 7 dias + 7 dias a partir de 06/09/2026 04:32 UTC. Outro recorte só vale se os dois blocos continuarem sem sobreposição e o A tiver volume para o mínimo da Tarefa 2
- [ ] Timeout permanece no arquivo
- [ ] JSON bruto preservado; a tabela tratada não apaga o bruto
- [ ] Parâmetros (período, probes, destinos) em `config/`, não espalhados no código
- [ ] HTTP com timeout, releitura em erro transitório e coleta idempotente (rodar de novo não duplica)
- [ ] `requirements.txt` da coleta

**Evidências (notebook, commit, trecho do config):**

## 3. Relatório de qualidade — ainda sem classe

- [X] Registros por `fluxo_id`
- [ ] Início e fim de cada fluxo
- [ ] Campos ausentes (RTT vazio é ausência, não zero)
- [ ] Duplicatas
- [ ] Quantidade de timeouts
- [ ] RTT e perda descritos (mínimo, mediana, máximo) **sem** dizer OK, RISCO ou FALHA

**N de registros brutos: 8**  
**N de fluxos: 8**  
**Caminho curto e caminho longo presentes (quais):**

## 4. Scrum

- [ ] Product Owner = docente; Scrum Master da tarefa; time de desenvolvimento
- [ ] Board com To do / Doing / Done
- [ ] Pelo menos 3 histórias: coletar fluxos diversos; preservar o bruto com timeout; separar Período A e Período B sem rotular

**Histórias:**  
1. Como integrante da equipe, quero coletar medições de várias sondas e destinos, incluindo caminhos curtos e longos, para analisar diferentes comportamentos de rede.

Critério de aceitação: os dados devem conter vários fluxos identificáveis por probe_id e dst_addr, com a diversidade documentada.  
2.  Como integrante da equipe, quero armazenar os resultados originais da API, inclusive as medições sem resposta, para garantir a rastreabilidade dos dados.

Critério de aceitação: o JSON original deve ser preservado, os RTTs ausentes não podem ser substituídos por zero e os timeouts devem continuar representados nos dados.

3. Como integrante da equipe, quero organizar a coleta em dois blocos temporais sem sobreposição e sem rótulos, para permitir que o baseline seja construído na Tarefa 2 e a rotulação seja realizada posteriormente.

Critério de aceitação: os intervalos devem estar documentados na configuração, sem sobreposição, e os dados devem permanecer sem classes OK, RISCO ou FALHA. 

**Link do board: https://atlas.ripe.net/**

## 5. Diário de bordo

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
|Pedro Gabriel | Pesquisa dos probes| Probes expirados | |
|Rodrigo Camargo | Pesquisa dos probes|Probes expirados | |
|Lucas Felix | estruturamento da coleta |Configurar o código de coleta |estrutura do JSON |
|Luiz Eduardo | Pesquisa dos probes|Probes expirados | |
|Ricardo Aguilar | Pesquisa dos probes|Probes expirados | |

## 6. Evidências gerais

- Link do RFC:https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/blob/main/Doc/memorando_de_decisao_grupo10.md
- Link do dicionário v0.1: 
- Link dos commits: https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/commits/main/
- Link de `data/raw/` e do `config/`: https://drive.google.com/drive/folders/1pF0rNDtvEVErohRlAvmZZtZDmRZDeCVG?hl=pt-br

---

## Rubrica — Tarefa 1 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Problema | 0,5 | Fluxo, unidade de análise e proibição de RTT absoluto como falha estão explícitos | | |
| Coleta bruta | 1,5 | Vários fluxos (curto e longo), timeout preservado, RTT vazio ≠ 0, config externa, bruto intocável | | |
| Período A e Período B sem rótulo | 1,0 | Dois blocos sem sobreposição; relatório de qualidade **sem** classe | | |
| Scrum + diário | 1,0 | Papéis, board, histórias e diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
