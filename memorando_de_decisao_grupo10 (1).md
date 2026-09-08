# Memorando de Decisão — Fonte de Dados do Projeto

| Campo | Informação |
|---|---|
| Curso / Disciplina | `Ciência da Computação / Estrutura de Dados II` |
| Projeto integrador | `[]` |
| Orientador(a) | `Andrea Ono Sakai` |
| Data de entrega desta etapa | `08/09/2026` |
| Integrantes do grupo | `Lucas Felix Romero, Ricardo Aguilar Arapa, Pedro Gabriel Castro da Silva, Luiz Eduardo dos Reis` |


---

> Preencha cada seção com o que você encontrou na pesquisa. Não deixe nenhum campo com o texto entre colchetes — substitua pelo seu conteúdo. Toda informação levantada nas Opções A e B precisa indicar a fonte de onde veio.

## 1. Situação

<!-- Em uma frase: qual decisão precisa ser tomada e por quê. 
O pipeline do projeto já está definido: qualquer fonte de dados precisa produzir registros que se transformem em janelas e, por fim, em X = [latência, perda, jitter]. Falta decidir de onde virão esses dados na próxima fase. A equipe do projeto precisa recomendar, com base em pesquisa e não em preferência pessoal, se a próxima etapa deve usar um dataset real já publicado ou a API do RIPE Atlas. O grupo deve produzir um memorando de decisão com a recomendação da tomada de decisão. A recomendação só tem valor se for sustentada por pesquisa real — não existe resposta pronta para copiar; ela precisa ser construída a partir do que vocês encontraram.
-->

A equipe precisa decidir se, na próxima etapa do projeto, deve utilizar um dataset real de ICMP já publicado ou coletar novos dados por meio da API do RIPE Atlas, buscando a opção que melhor atenda ao pipeline de transformação dos dados em janelas e, depois, em X = latência, perda, jitter.

