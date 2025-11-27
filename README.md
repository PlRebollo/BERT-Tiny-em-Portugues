# BERT-tiny-PT  
Fine-Tuning e Destilação do BERT-tiny para Português Brasileiro

Este repositório apresenta o processo completo de adaptação do modelo BERT-tiny para o português brasileiro utilizando técnicas de destilação de conhecimento, tendo o BERTimbau-base como modelo professor. O objetivo é produzir um modelo leve, rápido e eficiente, capaz de compreender português mantendo parte da capacidade representacional de um modelo maior.

O repositório inclui:
- `destilacao_parcial.ipynb` – destilação mantendo o vocabulário original do aluno.
- `destilacao_total.ipynb` – destilação reconstruindo completamente o aluno com o vocabulário do professor.
- Relatório com metodologia, experimentos e conclusões.

---

## 1. Introdução

Modelos de linguagem de grande porte apresentam excelente desempenho, mas possuem alto custo computacional. A destilação de conhecimento oferece uma alternativa eficiente, permitindo transferir capacidades de um modelo grande (teacher) para um modelo pequeno (student), mais rápido e adequado a ambientes restritos.

Este projeto busca responder se é possível construir um BERT-tiny capaz de compreender português de maneira útil. Para isso, exploramos duas abordagens de destilação:

1. Destilação Parcial – preserva o vocabulário original do BERT-tiny (inglês).
2. Destilação Total – substitui completamente o vocabulário, adotando o tokenizador do BERTimbau-base.

---

## 2. Metodologia

A metodologia segue princípios clássicos de destilação, combinando perdas de Masked Language Modeling (MLM), divergência KL e similaridade de cosseno. As duas estratégias são descritas abaixo.

### 2.1 Destilação Parcial

- Mantém o tokenizador original do BERT-tiny.
- O professor fornece embeddings de referência.
- A loss final combina:
  - MLM
  - Similaridade de cosseno entre embeddings do professor e do aluno.

Essa abordagem preserva parte das representações semânticas já aprendidas pelo tiny em inglês, favorecendo tarefas que exigem inferência lógica.

### 2.2 Destilação Total

- O modelo aluno é reconstruído do zero.
- O tokenizador e vocabulário passam a ser exatamente os do BERTimbau-base.
- Como as dimensões são incompatíveis (768 → 128), adicionou-se uma camada linear de projeção.
- A loss final combina:
  - MLM
  - KL Divergence (temperatura 2.0)
  - Similaridade de cosseno

Essa abordagem gera um modelo mais alinhado ao português do ponto de vista morfossintático, porém com semântica inicial mais limitada.

---

## 3. Dataset

Foi utilizado o corpus brWaC, com mais de 3,5 milhões de textos em português brasileiro. O dataset passou por limpeza rigorosa envolvendo remoção de HTML, URLs, metadados, duplicatas e textos muito curtos ou longos.

Após pré-processamento, restaram cerca de 3,25 milhões de textos.  
A divisão dos dados foi:

- 90% para treinamento  
- 10% para teste  

---

## 4. Experimentos

Ambas as metodologias foram avaliadas em tarefas intrínsecas (MLM) e extrínsecas:

- Masked Language Modeling (MLM)
- Similaridade Textual – ASSIN2 (Pearson)
- Análise de Sentimentos – TweetSentBR (F1)
- Entailment – ASSIN2 RTE (F1)

### 4.1 Resultados da Destilação Parcial

| Tarefa | Tiny Original | Tiny-PT (Parcial) | BERTimbau |
|--------|--------------|--------------------|-----------|
| MLM Loss | 5.7462 | 1.9433 | 1.7569 |
| STS (Pearson) | 0.5262 | 0.5763 | 0.6139 |
| Sentiment (F1) | 0.1905 | 0.2121 | 0.2778 |
| RTE (F1) | 0.2183 | 0.4059 | 0.1983 |

A abordagem parcial apresentou melhor desempenho geral. O modelo retém parte da semântica aprendida no inglês e demonstrou forte desempenho em inferência lógica, superando inclusive o professor em RTE.

### 4.2 Resultados da Destilação Total

| Tarefa | Tiny Original | Tiny-PT (Total) | BERTimbau |
|--------|--------------|------------------|-----------|
| MLM Loss | 5.7462 | 3.3025 | 1.7569 |
| STS (Pearson) | 0.5262 | 0.4400 | 0.6139 |
| Sentiment (F1) | 0.3583 | 0.2000 | 0.1667 |
| RTE (F1) | 0.2183 | 0.2225 | 0.1983 |

A destilação total produziu um modelo mais alinhado ao português, mas com semântica global mais fraca, reflexo da reconstrução completa do vocabulário.

---

## 5. Conclusão

O BERT-tiny-PT atingiu o objetivo principal: tornou-se capaz de compreender português e superou o modelo tiny original em Masked Language Modeling.

Entretanto, a transferência da capacidade semântica profunda não foi totalmente alcançada. Os resultados mostram que:

- A destilação parcial preserva melhor a semântica e favorece tarefas de inferência.
- A destilação total é mais fiel ao idioma, mas necessita de treinamento adicional para maturar a semântica.

Destilar um modelo pequeno exige mais do que aproximar logits e embeddings; é necessário considerar limitações arquiteturais e estratégias específicas para preservar semântica profunda.

---
# BERT-Tiny-em-Português
Destilação do BERTimbau para o BERT-Tiny
