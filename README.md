# Network Failure Predictor

**Nome do projeto:** Network Failure Predictor

## Objetivo

O objetivo deste projeto é desenvolver um sistema capaz de analisar dados relacionados ao envio de pacotes ICMP e utilizar essas informações para identificar ou prever possíveis falhas no envio dos pacotes.


## Descrição do Projeto

O projeto tem como foco o monitoramento da comunicação de rede por meio de pacotes ICMP (Internet Control Message Protocol).

A partir dos dados coletados, serão analisadas características como latência, perda de pacotes e jitter, que serão utilizadas como informações para o desenvolvimento do preditor de falhas.

A proposta é transformar os dados de rede em um formato adequado para análise e, posteriormente, utilizar técnicas de aprendizado de máquina para identificar padrões que possam indicar possíveis problemas ou falhas no envio dos pacotes.

## Integrantes do Grupo

* Lucas Felix Romero -- 41969286		
* Pedro Gabriel Castro da Silva -- 42832152	
* Ricardo Aguilar Arapa -- 42628156
* Luiz Eduardo dos Reis -- 42973759
* Rodrigo Camargo Vieira

## Informações da Entrega

* **Turma:** Ciência da Computação 4° Semestre Periodo Noturno
* **Link do repositório:** https://github.com/LucasFR-BRZ/Projeto-Preditor-de-Falhas
* **Branch principal utilizada:** main

## Estrutura do Projeto

A organização do projeto está dividida da seguinte forma:

📁 Projeto-Preditor-de-Falhas
├── 📁 Dados brutos(CSV, JSON, MetaDados)
│   ├── 📄 ripe_atlas_m210821089_20261004T140510Z.csv
│   ├── 📄 ripe_atlas_m210821089_20261004T140510Z.json
│   └── 📄 ripe_atlas_m210821089_20261004T140510Z_metadata.json
├── 📁 Doc
│   └── 📄 memorando_de_decisao_grupo10_2.md
├── 📁 Notebooks
│   └── 📄 Coleta_de_dados_corrigido.ipynb
└── 📄 README.md

## Tecnologias Utilizadas

* API RIPE do Atlas
* Google Collab
* Visual Studio Code

## Observações

* O repositório deve estar acessível para consulta.
* Todos os integrantes do grupo devem estar identificados neste README.
* Os arquivos e códigos desenvolvidos até o momento devem estar disponíveis no repositório.
* As pastas e arquivos devem permanecer organizados
* Não devem ser incluídas senhas, tokens, chaves de API ou outros dados confidenciais no repositório.
