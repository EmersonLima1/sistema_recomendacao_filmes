# Sistema de Recomendação de Filmes

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
- Inverse Document Frequency (IDF): Mede a raridade de uma palavra em uma coleção de documentos. É calculada como o logaritmo do número total de documentos na coleção dividido pelo número de documentos que contêm a palavra.


