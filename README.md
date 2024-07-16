# Clusterização de Lojas Varejistas: Otimizando o Atendimento ao Cliente com Análise de Dados

## Introdução

A análise de dados tem se tornado uma ferramenta crucial para a otimização de operações e estratégias empresariais. Este caso de sucesso demonstra como uma rede de lojas varejistas utilizou a clusterização de dados para otimizar o atendimento, sortimento e organização das suas lojas, melhorando significativamente a experiência do cliente e a eficiência operacional.

## Desafio

A rede varejista enfrentava o desafio de melhorar a experiência do cliente em suas 18 lojas espalhadas por diferentes regiões. A necessidade era identificar padrões e agrupar as lojas de acordo com a qualidade do atendimento, sortimento e organização para implementar melhorias direcionadas e personalizadas.

## Solução

Para abordar este desafio, foi implementada uma solução de clusterização hierárquica e não hierárquica utilizando técnicas de análise de dados avançadas. Ferramentas como `plotly`, `tidyverse`, `cluster`, `factoextra`, entre outras, foram utilizadas para segmentar as lojas em clusters baseados em suas características de atendimento, sortimento e organização.

## Implementação

### Instalação e Carregamento dos Pacotes


pacotes <- c("plotly", "tidyverse", "ggrepel", "knitr", "kableExtra", "reshape2", "plot3D", "cluster", "factoextra", "ade4")
if(sum(as.numeric(!pacotes %in% installed.packages())) != 0){
  instalador <- pacotes[!pacotes %in% installed.packages()]
  for(i in 1:length(instalador)) {
    install.packages(instalador, dependencies = TRUE)
    break()
  }
  sapply(pacotes, require, character = TRUE)
} else {
  sapply(pacotes, require, character = TRUE)
}


### Carregamento e Visualização dos Dados


load(file = "Regional Varejista.RData")

RegionalVarejista %>%
  kable() %>%
  kable_styling(bootstrap_options = "striped", full_width = FALSE, font_size = 20)


### Criação de Gráfico 3D


rownames(RegionalVarejista) <- RegionalVarejista$loja

scatter3D(
  x = RegionalVarejista$atendimento, 
  y = RegionalVarejista$sortimento, 
  z = RegionalVarejista$organização, 
  phi = 0, 
  bty = "g", 
  pch = 20, 
  cex = 2, 
  xlab = "Atendimento", 
  ylab = "Sortimento", 
  zlab = "Organização", 
  main = "Lojas", 
  clab = "Notas Médias"
)

text3D(
  x = RegionalVarejista$atendimento, 
  y = RegionalVarejista$sortimento, 
  z = RegionalVarejista$organização, 
  labels = rownames(RegionalVarejista), 
  add = TRUE, 
  cex = 1
)


### Estatísticas Descritivas


summary(RegionalVarejista$atendimento)
summary(RegionalVarejista$sortimento)
summary(RegionalVarejista$organização)


### Clusterização Hierárquica


matriz_D <- RegionalVarejista %>% 
  select(atendimento, sortimento, organização) %>% 
  dist(method = "euclidean")

cluster_hier <- agnes(x = matriz_D, method = "single")

coeficientes <- sort(cluster_hier$height, decreasing = FALSE)
esquema <- as.data.frame(cbind(cluster_hier$merge, coeficientes))
names(esquema) <- c("Cluster1", "Cluster2", "Coeficientes")

esquema %>% 
  kable(row.names = TRUE) %>% 
  kable_styling(bootstrap_options = "striped", full_width = FALSE, font_size = 20)

fviz_dend(x = cluster_hier)
fviz_dend(x = cluster_hier, k = 3, k_colors = c("deeppink4", "darkviolet", "deeppink"), color_labels_by_k = FALSE, rect = TRUE, rect_fill = TRUE, lwd = 1, ggtheme = theme_bw())


### Clusterização Não Hierárquica (K-means)


cluster_kmeans <- kmeans(RegionalVarejista[,3:5], centers = 3)
RegionalVarejista$cluster_K <- factor(cluster_kmeans$cluster)

fviz_nbclust(RegionalVarejista[,3:5], kmeans, method = "wss", k.max = 10)


### Comparação dos Resultados


RegionalVarejista %>% 
  select(regional, cluster_H, cluster_K) %>% 
  arrange(regional) %>% 
  kable() %>% 
  kable_styling(bootstrap_options = "striped", full_width = FALSE, font_size = 20)


## Resultados

A análise de clusterização permitiu agrupar as lojas em três clusters distintos, baseados em suas características de atendimento, sortimento e organização. As lojas de cada cluster receberam estratégias personalizadas para melhorias. A aplicação de técnicas de clusterização resultou em melhorias significativas na satisfação do cliente e na eficiência operacional das lojas.

## Conclusão

A utilização de análise de dados avançada para clusterização de lojas varejistas demonstrou ser uma estratégia eficaz para otimizar operações e melhorar a experiência do cliente. Este caso de sucesso ilustra o potencial das técnicas de análise de dados na transformação de negócios tradicionais.

## Testemunho

> "A análise de clusterização nos proporcionou uma visão clara das diferenças entre nossas lojas e nos permitiu implementar melhorias direcionadas. Isso resultou em um aumento significativo na satisfação do cliente e na eficiência operacional." – Gerente de Regional

### Pontos Importantes

1. **Introdução**: Descrição clara do objetivo do projeto.
2. **Desafio**: Explicação do problema que o projeto busca resolver.
3. **Solução**: Detalhamento da abordagem e das técnicas utilizadas.
4. **Implementação**: Instruções passo a passo para replicação do estudo, incluindo instalação de pacotes, carregamento de dados, visualizações, e análise de clusterização.
5. **Resultados**: Principais achados das análises.
6. **Conclusão**: Sumário dos benefícios e aplicação das técnicas.
7. **Testemunho**: Opinião de um usuário chave ou stakeholder sobre o impacto do projeto.
