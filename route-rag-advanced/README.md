# RAG Agentics Workflow Confiáveis com LangGraph, Groq-Llama-3 e AstraDB

## Introdução

Neste tutorial, vamos construir agentes RAG (Recuperação Aumentada por Geração) confiáveis utilizando as ferramentas **LangGraph**, **Groq-Llama-3** e **AstraDB**. Combinaremos diferentes conceitos e técnicas avançadas para criar um agente RAG robusto e adaptável. Também utilizaremos o framework **Graphdoc**, que encapsula modelos como **BERT**, **Swin Transformer** e **LayoutLM** para gerar embeddings de alto nível, otimizando a recuperação de informações a partir de documentos estruturados e semiestruturados.

### Estratégias RAG

Para garantir que nosso agente seja capaz de lidar com uma variedade de consultas de maneira eficaz, utilizaremos quatro estratégias principais baseadas em artigos de referência:

1. **Adaptive RAG** ([artigo](https://arxiv.org/abs/xxxx)): Esta abordagem utiliza um roteador que encaminha as perguntas para diferentes abordagens de recuperação, dependendo do tipo de consulta. O roteador decide se deve usar uma recuperação baseada em vectorstore ou realizar uma pesquisa na web, dependendo da natureza da pergunta.

2. **Corrective RAG** ([artigo](https://arxiv.org/abs/xxxx)): Nesta estratégia, desenvolvemos um mecanismo de fallback que é acionado quando o contexto recuperado não é relevante o suficiente para responder à pergunta do usuário. Neste caso, o agente realiza uma nova tentativa, ajustando sua recuperação até obter uma resposta adequada.

3. **Auto-RAG** ([artigo](https://arxiv.org/abs/xxxx)): Aqui, implementamos um classificador de alucinações, que corrige respostas que podem ter sido geradas erroneamente ou que não abordam diretamente a pergunta feita. Esse classificador permite detectar respostas potencialmente imprecisas e melhora a confiança nas respostas apresentadas.

4. **Self-Querying RAG**: Uma técnica poderosa de autoconsulta que utiliza LLMs para gerar consultas estruturadas automaticamente a partir de entradas em linguagem natural. Esse método permite:
   - **Extrair metadados** da entrada de linguagem natural.
   - **Indexar campos de metadados** para garantir consultas precisas.
   - **Gerar filtros** que contenham expressões e operadores de correspondência suportados pela API do vectorstore.
   - **Executar consultas de busca vetorial** com pré-filtros, refinando os resultados para garantir relevância.

### Fluxo de Trabalho do Agente RAG

1. **Roteamento da Pergunta**: Com base na pergunta do usuário, o roteador decide se a recuperação do contexto deve ser feita a partir do **vectorstore** (caso a pergunta esteja relacionada a dados já indexados) ou se uma **pesquisa na web** deve ser realizada.

2. **Recuperação e Classificação de Documentos**: 
   - Caso a consulta seja direcionada para o **vectorstore**, os documentos relevantes serão recuperados.
   - Caso contrário, uma pesquisa na web será realizada usando a **tavily-api**.
   - Após a recuperação, um **classificador de documentos** avalia se o conteúdo é relevante ou irrelevante.

3. **Verificação de Alucinações**:
   - Se o contexto recuperado for classificado como relevante, um **classificador de alucinações** será utilizado para verificar a precisão da resposta. Se for considerada uma resposta sem alucinação, ela será apresentada ao usuário.
   - Se o conteúdo for considerado irrelevante, será realizada uma nova **pesquisa na web**.

4. **Self-Querying para Metadados**: Quando a consulta envolve informações específicas que exigem filtragem detalhada, a estratégia de **self-querying** será aplicada. Isso inclui:
   - Geração de consultas baseadas em **metadados** do banco de dados, resultando em respostas mais focadas e detalhadas.
   - **Recuperação vetorial** usando pré-filtros gerados automaticamente, garantindo que os documentos mais relevantes sejam retornados.

5. **Síntese da Resposta**: Após a recuperação e classificação, a resposta será sintetizada usando um **LLM** (Groq-Llama-3), e então apresentada ao usuário.

