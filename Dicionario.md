
# Dicionário de Dados — Preditor de Falhas de Rede

## 1. Objetivo

Este documento descreve a origem dos dados, as medições selecionadas, os campos utilizados e as abreviações presentes nos registros coletados para o projeto de predição de falhas de rede.

## 2. Fonte dos dados

Os dados são obtidos por meio do [RIPE Atlas](https://atlas.ripe.net/), uma plataforma que realiza medições de conectividade e desempenho da internet por meio de sondas distribuídas geograficamente.

As medições selecionadas utilizam testes do tipo `ping`, baseados no protocolo ICMP, para registrar os tempos de resposta entre sondas e endereços IP de destino.

## 3. Medições selecionadas

| ID da medição (`msm_id`) | Origem informada | Destino informado | IP de destino |
|---|---|---|---|
| `11688943` | Finlândia | Porto Alegre, Brasil | `200.132.1.32` |
| `203894195` | Finlândia | Paris, França | `82.66.27.208` |
| `192385276` | Finlândia | Zug, Suíça | `193.134.29.16` |
| `146202339` | França | Düsseldorf, Alemanha | `91.207.13.233` |
| `209654486` | Finlândia | Umm ash Sha'um, Bahrein | `37.131.15.98` |
| `112718717` | Finlândia | Buenos Aires, Argentina | `200.9.157.207` |
| `100423378` | Finlândia | Santa Cruz, Costa Rica | `200.5.63.109` |
| `160208095` | Finlândia | Ostend, Alemanha* | `80.243.198.243` |

> **Nota:** as localidades de destino foram registradas conforme as informações fornecidas e ainda precisam de confirmação independente. Ostend é conhecida como uma cidade da Bélgica; por isso, a última associação deve ser revisada.

## 4. Dicionário dos campos das medições

| Campo | Descrição |
|---|---|
| `msm_id` | Identificador da medição no RIPE Atlas. |
| `prb_id` | Identificador da sonda que realizou a medição. |
| `src_addr` | Endereço IP de origem da sonda. |
| `dst_addr` | Endereço IP do destino testado. |
| `dst_name` | Nome ou endereço associado ao destino. |
| `proto` | Protocolo utilizado na medição, como ICMP. |
| `type` | Tipo de medição, como `ping`. |
| `timestamp` | Data e hora do registro em formato Unix timestamp. |
| `avg` | Tempo médio de resposta, em milissegundos. |
| `min` | Menor tempo de resposta registrado, em milissegundos. |
| `max` | Maior tempo de resposta registrado, em milissegundos. |
| `sent` | Quantidade de pacotes enviados. |
| `rcvd` | Quantidade de pacotes recebidos. |
| `result` | Lista de resultados individuais, que pode conter os tempos de resposta (`rtt`). |
| `ttl` | Campo do pacote que limita seu tempo de permanência na rede por meio de saltos. |
| `size` | Tamanho do pacote, em bytes. |
| `dup` | Quantidade de respostas duplicadas registradas. |
| `step` | Intervalo programado entre as medições, em segundos. |
| `group_id` | Identificador do grupo associado à medição, quando fornecido. |
| `stored_timestamp` | Data e hora em que o resultado foi armazenado, em formato Unix timestamp. |

## 5. Dicionário dos metadados das sondas

| Campo | Descrição |
|---|---|
| `address_v4` | Endereço IPv4 cadastrado para a sonda. |
| `address_v6` | Endereço IPv6 cadastrado para a sonda, quando disponível. |
| `asn_v4` | Número do Sistema Autônomo associado à conexão IPv4. |
| `asn_v6` | Número do Sistema Autônomo associado à conexão IPv6, quando disponível. |
| `country_code` | Código de duas letras do país associado à sonda. |
| `description` | Descrição da rede ou organização associada à sonda. |
| `firmware_version` | Versão do firmware da sonda. |
| `first_connected` | Data do primeiro registro de conexão da sonda, em formato Unix timestamp. |
| `geometry` | Estrutura que contém as coordenadas geográficas cadastradas da sonda. |

### 5.1. Coordenadas geográficas

O campo `geometry` pode conter as coordenadas no formato:

`[longitude, latitude]`

A ordem é importante: o primeiro valor representa a longitude e o segundo representa a latitude.

As coordenadas correspondem à localização cadastrada da sonda e não garantem a localização física exata do equipamento.

## 6. Abreviações e termos técnicos

| Termo | Significado |
|---|---|
| IP | *Internet Protocol*: protocolo responsável pelo endereçamento e encaminhamento de pacotes IP. |
| IPv4 | Quarta versão do protocolo IP. |
| IPv6 | Sexta versão do protocolo IP. |
| ICMP | *Internet Control Message Protocol*: protocolo utilizado, entre outras funções, em testes de ping. |
| RTT | *Round-Trip Time*: tempo de ida de uma mensagem até o destino e de retorno da resposta. |
| ASN | *Autonomous System Number*: identificador numérico de um Sistema Autônomo na internet. |
| TTL | *Time To Live*: campo utilizado para limitar a quantidade de saltos de um pacote. |
| JSON | Formato estruturado de dados utilizado para armazenamento e transmissão de informações. |
| Unix timestamp | Representação numérica de data e hora a partir de 1º de janeiro de 1970, em UTC. |
| Sonda (*probe*) | Equipamento ou agente que executa medições de rede na plataforma RIPE Atlas. |
| Latência | Tempo necessário para que uma mensagem percorra a rede até o destino e a resposta retorne, conforme a medição utilizada. |
| Perda de pacotes | Situação em que pacotes enviados não recebem resposta ou não chegam ao destino esperado. |

## 7. Organização geográfica

Para comparar as medições por localização, podem ser associados os seguintes campos a cada registro:

- País de origem.
- Continente de origem.
- País de destino.
- Continente de destino.
- Endereço IP de origem.
- Endereço IP de destino.

A localização da origem pode ser obtida por meio dos metadados da sonda no RIPE Atlas. A localização do destino deve ser verificada por fontes adequadas, como informações de rede e serviços de geolocalização de IP.

A localização geográfica aproximada de um IP não comprova, por si só, o caminho percorrido pelos pacotes. Para investigar os roteadores intermediários, podem ser necessárias medições adicionais, como `traceroute`.

## 8. Finalidade dos dados no projeto

Os dados serão utilizados para investigar possíveis relações entre características das conexões e a ocorrência de problemas de rede.

Entre as variáveis que podem ser analisadas estão:

- Tempo médio de resposta (`avg`).
- Menor e maior tempo de resposta (`min` e `max`).
- Quantidade de pacotes enviados e recebidos (`sent` e `rcvd`).
- Resultados individuais de RTT.
- Horários das medições.
- Países e continentes associados à origem e ao destino.

A localização geográfica poderá ser utilizada para agrupar e comparar medições realizadas entre diferentes regiões.

Entretanto, uma distância geográfica maior não implica necessariamente maior latência ou ocorrência de falhas. O desempenho também depende de fatores como roteamento, congestionamento e infraestrutura de rede.

Da mesma forma, um tempo de resposta elevado, isoladamente, não comprova uma falha. Os critérios de classificação deverão ser definidos durante o desenvolvimento do projeto.

## 9. Observações sobre a qualidade dos dados

1. Os identificadores `msm_id` e `prb_id` representam entidades diferentes: o primeiro identifica a medição e o segundo identifica a sonda.
2. Uma mesma medição pode apresentar resultados de várias sondas.
3. Os metadados de uma sonda podem se repetir em diversos registros, pois a mesma sonda pode executar várias medições.
4. As localidades dos destinos devem ser verificadas antes de serem consideradas definitivas.
5. Os dados originais devem ser preservados, mantendo separadas as informações coletadas e as informações geográficas acrescentadas posteriormente.
