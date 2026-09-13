# Memorando de Decisão — Fonte de Dados do Projeto

| Campo | Informação |
|---|---|
| Curso / Disciplina | `Ciência da Computação / Estrutura de Dados II` |
| Projeto integrador | `Network Failure Predictor` |
| Orientador(a) | `Andrea Ono Sakai` |
| Data de entrega desta etapa | `08/09/2026` |
| Integrantes do grupo | `Lucas Felix Romero, Ricardo Aguilar Arapa, Pedro Gabriel Castro da Silva, Luiz Eduardo dos Reis` |


---

## 1. Situação

A equipe precisa decidir se, na próxima etapa do projeto, deve utilizar um dataset real de ICMP já publicado ou coletar novos dados por meio da API do RIPE Atlas, buscando a opção que melhor atenda ao pipeline de transformação dos dados em janelas e, depois, em X = latência, perda, jitter.

## 2. Opção A — Dataset real
Origem / link: Hats Network — Global Latency Measurements
https://hatsnet.io/opendata/latency/

Documentação da metodologia:
https://hatsnet.io/docs/network/latency

Formato: CSV, JSON e YAML. O repositório também disponibiliza arquivos agregados, arquivos compactados, datapackage.json, manifest.json e somas SHA-256 para verificação dos arquivos

Período coberto: o dataset possui lançamentos versionados diariamente. Cada versão é identificada por uma data, como v20260823, e o diretório latest/ aponta para a versão mais recente. Uma nova versão completa é publicada quando há alteração nas medições.

Campos disponíveis: RTT (tempo de ida e volta) das amostras ICMP, origem e destino da medição, além de estatísticas por par, como RTT mínimo, médio e máximo, desvio-padrão, jitter e perda de pacotes.
Licença de uso: Creative Commons Attribution 4.0 International (CC BY 4.0). A licença permite compartilhar e adaptar os dados, desde que seja dado o devido crédito.

**Resumo do que foi encontrado:**

O dataset Hats Network Global Latency Measurements reúne medições reais de latência entre os PoPs da rede global da Hats Network (AS203314). Os dados são produzidos a partir de medições ICMP realizadas na infraestrutura de backbone da rede.

Um ponto positivo para o projeto é que os dados brutos por par podem ser utilizados para formar janelas de observação. Isso permite trabalhar diretamente com os valores de RTT e calcular as características necessárias para o modelo, de acordo com a definição adotada pelo projeto.

Como exemplo, na medição entre São Paulo (GRU) e Johannesburg (JNB), realizada em 16/08/2026, foram registrados 50 samples ICMP a cada 100 ms. O RTT médio foi de aproximadamente 333,8 ms, com jitter de 9,28 ms e perda de pacotes de 0%.

Assim, o dataset apresenta dados reais e já estruturados, permitindo iniciar a etapa de preparação dos dados sem a necessidade de configurar uma nova infraestrutura de coleta.


## 3. Opção B — API do RIPE Atlas


