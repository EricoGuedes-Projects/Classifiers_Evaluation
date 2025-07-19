## Explicação do Código

O notebook inicia importando as bibliotecas essenciais para manipulação de dados (`pandas`, `numpy`), visualização (`matplotlib`, `seaborn`) e modelagem (`scikit‑learn`). Em seguida, a base de dados de partidas de League of Legends é carregada e as colunas irrelevantes (como o identificador da partida) são descartadas.

1. **Pré‑processamento**  
   - Codificação de variáveis categóricas (por exemplo, time vencedor).  
   - Padronização das características numéricas para média zero e variância unitária.  
   - Separação das tarefas (I a V) conforme os subconjuntos de atributos usados (pré‑jogo, 10 min, 15 min).

2. **Definição dos Classificadores**  
   - Quatro modelos do scikit‑learn são configurados:  
     - Árvore de Decisão (DT)  
     - k‑Nearest Neighbors (kNN)  
     - Perceptron Multicamadas (MLP)  
     - Floresta Aleatória (RF)  
   - O ensemble **Heterogeneous Boosting (HB)** é implementado como classe Python própria:  
     - Recebe parâmetro `n_estimators`  
     - Em cada iteração, amostra com reposição via roleta e treina quatro candidatos (NB, DT, MLP, kNN)  
     - Escolhe o melhor classificador para compor o ensemble e ajusta pesos de exemplos mal classificados  
     - No método `predict`, consolida as predições por votação majoritária com critério de desempate.

3. **Validação Cruzada Aninhada**  
   - Loop externo de teste com 10 folds (`RepeatedStratifiedKFold`)  
   - Loop interno de validação com 4 folds para busca em grade de hiperparâmetros  
   - Grade de parâmetros específica para cada modelo (por exemplo, profundidades no DT, número de vizinhos no kNN, tamanhos de camada no MLP, etc.).

4. **Métricas e Estatísticas**  
   - Cálculo da **acurácia** em cada repetição de teste.  
   - Extração da **média**, **desvio‑padrão** e **intervalo de confiança** a 95% para cada método.  
   - Implementação do **teste t corrigido** (Nadeau & Bengio) e do **teste de Wilcoxon** para comparações pareadas entre todos os pares de classificadores.

5. **Visualizações**  
   - Geração de **boxplots** das distribuições de acurácia por método usando Seaborn.  
   - Montagem de tabelas com os resultados estatísticos e destaques em negrito para diferenças significativas.

---

## Resultados

- **Desempenho Médio**  
  Todos os classificadores apresentaram acurácias elevadas (> 90%), com a Floresta Aleatória geralmente liderando em média, seguida por MLP e Heterogeneous Boosting.

- **Variabilidade**  
  O boxplot revelou que o HB tende a ter menor variabilidade entre os folds, indicando maior consistência, embora sua média possa ficar ligeiramente abaixo do RF em alguns cenários.

- **Intervalos de Confiança**  
  As bandas de 95% mostram que, apesar da sobreposição em muitos casos, o RF mantém um limite inferior superior ao dos demais em tarefas I e III.

- **Comparações Estatísticas**  
  - O **teste t corrigido** identificou diferença significativa entre RF e os métodos mais simples (DT, kNN) em pelo menos dois cenários, com *p* < 0.05 (negrito na tabela).  
  - O **Wilcoxon** confirmou essas diferenças de forma não paramétrica, validando a superioridade estatística do RF.  
  - Entre MLP e HB, as diferenças não foram consistentes — ambos métodos se mostraram estatisticamente equivalentes em várias tarefas.

- **Conclusão Parcial**  
  A Floresta Aleatória destacou‑se como o classificador individual mais robusto, mas o Heterogeneous Boosting demonstrou boa consistência e constituiu uma alternativa viável quando se busca reduzir a variabilidade dos resultados.
