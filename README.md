# Ciência de Dados
> Trabalho Prático | Tor-nonTor dataset (ISCXTor2016)

Link: https://www.unb.ca/cic/datasets/tor.html

Arquivo usado: SelectedFeatures-10s-TOR-NonTOR.csv

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

### 1. Que tipo de dados você tem, majoritariamente (atributos numéricos, textuais)?
Numéricos. Das 29 colunas, 3 são strings: `source_ip`, `destination_ip` e `label`.

### 2. Qual seu objetivo com esse dataset?
A partir de amostras de fluxos de 10 segundos de uma rede encriptada, conseguir classificar esse fluxo como Tor ou Não-Tor.

### 3. Seu dataset é rotulado de que maneira?
nonTOR, TOR

### 4. Como é a distribuição dos dados do dataset?
|       | frequência  |porcentagem|
|---|---|---|
|nonTOR |      59784  |   0.881406|
|TOR    |       8044  |   0.118594|


### 5. Quais colunas/atributos você julga ser interessante manter e remover? Por quê?

As colunas de IP (origem e destino) são atributos textuais e acredito que não faça sentido transformá-los em números. O que é possível fazer é substituir essas colunas por um indicativo de endereço ip privado, mas não acho que seja muito produtivo.

O mesmo vale pras colunas de porta, mesmo diferenciando por portas "bem conhecidas ou de sistema" que ficam no intervalo [0, 1024).

Como o TOR não usa o protocolo UDP, pode ser interessante usar isso na classificação. A única alteração que precisa ser feita é no tipo que é numérico, mas deveria ser um atributo categórico.

As colunas de fluxo parecem ser as mais relevantes, exceto as `flow_bytes/s` e `flow_packets/s`. Já as colunas que envolvem o tempo de idle e atividade não aparentam ser relevantes, como mostram os gráficos.

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
