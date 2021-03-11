# Projeto Final
1. Separar seu dataset em 2 porções (80% e 20%)
2. Mostrar a distribuição de amostras por classe visualmente (gerar gráfico) para ambas as porções
3. Criar modelos usando no mínimo: KNN, Random Forest e um tipo de rede neural a sua escolha (GRUPOS CUJO DATASET É TEXTUAL: usar TF-IDF e Word2Vec) 
    - Um treinamento deve ser feito com a porção 1 do dataset (80%). Nesta porção, use divisão 80/20 (percentage split) para treino/teste, ou seja, **80% dos 80% para treinamento e 20% dos 80% para teste.**
    - O relatório deve listar as configurações de cada algoritmo (tuning, poda, vizinhos, limiares, neurônios/camadas, etc.)
    - Outro treinamento/teste deve ser feito usando validação cruzada com 5 pastas (k-fold cross validation com k = 5). Este treinamento usa a mesma porção de 80% das amostras separadas no item 1.
    -  Apresentar a matriz de confusão para ambos os treinamentos (split percentage e k-fold cross validation), bem como os valores obtidos de precisão e erro (_mean_absolute_error_) de ambos os treinamentos. **Para o treinamento com _percentage split_, haverá 1 matriz de confusão, 1 precisão e 1 erro por algoritmo. Para o treinamento com k-fold cross validation haverá 5 de cada (matriz, precisão, erro) por algoritmo.**
    - Apresentar duas figuras por algoritmo: a curva ROC para os modelos gerados individualmente (**1 por algoritmo no _percentage split_**) e as curvas ROC da k-fold cross-validation (**5 curvas por algoritmo, traçadas na mesma figura**)
4. Utilizar a **porção 2** do dataset (20%) como “dados de produção não-rotulados” e refazer os passos 2 (somente teste dos modelos) e 3 acima listados.