## 2. Opção A — Dataset real
Origem / link: Hats Network — Global Latency Measurements (https://hatsnet.io/opendata/latency/)
Formato: CSV, JSON e YAML. O repositório também disponibiliza arquivos agregados e um datapackage.json para descrever o conjunto de dados.
Período coberto: o dataset possui lançamentos diários. A versão consultada é v20260808, e a Hats Network informa que uma nova versão completa é publicada diariamente quando há alterações nas medições.
Campos disponíveis: RTT (tempo de ida e volta) das amostras ICMP, origem e destino da medição, além de estatísticas por par, como RTT mínimo, médio e máximo, desvio-padrão, jitter e perda de pacotes.
Licença de uso: Creative Commons Attribution 4.0 International (CC BY 4.0). A licença permite compartilhar e adaptar os dados, desde que seja dado o devido crédito.
Resumo do que foi encontrado:

O dataset Hats Network Global Latency Measurements reúne medições reais de latência entre pontos de presença (PoPs) da rede global da Hats Network. As medições são realizadas utilizando ICMP Echo, com cada par direcionado de pontos de presença sendo testado por 50 requisições ICMP a cada 100 ms. O conjunto possui mais de 342 pares direcionados e cobre 20 pontos de presença distribuídos pela Europa, América do Norte, América do Sul, Ásia-Pacífico e África.

Os dados incluem os valores individuais de RTT e estatísticas resumidas, permitindo analisar latência, variação da latência e perda de pacotes. Como exemplo, na medição entre São Paulo (GRU) e Johannesburg (JNB), realizada em 16/08/2026, foram registrados 50 probes ICMP, com RTT médio de aproximadamente 333,8 ms no sentido São Paulo → Johannesburg e perda de aproximadamente 0,04%.

Os dados são disponibilizados publicamente sob a licença CC BY 4.0.

**Resumo do que foi encontrado:**

[Escreva aqui, citando a fonte consultada]

## 3. Opção B — API do RIPE Atlas

<!-- O que foi encontrado sobre a API: autenticação, criação e consulta de medições. Cite a fonte de cada informação. -->

- **Documentação consultada (link):** documentação oficial da API REST do RIPE Atlas. (https://atlas.ripe.net/docs/apis/)
- **Autenticação exigida:** consultas podem ser feitas sem autenticação em alguns casos, mas a criação de novas medições exige uma API key com a permissão necessária.
- **Como se cria uma medição:** é feita uma requisição POST para a API, informando a configuração da medição, os probes que serão utilizados e outros parâmetros. Para o nosso caso, é possível criar uma medição do tipo ping, baseada em ICMP.
- **Como se consultam os resultados:** depois que a medição é criada, os resultados podem ser consultados pelo endpoint de resultados da medição. Também é possível consultar os resultados mais recentes e filtrar por probe e período.

**Resumo do que foi encontrado:**

O RIPE Atlas tem uma vantagem: podemos controlar melhor a coleta. É possível escolher os probes, o destino, a quantidade de pacotes, o intervalo entre as medições e outros parâmetros. Dessa forma, conseguimos montar uma coleta mais próxima do que realmente precisamos para o nosso pipeline.

Os resultados de ping também já fornecem informações úteis para as métricas do projeto, como timestamp, número de pacotes enviados e recebidos e valores de RTT. A partir desses dados podemos calcular a latência e a perda e, usando os RTTs de uma janela, calcular o jitter de acordo com a definição adotada pelo projeto.

Por outro lado, utilizar a API exige um pouco mais de configuração. Também existe o sistema de créditos do RIPE Atlas, e as medições consomem créditos de acordo com a quantidade e as características dos testes realizados.

Fontes: documentação oficial do RIPE Atlas sobre autenticação, criação de medições, resultados e créditos.

## 4. Comparação

<!-- Preencha a tabela com base no que você levantou nas seções 2 e 3. -->

| Critério | Opção A — Dataset real | Opção B — API RIPE Atlas |
|---|---|---|
| Controle sobre a coleta |Baixo: utiliza medições já realizadas pela Hats Network.|Alto: permite configurar novas medições.|
| Diversidade geográfica |Alta: 20 PoPs em diferentes continentes.|Muito alta: depende dos probes disponíveis e da configuração da medição.|
| Custo / complexidade de implementação |Baixo: os dados já estão coletados e disponíveis em formatos estruturados.|Maior: é necessário utilizar a API e configurar/acompanhar medições.|
| Tempo até os primeiros dados estarem disponíveis |Imediato: os dados já estão disponíveis para download.|Maior: é necessário criar a medição e aguardar sua execução.|

## 5. Recomendação

<!-- Uma frase direta: qual opção você recomenda. -->

Recomenda-se a Opção A — Hats Network Global Latency Measurements.

## 6. Justificativa

<!-- Por que essa opção vence a outra, com base nas evidências das seções 2, 3 e 4 — não em preferência pessoal. -->

A Opção A é recomendada porque fornece dados reais de medições ICMP já coletados, incluindo RTT, variação da latência e perda de pacotes, permitindo iniciar a análise imediatamente. Além disso, o dataset possui cobertura geográfica internacional e inclui um ponto de presença em São Paulo, possibilitando análises envolvendo o Brasil. Os dados também são disponibilizados em formatos estruturados e sob uma licença aberta CC BY 4.0.

A principal vantagem em relação à API do RIPE Atlas é a menor complexidade inicial: não é necessário configurar uma infraestrutura de coleta para obter os primeiros dados. Por outro lado, o RIPE Atlas possui como vantagem o maior controle sobre as medições, permitindo definir novos experimentos conforme os objetivos do projeto.

## 7. Riscos e limitações

<!-- O que pode dar errado com a opção escolhida, e como isso poderia ser mitigado. -->

Uma limitação do dataset é que não temos controle sobre a coleta original. As medições são realizadas entre os pontos de presença da Hats Network e representam principalmente o comportamento da rede backbone da própria infraestrutura. Portanto, os resultados não devem ser interpretados como uma representação direta da experiência de todos os usuários da Internet. A própria documentação ressalta que o RTT publicado representa o comportamento do caminho de backbone e que a latência percebida pelo usuário também depende de fatores como rede de acesso, congestionamento, roteamento e engenharia de tráfego.

Para reduzir esse problema, a análise pode utilizar diversos pares de origem e destino, diferentes regiões e múltiplas rodadas de medição, evitando conclusões baseadas em apenas uma rota.

## 8. Contribuição Individual dos Integrantes

<!-- cada integrante deve descrever, com suas próprias palavras, o que efetivamente fez nesta etapa. Contribuições genéricas como "ajudei em tudo" não serão aceitas. Use verbos de ação e seja específico (ex.: "pesquisei , analisei, testei, ... apresentei prós/contras ao grupo, ...").-->

### Integrante 1 — `Lucas Felix Romero`
- **O que fez nesta etapa:** `analisei a documentação da API do RIPE Atlas`
- **Tempo dedicado (aprox.):** `55 minutos`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*:
`(https://i.imgur.com/6a0uSMR.png)` 
`(https://i.imgur.com/M1V84yz.png)`

### Integrante 2 — `Pedro Gabriel Castro da Silva`
- **O que fez nesta etapa:** realizei uma pesquisa de um data set real
- **Tempo dedicado (aprox.):**  `14 minutos `
- **Evidência da contribuição** (print de conversa, rascunho, e-mail, documento compartilhado etc.):
`(https://i.imgur.com/1XGtN0J.png)`
`(https://i.imgur.com/Njhx3gf.png)`

### Integrante 3 — `[Escreva nome completo do aluno ]`
- **O que fez nesta etapa:** `[]`
- **Tempo dedicado (aprox.):** `[ex.: 3h30]`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`[]` 
`[]`

### Integrante 4 — `[Escreva nome completo do aluno ]`
- **O que fez nesta etapa:** `[]`
- **Tempo dedicado (aprox.):** `[ex.: 3h30]`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`[]` 
`[]`

### Integrante 5 — `[Escreva nome completo do aluno ]`
- **O que fez nesta etapa:** `[]`
- **Tempo dedicado (aprox.):** `[ex.: 3h30]`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`[]` 
`[]`

### Integrante 6 — `[Escreva nome completo do aluno ]`
- **O que fez nesta etapa:** `[]`
- **Tempo dedicado (aprox.):** `[ex.: 3h30]`
- **Evidência da contribuição** *(print de conversa, rascunho, e-mail, documento compartilhado etc.)*: 
`[]` 
`[]`

---

## Fontes consultadas

<!-- Mínimo de 3 fontes. Liste todas as páginas de documentação, artigos ou repositórios usados. -->

1. (hatsnet.io/opendata/latency/)
2. (https://atlas.ripe.net/docs/)
3. (https://atlas.ripe.net/docs/getting-started/what-is-ripe-atlas)
4. (https://atlas.ripe.net/docs/getting-started/credits)