- **Documentação consultada (link):** documentação oficial da API REST do RIPE Atlas. (https://atlas.ripe.net/docs/apis/)
- **Autenticação exigida:** consultas podem ser feitas sem autenticação em alguns casos, mas a criação de novas medições exige uma API key com a permissão necessária.
- **Como se cria uma medição:** é realizada uma requisição POST para:

https://atlas.ripe.net/api/v2/measurements/

O corpo da requisição deve informar pelo menos uma definição de medição e uma seleção de probes. Para este projeto, pode ser utilizada uma medição do tipo ping, definindo o destino, a família de endereços e outros parâmetros da coleta.

- **Como se consultam os resultados:** depois de criada a medição, os resultados podem ser obtidos pelo endpoint:

GET /api/v2/measurements/{id}/results/

Também é possível consultar os resultados mais recentes por meio de:

GET /api/v2/measurements/{id}/latest/

Os resultados podem ser filtrados por probe e período. A API também disponibiliza o endpoint ping-stats, que fornece estatísticas de medições de ping em diferentes resoluções, como nativa, hora, dia e semana

**Resumo do que foi encontrado:**

O RIPE Atlas tem uma vantagem: podemos controlar melhor a coleta. É possível escolher os probes, o destino, a quantidade de pacotes, o intervalo entre as medições e outros parâmetros. Dessa forma, conseguimos montar uma coleta mais próxima do que realmente precisamos para o nosso pipeline.

Os resultados de ping também já fornecem informações úteis para as métricas do projeto, como timestamp, número de pacotes enviados e recebidos e valores de RTT. A partir desses dados podemos calcular a latência e a perda e, usando os RTTs de uma janela, calcular o jitter de acordo com a definição adotada pelo projeto.

Por outro lado, utilizar a API exige um pouco mais de configuração. Também existe o sistema de créditos do RIPE Atlas, e as medições consomem créditos de acordo com a quantidade e as características dos testes realizados.

Fontes: documentação oficial do RIPE Atlas sobre autenticação, criação de medições, resultados e créditos.

## 4. Comparação

| Critério | Opção A — Dataset real | Opção B — API RIPE Atlas |
|---|---|---|
| Controle sobre a coleta |Baixo: utiliza medições já realizadas pela Hats Network.|Alto: permite configurar novas medições.|
| Diversidade geográfica |Alta: 20 PoPs em diferentes continentes.|Muito alta: depende dos probes disponíveis e da configuração da medição.|
| Custo / complexidade de implementação |Baixo: os dados já estão coletados e disponíveis em formatos estruturados.|Maior: é necessário utilizar a API e configurar/acompanhar medições.|
| Tempo até os primeiros dados estarem disponíveis |Imediato: os dados já estão disponíveis para download.|Maior: é necessário criar a medição e aguardar sua execução.|

## 5. Recomendação

Recomenda-se a Opção A — Hats Network Global Latency Measurements.

## 6. Justificativa


A Opção A é recomendada porque fornece dados reais de medições ICMP já coletados, incluindo RTT, variação da latência e perda de pacotes, permitindo iniciar a análise imediatamente. Além disso, o dataset possui cobertura geográfica internacional e inclui um ponto de presença em São Paulo, possibilitando análises envolvendo o Brasil. Os dados também são disponibilizados em formatos estruturados e sob uma licença aberta CC BY 4.0.

A principal vantagem em relação à API do RIPE Atlas é a menor complexidade inicial: não é necessário configurar uma infraestrutura de coleta para obter os primeiros dados. Por outro lado, o RIPE Atlas possui como vantagem o maior controle sobre as medições, permitindo definir novos experimentos conforme os objetivos do projeto.

## 7. Riscos e limitações

A principal limitação do dataset é que a equipe não possui controle sobre a coleta original. As medições são realizadas entre os pontos de presença da Hats Network e representam principalmente o comportamento do backbone da própria infraestrutura.

Por isso, um resultado de baixa latência entre dois PoPs não significa necessariamente que um usuário final terá a mesma experiência. A própria Hats Network informa que seus valores representam caminhos entre PoPs do backbone e que a latência percebida pelo usuário também depende da rede de acesso, congestionamento, políticas de roteamento e engenharia de tráfego.

Para reduzir esse problema, a análise deve utilizar diversos pares de origem e destino, diferentes regiões e várias rodadas de medição, evitando conclusões baseadas em apenas uma rota.

Além disso, o modelo deve tratar os dados da Hats como indicadores do comportamento do backbone, e não como uma medição direta da qualidade da conexão de usuários finais.

## 8. Contribuição Individual dos Integrantes


### Integrante 1 — `Lucas Felix Romero`
- **O que fez nesta etapa:** `analise da documentação da API do RIPE Atlas`
- **Tempo dedicado (aprox.):** `55 minutos`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*:
`(https://i.imgur.com/6a0uSMR.png)` 
`(https://i.imgur.com/M1V84yz.png)`

### Integrante 2 — `Pedro Gabriel Castro da Silva`
- **O que fez nesta etapa:** `pesquisa e análise de um dataset real de medições ICMP, avaliando sua origem, formato, métricas e adequação ao projeto.`
- **Tempo dedicado (aprox.):**  `30 minutos `
- **Evidência da contribuição** (print de conversa, rascunho, e-mail, documento compartilhado etc.):
`(https://i.imgur.com/1XGtN0J.png)`
`(https://i.imgur.com/Njhx3gf.png)`

### Integrante 3 — `Luiz Eduardo dos Reis`
- **O que fez nesta etapa:** `montagem e organização do arquivo do memorando, reunindo as informações pesquisadas pelo grupo.`
- **Tempo dedicado (aprox.):** `1h30`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`https://i.imgur.com/4D9DGWz.png` 
`https://i.imgur.com/QJMbANL.png`

### Integrante 4 — `Ricardo Aguilar Arapa`
- **O que fez nesta etapa:** `organização dos prints utilizados como evidências das contribuições dos integrantes.`
- **Tempo dedicado (aprox.):** `30 minutos`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`https://i.imgur.com/cK003xB.png` 
`https://i.imgur.com/kKRhWUc.png`


## 9. Resposta à pergunta técnica

Se a latência entre GRU e JNB estiver boa, mas o usuário estiver tendo muita perda na conexão, a Hats não vai conseguir mostrar esse problema, porque ela mede apenas o caminho entre os PoPs.

Nesse caso, podemos usar os dados da Hats para identificar problemas no backbone, mas precisamos deixar claro que eles não mostram diretamente o que está acontecendo na conexão do usuário.

Se quisermos identificar também problemas na rede de acesso, podemos futuramente complementar o projeto com medições do RIPE Atlas, usando probes mais próximos dos usuários. Assim, seria possível comparar os dados e ter uma ideia melhor de onde está acontecendo a falha.

Por isso, a Hats continua sendo uma boa opção para começar o projeto, mas com essa limitação em mente.

---

## Fontes consultadas/utilizadas como dados para o memorando:

## 1 - Hats Network — Global Network Latency / Open Data (https://hatsnet.io/opendata/latency/)

## 2 - Hats Network — Global Ping & RTT Latency Matrix:(https://hatsnet.io/docs/network/latency)

## 3 - Hats Network — São Paulo → Johannesburg:(https://hatsnet.io/docs/network/latency/pairs/gru-jnb-rtt)

## 4 - RIPE Atlas — APIs:(https://atlas.ripe.net/docs/apis/)

## 5 - RIPE Atlas — Authentication:(https://atlas.ripe.net/docs/apis/rest-api-manual/authentication/)

## 6 - RIPE Atlas — Creating Measurements:(https://atlas.ripe.net/docs/apis/rest-api-manual/measurements/creating-measurements/)

## 7 - RIPE Atlas — Results and Latest:(https://atlas.ripe.net/docs/apis/rest-api-manual/measurements/results-and-latest/)

## 8 - RIPE Atlas — REST API Reference — Results:(https://atlas.ripe.net/docs/apis/rest-api-reference/measurements/measurements_results)

