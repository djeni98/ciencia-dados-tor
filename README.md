# Ciência de Dados
> Trabalho Prático | Tor-nonTor dataset (ISCXTor2016)

Link: https://www.unb.ca/cic/datasets/tor.html

Arquivo usado: SelectedFeatures-10s-TOR-NonTOR.csv

**Objetivo:** A partir de amostras de fluxos de 10 segundos de uma rede encriptada, conseguir classificar esse fluxo como Tor ou Não-Tor.

<details><summary>Colunas: 29</summary>

- Source IP
- Source Port
- Destination IP
- Destination Port
- Protocol
- Flow Duration
- Flow Bytes/s
- Flow Packets/s
- Flow IAT Mean
- Flow IAT Std
- Flow IAT Max
- Flow IAT Min
- Fwd IAT Mean
- Fwd IAT Std
- Fwd IAT Max
- Fwd IAT Min
- Bwd IAT Mean
- Bwd IAT Std
- Bwd IAT Max
- Bwd IAT Min
- Active Mean
- Active Std
- Active Max
- Active Min
- Idle Mean
- Idle Std
- Idle Max
- Idle Min
- label

Legendas:
- Flow IAT: Flow Inter Arrival Time, the time between two packets sent in either direction
- Fwd IAT: Forward Inter Arrival Time, the time between two packets sent forward direction
- Bwd IAT: Backward Inter Arrival Time, the time bettween two packets sent backwards
- Active: The amount of time time a flow was active before going idle
- Idle: The amount of time time a flow was idle before becoming active
</details>

---

**Referência:** Arash Habibi Lashkari, Gerard Draper-Gil, Mohammad Saiful Islam Mamun and Ali A. Ghorbani, "Characterization of Tor Traffic Using Time Based Features", In the proceeding of the 3rd International Conference on Information System Security and Privacy, SCITEPRESS, Porto, Portugal, 2017

## Exploração de Dados

<a href="https://colab.research.google.com/github/djeni98/ciencia-dados-tor/blob/master/exploracao.ipynb" target="_blank"><img alt="Colab - Exploração de Dados" src="https://img.shields.io/badge/Open%20in%20Colab-grey?logo=google-colab" /></a>

Pontos observados na exploração:
- Dados majoritariamente numéricos. Das 29 colunas, 3 são strings: `source_ip`, `destination_ip` e `label`.
- O dataset é mal distribuido

|       | frequência  |porcentagem|
|---|---|---|
|nonTOR |      59784  |   0.881406|
|TOR    |       8044  |   0.118594|


- As colunas de IP (origem e destino) são atributos textuais e acredito que não faça sentido transformá-los em números. O que é possível fazer é substituir essas colunas por um indicativo de endereço ip privado, mas não acho que seja muito produtivo.
- O mesmo vale pras colunas de porta, mesmo diferenciando por portas "bem conhecidas ou de sistema" que ficam no intervalo [0, 1024).
- Como o TOR não usa o protocolo UDP, pode ser interessante usar isso na classificação. A única alteração que precisa ser feita é no tipo que é numérico, mas deveria ser um atributo categórico.
- As colunas de fluxo parecem ser as mais relevantes, exceto as `flow_bytes/s` e `flow_packets/s`. Já as colunas que envolvem o tempo de idle e atividade não aparentam ser relevantes, como mostram os gráficos.

Então, por enquanto:

- **Mantém**

       'protocol', 'flow_duration',
       'flow_iat_mean', 'flow_iat_std', 'flow_iat_max', 'flow_iat_min',
       'fwd_iat_mean', 'fwd_iat_std', 'fwd_iat_max', 'fwd_iat_min',
       'bwd_iat_mean', 'bwd_iat_std', 'bwd_iat_max', 'bwd_iat_min',
       'label'
- **Retira**

        'source_ip', 'source_port', 'destination_ip', 'destination_port',
        'flow_bytes/s', 'flow_packets/s',
        'active_mean', 'active_std', 'active_max', 'active_min',
        'idle_mean', 'idle_std', 'idle_max', 'idle_min'

## Extração de Características

<a href="https://colab.research.google.com/github/djeni98/ciencia-dados-tor/blob/master/extracao_caracteristicas.ipynb" target="_blank"><img alt="Colab - Extração de Características" src="https://img.shields.io/badge/Open%20in%20Colab-grey?logo=google-colab" /></a>

A partir das colunas selecionadas no precesso de exploração, analisei a correlação entre as variáveis. Com isso pude perceber que existiam atributos altamente correlacionados e que poderiam ser ignorados. São eles:

- Flow_iat_mean
- Flow_iat_std
- Flow_iat_max
- Flow_iat_min
- Fwd_iat_mean
- Fwd_iat_std
- Bwd_iat_mean
- Bwd_iat_std

Desta forma, as colunas que vão ser usadas no processo de machine learning são:

       'protocol', 'flow_duration',
       'fwd_iat_max', 'fwd_iat_min',
       'bwd_iat_max', 'bwd_iat_min',
       'label'

## Machine Learning

<a href="https://colab.research.google.com/github/djeni98/ciencia-dados-tor/blob/master/Projeto Final.ipynb" target="_blank"><img alt="Colab - Extração de Características" src="https://img.shields.io/badge/Open%20in%20Colab-grey?logo=google-colab" /></a>

A partir da [especificação](especificacao.md) do projeto final, foram feitos treinamento e testes com 4 modelos diferentes de machine learning: KNN, Random Forest, SVM e Perceptron.

Poucos parâmetros foram alterados em cada modelo, dado que algumas alterações não faziam diferença. No entanto, teve um parâmetro muito importante para melhora do desempenho do SVM, o `class_weight`, pois ele serve justamente para datasets desbalanceados.

### Conclusões

Olhando a grosso modo, o Random Forest parece a melhor solução para classificar tráfegos TOR-nonTOR:
- Tem a melhor precisão
- Menor erro
- Maior área sob a curva ROC.

No entanto, o dataset de teste tem muito mais amostras nonTOR do que TOR, que é justamente onde o Random Forest tem a melhor taxa de acerto.

Logo, num ambiente onde sabemos que o tráfego em sua maioria não usa TOR, o Random Forest é a solução ideal.

Agora, o algoritmo que tem a melhor taxa de acerto nas amostras TOR é o SVM. Dá pra perceber que a sua precisão não foi muito boa, mas acredito que isso seja resultado do dataset desbalanceado.

Outra desvantagem do SVM é o tempo de treino. Portanto, se for necessário ficar retreinando o modelo, talvez seja melhor ficar com Random Forest mesmo.

#### Em Resumo
- Usar o modelo **Random Forest** quando:
    - Se espera acertar mais detecção de tráfego nonTOR
    - Sem tempo para treino e/ou retreino
    - Necessidade de detecção executada na casa de milisegundos
- Usar o modelo **SVM** quando:
    - Se espera acertar mais detecção de tráfego TOR
