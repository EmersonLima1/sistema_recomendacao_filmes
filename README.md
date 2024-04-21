# Sistema de Recomendação de Filmes

![](https://github.com/EmersonLima1/sistema_recomendacao_filmes/blob/main/imagem.jpg)

<div align="justify">

Este é um sistema de recomendação de filmes baseado em conteúdo, que utiliza técnicas de processamento de linguagem natural (NLP) e similaridade de cosseno para sugerir filmes semelhantes com base em uma entrada fornecida pelo usuário.

## Introdução

Este sistema de recomendação foi desenvolvido utilizando um conjunto de dados do TMDb (The Movie Database) obtido no [Kaggle](https://www.kaggle.com/datasets/asaniczka/tmdb-movies-dataset-2023-930k-movies/data). O conjunto de dados contém informações detalhadas sobre mais de 1.000.000 de filmes, incluindo títulos, avaliações, datas de lançamento, receitas, gêneros e mais. A recomendação de filmes é feita com base nas seguintes características de cada filme:

 - Overview: Breve descrição ou resumo do filme.
 - Genres: Gênero(s) do filme.
 - Keywords: Palavras-chave associadas ao filme.

## Pré-processamento dos dados

Os dados foram pré-processados antes de construir o sistema de recomendação. Isso incluiu a seleção das colunas relevantes e a limpeza de valores ausentes. Durante o pré-processamento, foi selecionado apenas as colunas relevantes ('title', 'overview', 'genres', 'keywords') e foi removido as linhas que continham valores ausentes na coluna 'overview', pois essa é uma informação crítica para a recomendação. Em seguida, as informações de `'overview'`, `'genres'` e `'keywords'` foram combinadas em uma única coluna chamada `'combined_features'`. Essa coluna combinada é importante porque captura várias facetas do conteúdo de cada filme em uma única representação.

## Matriz TF-IDF

Para representar o conteúdo textual dos filmes, utilizei a técnica TF-IDF, abreviação de Term Frequency-Inverse Document Frequency (Frequência do Termo-Inverso da Frequência nos Documentos). Esse é uma métrica estatística utilizada para avaliar a relevância de uma palavra em um documento em relação a uma coleção de documentos ou corpus linguístico. É comumente empregada como um fator de ponderação em tarefas de recuperação de informações e mineração de dados.

O valor TF-IDF de uma palavra aumenta à medida que sua frequência de ocorrência dentro de um documento aumenta, porém, é ajustado pela frequência da palavra em todo o corpus. Esse ajuste auxilia na diferenciação entre ocorrências comuns e raras de palavras, garantindo uma representação mais precisa da importância de cada termo.

- Term Frequency (TF): Mede a frequência de uma palavra em um documento. É calculada como o número de vezes que a palavra aparece no documento dividido pelo número total de palavras no documento.
  - A ideia básica por trás da Frequência do Termo é que quanto mais uma palavra específica aparece em um documento, mais relevante ela pode ser para o conteúdo desse documento. Hans Peter Luhn (pesquisador alemão no campo da ciência da computação) foi um dos primeiros a propor essa ideia e ele afirmou que o peso de um termo em um documento é diretamente proporcional à sua frequência.
- Inverse Document Frequency (IDF): Mede a raridade de uma palavra em uma coleção de documentos. É calculada como o logaritmo do número total de documentos na coleção dividido pelo número de documentos que contêm a palavra.
  - Em vez de dar peso igual a todas as palavras, é reduzido o peso das palavras muito comuns que aparecem em muitos documentos e aumentamos o peso das palavras mais raras que aparecem em poucos documentos. Karen Spärck Jones (cientista da computação britânica) propôs uma interpretação estatística do IDF, sugerindo que a especificidade de um termo pode ser medida pela frequência inversa com que ele aparece em documentos. Quanto menos documentos contêm um termo específico, mais importante ele pode ser na diferenciação entre documentos.

A matriz TF-IDF foi criada usando a classe `TfidfVectorizer` do scikit-learn. Esta classe converte a coleção de documentos de texto (no caso, a `combined_features`) em uma matriz de recursos TF-IDF.

Esta classe realiza várias etapas essenciais automaticamente:

- Tokenização: Divide o texto em palavras ou termos individuais, chamados de "tokens".
- Remoção de Stop Words: Elimina palavras comuns que não contribuem significativamente para o conteúdo do texto. Exemplos de stop words em inglês incluem "is", "it", "the", "and", "or", "of", "in", entre outras.
- Cálculo da Frequência do Termo (TF): Conta quantas vezes cada palavra aparece em cada documento.
- Cálculo do Inverso da Frequência nos Documentos (IDF): Calcula a raridade de cada palavra em relação a toda a coleção de documentos.
- Cálculo do TF-IDF: Combina TF e IDF para calcular a importância de cada palavra em cada documento e em toda a coleção.

#### Fit e Transform:

- Primeiro, instanciei o TfidfVectorizer e especifiquei que desejo remover as stop words em inglês.
- Em seguida, foi utilizado o método fit_transform para ajustar o vetorizador aos dados e transformar a coluna combinada de texto em uma matriz TF-IDF.
- Durante este processo, o vetorizador aprende o vocabulário (conjunto de todas as palavras únicas nos documentos) e calcula os valores TF-IDF para cada palavra em cada documento.

Essa abordagem garante que as palavras mais significativas sejam enfatizadas na representação final dos documentos, enquanto palavras comuns e menos relevantes sejam descartadas, contribuindo para recomendações mais precisas e relevantes.

## Similaridade de Cosseno

A similaridade de cosseno é uma medida de similaridade entre dois vetores que mede o cosseno do ângulo entre eles. Quanto mais próximos os vetores estiverem na direção, maior será o valor do cosseno e, portanto, maior será a similaridade entre eles.

Por exemplo, se tivermos dois vetores A e B, podemos calcular a similaridade de cosseno entre eles usando a seguinte fórmula: $\frac{A⋅B}{∥A∥×∥B∥​}$

Onde:

- $A⋅B$ é o produto escalar entre os vetores A e B.
- $∥A∥$ e $∥B∥$ são as magnitudes dos vetores A e B, respectivamente.

Quanto mais próximo o valor da similaridade de cosseno estiver de 1, mais similares os vetores (e, por extensão, os documentos representados por esses vetores) estão entre si.

#### Uso em Conjunto com a Matriz TF-IDF

Na função obter_recomendacoes, a similaridade de cosseno é calculada entre o filme selecionado (representado pelo seu vetor na matriz TF-IDF) e todos os outros filmes na coleção (também representados pelos seus vetores na matriz TF-IDF).

- Primeiro, é encontrado o índice do filme selecionado no DataFrame df.
- Em seguida, é calculada a similaridade de cosseno entre o vetor do filme selecionado (`tfidf_matrix[idx]`) e todos os outros vetores de filmes na matriz TF-IDF (tfidf_matrix). Isso é feito usando a função cosine_similarity do scikit-learn.
- A similaridade de cosseno resultante é uma matriz de uma dimensão, onde cada elemento representa a similaridade entre o filme selecionado e um filme na coleção.
- Os índices dos filmes com as maiores similaridades (excluindo o próprio filme selecionado) são selecionados e armazenados em similar_movies_indices.
- Por fim, os filmes correspondentes a esses índices são retornados como recomendações.

A similaridade de cosseno ajuda a encontrar os filmes mais similares ao filme selecionado com base em seus conteúdos textuais representados pela matriz TF-IDF.

## Conclusão

Em suma, o sistema de recomendação de filmes baseado em conteúdo utiliza técnicas avançadas de processamento de linguagem natural e similaridade de cosseno para oferecer sugestões personalizadas aos usuários. Através da criação de uma matriz TF-IDF, que captura a importância relativa das palavras nos documentos, e da comparação dos vetores TF-IDF usando a similaridade de cosseno, o sistema é capaz de calcular recomendações precisas, sugerindo filmes com conteúdos textuais semelhantes aos filmes de entrada dos usuários. Essa abordagem garante uma experiência de recomendação relevante e personalizada, ajudando os usuários a descobrir novos filmes com base em seus interesses individuais.
