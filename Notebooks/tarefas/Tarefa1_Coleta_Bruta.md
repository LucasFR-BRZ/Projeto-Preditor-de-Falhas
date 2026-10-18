# Diário da Tarefa 1 — Problema e coleta bruta (sem rótulo)

**Período:** 17/10/2026 a 18/10/2026  
**Projeto:** Preditor de degradação de rede com RTT normalizado (independente da rota)  
**Modelo desta disciplina:** árvore de decisão. Nesta tarefa não se treina árvore.

**Equipe: 10**  
**Integrantes: Pedro Gabriel Castro da Silva, Lucas Felix Romero, Rodrigo Camargo Vieira, Luiz Eduardo dos Reis, Ricardo Aguilar Arapa**  
**Scrum Master da tarefa: Andrea Ono Sakai**  
**Repositório GitHub: https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas**

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

- [X] RFC do grupo preenchido a partir desta tabela
- [X] Dicionário v0.1 só com variáveis brutas

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

- [X] Vários fluxos, com caminhos curtos e longos na coleta
- [X] A diversidade geográfica está documentada e não virou classe
- [X] Dois períodos coletados sem sobreposição; não são blocos contíguos

Período A — 17/10/2026

Aproximadamente 20h00–22h00 UTC, equivalente a 17h00–19h00 no horário de Brasília.
226.053 registros brutos somados nos 8 arquivos.

Período B — 18/10/2026

Aproximadamente 13h04–15h03 UTC, equivalente a 10h04–12h03 no horário de Brasília.
219.757 registros brutos somados nos 8 arquivos.

Os períodos A e B ocorreram em dias diferentes, sem sobreposição, mas não são contíguos. Cada período corresponde a aproximadamente duas horas de coleta, e não a um dia completo.

- [X] JSONs brutos preservados
- [ ] Confirmar e documentar a regra de tratamento de respostas duplicadas e contadores de pacotes
- [ ] HTTP com timeout, releitura em erro transitório e coleta idempotente
- [ ] `requirements.txt` da coleta

**Evidências (notebook, commit, trecho do config):**

## 3. Relatório de qualidade — ainda sem classe

- [X] Quantidade de registros por arquivo de medição
- [X] Verificação dos campos obrigatórios básicos (`timestamp`, `prb_id`, `dst_addr`)
- [X] Verificação inicial de duplicatas exatas
- [X] Identificação preliminar de registros sem RTT e de perda total
- [X] Estatísticas iniciais de RTT e perda por arquivo
- [ ] Consolidar início e fim de cada fluxo no relatório final
- [ ] Validar definitivamente a contagem de timeouts e a perda de pacotes
- [ ] Definir o tratamento de respostas duplicadas
- [ ] Investigar os RTTs extremos nos arquivos
- [ ] Finalizar o relatório de qualidade por fluxo

**N de arquivos de resultados: 16**

**N de registros brutos: 445.810**

**N de fluxos previstos: 8**, correspondentes às 8 medições e aos pares de origem e destino identificados na coleta. Confirmar a unicidade de cada `fluxo_id` antes da preparação dos dados.

### Resumo da coleta

| Período | Arquivos | Registros brutos |
|---|---:|---:|
| A — 17/10/2026 | 8 | 226.053 |
| B — 18/10/2026 | 8 | 219.757 |
| **Total** | **16** | **445.810** |

### Verificações iniciais

- Os 16 arquivos foram encontrados e lidos.
- Todos os registros analisados eram objetos JSON.
- Não foram encontradas duplicatas exatas excedentes na verificação inicial.
- Foram identificados 6.711 registros sem RTT.
- Foram identificados 6 registros com `rcvd = 0` que, apesar disso, continham pelo menos uma resposta com RTT dentro de `result`.
- A diferença exige cuidado na interpretação de `rcvd`, das respostas marcadas como `dup` e da perda de pacotes.
- Respostas marcadas como duplicadas foram encontradas nos arquivos. Isso não significa que o registro inteiro deva ser excluído.
- Foram encontrados RTTs acima de 1.000 ms em diferentes arquivos.
- O maior RTT observado na investigação foi de 16.200,150 ms, na medição 203894195 do período B.
- Os valores extremos e as inconsistências foram preservados para investigação. Nenhum dado bruto foi apagado ou alterado.

Os números de registros sem RTT e de perda total são contagens preliminares. A regra definitiva de cálculo deve ser validada antes da criação da tabela tratada.

### Caminhos curtos e longos

Finlândia -> Porto Alegre, Brasil

Finlândia -> Paris, França

Finlândia -> Zug, Suíça

França -> Düsseldorf, Alemanha

Finlândia -> Umm ash Sha'um, Bahrein

Finlândia -> Buenos Aires, Argentina

Finlândia -> Santa Cruz, Costa Rica

Finlândia -> Ostend, Alemanha

**Observação:** 

Os dados continuam sem classes OK, RISCO ou FALHA. As verificações descritas nesta seção são de qualidade da coleta, não de desempenho de modelo.


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

## 5. Diário de bordo

| Integrante | O que fiz nesta tarefa | Dificuldades | O que pretendo manter/ajustar |
|---|---|---|---|
|Pedro Gabriel | Coleta dos dados - pesquisa de rotas - organização e retificação de memorando e documentos | nenhuma |  |
|Rodrigo Camargo | Pesquisa dos probes|Probes expirados | |
|Lucas Felix | estruturamento da coleta |Configurar o código de coleta |estrutura do JSON |
|Luiz Eduardo | Pesquisa dos probes|Probes expirados | |
|Ricardo Aguilar | Pesquisa dos probes|Probes expirados | |

## 6. Evidências gerais

- Link do RFC:https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/blob/main/Doc/memorando_de_decisao_grupo10.md
- Link do dicionário v0.1: https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/blob/main/Dicionario.md
- Link dos commits: https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas/commits/main/
- Link de `data/raw/` e do `config/`: https://drive.google.com/drive/folders/1UfmsrJEPZk8unb-jvsTGe12A9NliK-iR?hl=pt-br

---

## Rubrica — Tarefa 1 (0 a 4,0)

| Critério | Peso | Nota máxima | Nota | Observações |
|---|---|---|---|---|
| Problema | 0,5 | Fluxo, unidade de análise e proibição de RTT absoluto como falha estão explícitos | | |
| Coleta bruta | 1,5 | Vários fluxos (curto e longo), timeout preservado, RTT vazio ≠ 0, config externa, bruto intocável | | |
| Período A e Período B sem rótulo | 1,0 | Dois blocos sem sobreposição; relatório de qualidade **sem** classe | | |
| Scrum + diário | 1,0 | Papéis, board, histórias e diário de todos | | |
| **Total** | **4,0** | | **___ / 4,0** | |
