# 📝 Resposta do Laboratório: A Wiki Perdida dos Arquivos Corporativos

> Preencha este arquivo com a sua proposta de solução.
>
> Sua resposta deve explicar como transformar os documentos brutos da pasta `raw/` em uma Wiki Corporativa Inteligente, pesquisável e segura usando apenas serviços da AWS.

---

## 👤 Identificação
**Nome:**  Elias Oliveira de Freitas   
**Data:**  09/10/2026   
**Link do repositório:**  https://github.com/EliasOFreitas/dio-aws-desafio-nublify

---

# ✅ Quest 1: O Mapa dos Arquivos Perdidos

## 1.1 Formatos encontrados na pasta `raw/`

Descreva quais tipos de arquivos existem dentro da pasta `raw/`.

```md
Exemplo de como responder, com o formato e o que ele implica:
- <extensao>: <nasce digital ou precisa de OCR?>, <o que da para extrair>
```

> Abra a pasta e liste o que voce encontrou de fato. Esta quest avalia a sua
> leitura do acervo, entao a resposta certa e a que corresponde aos arquivos.

**Sua resposta:**

```md

Na pasta raw/, foi identificado três tipos diferentes de arquivos que representam o acervo da empresa:   

- .PDF (ata_reuniao_vendas_sa.pdf) : Documento digital em PDF com texto nativo (5 páginas). Por ser digital nativo, não precisa de OCR o texto pode ser lido diretamente. Contém a pauta da reunião, participantes, metas, decisões e plano de ação.    

- .PNG (ata_resultados_vendas_novos_dados.png) : Imagem digitalizada de uma folha impressa (1 página). Contém tabelas de vendas e anotações que podem conter escritas à mão ou carimbos. Como é uma imagem (pixels), exige o uso de OCR para transformar a imagem em texto.   

- .CSV (vendas_sa_dados_ficticios_laboratorio.csv) : Tabela com 240 oportunidades do CRM em 19 colunas. Contém dados estruturados sobre vendas, clientes, valores e status de negociação.

```

---

## 1.2 Principais desafios encontrados

Explique quais dificuldades esses documentos podem apresentar.

```md
Exemplo:
- Arquivos sem padrão de nomenclatura
- Documentos escaneados com baixa qualidade
- Textos manuscritos ou parcialmente ilegíveis
- Atas com estruturas diferentes
- Informações importantes espalhadas em vários formatos
```

**Sua resposta:**

```md

- Todos os arquivos estão misturados na pasta raw/ sem divisão por subpastas ou padrão de nomenclatura.
- Formatos diferentes (PDF com texto, PNG com imagem/manuscrito/carimbos e CSV com tabela).
- A imagem PNG pode possuir escrita manual (anotações feitas à mão) e carimbos que um OCR comum pode ter dificuldade de ler.
- Estruturas de informações diferente.
- O CSV é uma planilha e precisa ser convertido em um texto fácil de ler antes de ser pesquisado por Inteligência Artificial.

```

---

## 1.3 Informações importantes a serem extraídas

Liste quais informações precisam ser identificadas para transformar os documentos em conhecimento pesquisável.

**Sua resposta:**

```md

- Datas e assuntos principais das reuniões.
- Lista de participantes presentes.
- Decisões importantes aprovadas nas reuniões.
- Pessoas responsáveis por cada tarefa e os prazos definidos.
- Informações de vendas do CRM (valores, clientes, vendedores e motivos de perda).

```

---

## 1.4 Estratégia de classificação inicial

Como você classificaria os documentos sem depender de subpastas dentro de `raw/`?

**Sua resposta:**

```md
 
Para classificar os arquivos sem criar subpastas na pasta raw/ seguiria da seguinte forma:
- Identificamos o arquivo pela formato de extensão (.pdf, .png, .csv).
- Marcamos tags (etiquetas) no próprio serviço de armazenamento (Amazon S3) indicando o tipo de documento.
- Mantemos os arquivos brutos originais intocados.

```

---

# ✅ Quest 2: O Portal de Entrada na AWS

## 2.1 Armazenamento dos arquivos brutos

Explique como os arquivos da pasta `raw/` seriam enviados e armazenados na AWS.

Serviços que você pode considerar:

- Amazon S3
- AWS IAM
- AWS KMS
- Amazon S3 Versioning
- Amazon S3 Lifecycle

**Sua resposta:**

```md

- Usaremos o Amazon S3 (Simple Storage Service) para guardar todos os arquivos brutos da pasta raw/.
- O Amazon S3 é seguro, altamente disponível e permite armazenar qualquer tipo de arquivo.

```

---

## 2.2 Preservação dos arquivos originais

Explique como garantir que os arquivos originais sejam mantidos intactos e rastreáveis.

**Sua resposta:**

```md

- Ativaremos o Versionamento do S3 (S3 Versioning) para garantir que os arquivos originais nunca sejam apagados ou sobrescritos por engano.
- Os arquivos processados (textos extraídos) serão salvos em um local separado no S3.

```

---

## 2.3 Extração de texto dos documentos

Explique como cada tipo de arquivo seria processado.

Considere:

- PDFs escaneados;
- Imagens;
- PDFs digitais;
- Arquivos `.txt`;
- Arquivos `.docx`;
- Arquivos `.md`.

Serviços que você pode considerar:

- Amazon Textract
- AWS Lambda
- AWS Step Functions
- Amazon S3
- Amazon CloudWatch

**Sua resposta:**

```md

- Para o correto direcionamento o AWS Step Functions entra como orquestrador desse fluxo.

- PDF Digital (ata_reuniao_vendas_sa.pdf): O texto é extraído diretamente usando um código em AWS Lambda (pypdf ou pdfplumber), sem o custo ou tempo de execução de um OCR visual.

- Imagem PNG/JPG/JPEG (ata_resultados_vendas_novos_dados.png) ou PDFs escaneados: Usamos o Amazon Textract, serviço da AWS especializado em OCR que lê textos impressos e anotações feitas à mão.

- CSV do CRM (vendas_sa_dados_ficticios_laboratorio.csv): Usamos a AWS Lambda para ler cada linha da tabela e transformar em frases claras (textualização do CSV).

- Arquivos .txt: Usamos a a AWS Lambda que lê o arquivo do Amazon S3 (usando o SDK boto3) e decodifica o texto em UTF-8. É a extração mais simples e rápida da esteira.

- Arquivos .docx: Processados diretamente pela AWS Lambda utilizando bibliotecas leves de conversão (como python-docx) para abrir a estrutura XML do arquivo .docx, extraindo o texto de parágrafos e tabelas de forma limpa.

- Arquivos .md: Processados diretamente pela AWS Lambda, por ser texto puro com marcações estruturadas (títulos `#`, listas `-`), a Lambda lê o arquivo nativamente. Essa estrutura em Markdown facilita o corte do texto em trechos (chunks) e a extração de cabeçalhos para os metadados.

```

---

## 2.4 Tratamento de falhas

Explique como sua solução identificaria e registraria erros de processamento.

**Sua resposta:**

```md

- Se um arquivo estiver corrompido ou der erro no processamento, o Amazon CloudWatch registra o log de erro e alerta a equipe para que o arquivo seja verificado.

```

---

# ✅ Quest 3: A Relíquia dos Metadados

## 3.1 Padronização dos textos processados

Explique como os textos extraídos seriam limpos, normalizados e preparados para consulta.

**Sua resposta:**

```md
 
- Após a extração, os textos são limpos (removendo espaços extras e símbolos indesejados) e salvos em um formato padrão (Markdown ou TXT) para facilitar a consulta.

```

---

## 3.2 Metadados propostos

Defina quais metadados você extrairia de cada documento.

| Metadado | Por que ele é importante? |
|---|---|
| Nome do documento | Permite saber exatamente de onde veio a informação identificando a fonte exata para citar o arquivo de onde a IA tirou a resposta, garantindo transparência.|
| Tipo do documento | Permite filtrar por formato ou categoria identificando se é uma ata, uma imagem ou uma planilha do CRM. e direcionar conforme a extensão para o serviço de extração correto. |
| Data identificada | Organiza as reuniões e vendas por ordem cronológica e filtrar buscas por período de tempo. |
| Tema principal | Ajuda a filtrar documentos por assunto (ex: Vendas, Planejamento). |
| Participantes | Registra quem esteve presente na reunião. |
| Decisões tomadas | Facilita encontrar o que foi aprovado em cada reunião. |
| Responsáveis | Mostra quem ficou encarregado de cada tarefa. |
| Próximos passos | Permite acompanhar pendências, ações futuras e prazos definidos nas reuniões (como o plano de ação e datas estipuladas). Ajuda a Wiki a responder perguntas sobre o que precisa ser feito e o que ficou pendente. |
| Nível de confidencialidade | Essencial para a segurança e controle de acesso aos documentos (ex: Público, Interno, Confidencial). Garante que a Wiki só exiba informações sensíveis para usuários autorizados. |
| Caminho do arquivo original | Endereço no Amazon S3 para baixar ou consultar o arquivo original. |

Adicione outros metadados, se necessário.

---

## 3.3 Uso de IA para enriquecimento dos documentos

Explique como o Amazon Bedrock poderia ajudar a identificar temas, decisões, responsáveis, pendências e resumos dos documentos.

**Sua resposta:**

```md

- Usamos o Amazon Bedrock (com modelos de Inteligência Artificial) para ler o texto extraído, resumir o documento e identificar automaticamente quais foram as decisões, temas e responsáveis citados.

```

---

## 3.4 Armazenamento dos metadados

Explique onde os metadados seriam armazenados e como seriam conectados aos documentos originais.

Serviços que você pode considerar:

- Amazon S3
- Amazon DynamoDB
- AWS Glue Data Catalog
- Amazon Bedrock Knowledge Bases

**Sua resposta:**

```md

- Guardamos os metadados em uma tabela simples no Amazon DynamoDB (banco de dados rápido e gerenciado) conectado ao arquivo correspondente no Amazon S3.

```

---

# ✅ Quest 4: O Oráculo da Wiki Inteligente

## 4.1 Estratégia de indexação

Explique como os documentos seriam divididos em trechos menores e preparados para busca semântica.

**Sua resposta:**

```md

- Os textos completos são divididos em partes menores (chunks ou blocos de texto) para que a IA consiga encontrar a informação exata sem se perder em documentos longos.

```

---

## 4.2 Busca semântica e base vetorial

Explique como embeddings seriam gerados e onde seriam armazenados.

Serviços que você pode considerar:

- Amazon Bedrock Knowledge Bases
- Amazon OpenSearch Serverless
- Amazon Aurora PostgreSQL com pgvector
- Amazon S3 Vectors
- Modelos de embeddings no Amazon Bedrock

**Sua resposta:**

```md

Para transformar os documentos em conhecimento pesquisável por significado, a solução pensada utiliza a seguinte arquitetura de geração e armazenamento vetorial:

- 1. Geração de Embeddings: O Amazon Bedrock utiliza o modelo Amazon Titan Text Embeddings v2 para converter os trechos de texto (chunks) e metadados em vetores numéricos de alta dimensão, capturando o contexto semântico das atas e relatórios.

- 2. Orquestração da Ingestão: O Amazon Bedrock Knowledge Bases orquestra automaticamente todo o processo, lê os arquivos processados no Amazon S3, envia o texto para o Titan Embeddings gerar os vetores e os armazena no repositório vetorial. 

- 3. Armazenamento no Banco Vetorial: Os vetores gerados são armazenados no Amazon OpenSearch Serverless (Vector Engine). Ele atua como a base vetorial principal da Wiki, permitindo realizar buscas semânticas (por similaridade vetorial) e buscas por metadados (ex: filtrar apenas atas de 2026 ou decisões da Região Norte). 

- Alternativa técnica: Caso a empresa opte por uma solução relacional, pode-se utilizar o Amazon Aurora PostgreSQL com a extensão pgvector.

```

---

## 4.3 Geração de respostas com IA

Explique como a Wiki responderia perguntas em linguagem natural com base nos documentos originais.

Considere explicar:

- Como a pergunta do usuário seria recebida;
- Como os trechos relevantes seriam recuperados;
- Como o Amazon Bedrock geraria a resposta;
- Como a resposta indicaria as fontes utilizadas.

**Sua resposta:**

```md

1. O usuário digita uma pergunta na Wiki em linguagem natural (ex: "Quais foram as principais decisões sobre vendas?").
2. A solução busca os trechos mais relevantes nos documentos processados.
3. O Amazon Bedrock lê esses trechos e gera uma resposta clara e objetiva.
4. A resposta informa de qual documento a informação foi retirada (citando a fonte original).

```

---

## 4.4 Interface de consulta

Proponha como os usuários acessariam essa Wiki Inteligente.

Serviços que você pode considerar:

- Amazon Q Business
- AWS Amplify
- Amazon API Gateway
- AWS Lambda
- Amazon Cognito

**Sua resposta:**

```md

- Os usuários consultam a Wiki através de uma tela simples web ou pelo Amazon Q Business (assistente inteligente da AWS para empresas).

```

---

## 4.5 Segurança, auditoria e monitoramento

Explique como controlar acesso, proteger dados, auditar consultas e monitorar custos, erros e qualidade das respostas.

Serviços que você pode considerar:

- AWS IAM
- AWS KMS
- Amazon Cognito
- AWS CloudTrail
- Amazon CloudWatch
- Amazon Macie
- AWS Cost Explorer

**Sua resposta:**

```md

- Segurança: Acesso protegido por AWS IAM e Amazon Cognito (apenas pessoas autorizadas entram).
- Monitoramento: Amazon CloudWatch monitora o uso e eventuais erros da aplicação.
- Auditar Consultas: AWS CloudTrail monitora todas as chamadas de API do Bedrock e serviços envolvidos, registrando o histórico de acesso (quem fez a consulta, quando e de onde).

```

---

# 🧩 Arquitetura Final da Solução

Agora reúna tudo em uma visão única.

## 1. Visão geral

Explique em poucas linhas a ideia central da sua arquitetura.

**Sua resposta:**

```md

Uma solução Serverless na AWS que pega os documentos da pasta raw/, usa Amazon Textract e AWS Lambda para extrair o texto, organiza os dados com Amazon DynamoDB e Bedrock, permitindo que qualquer funcionário faça perguntas em linguagem natural e receba respostas precisas com citação da fonte.

```

---

## 2. Serviços AWS utilizados

| Serviço AWS | Papel na solução |
|---|---|
| **Amazon S3** | Armazenar os arquivos brutos da pasta raw/ e guardar os textos processados e metadados com versionamento e segurança. |
| **Amazon Textract** | Realizar a leitura OCR de imagens (.png, .jpg) e documentos escaneados, extraindo textos impressos e escritas manuais. |
| **Amazon Bedrock** | Gerar resumos, identificar entidades/decisões, criar embeddings (Titan Embeddings v2) e gerar as respostas finais da IA. |
| **Amazon Bedrock Knowledge Bases** | Orquestrar a busca RAG, dividindo textos em chunks, gerando vetores e conectando o S3 ao repositório vetorial. |
| **AWS Lambda** | Processar arquivos digitais (.pdf, .txt, .docx, .md, .csv), realizar a limpeza do texto e textualização dos dados do CRM. |
| **AWS Step Functions** | Orquestrar a esteira automatizada de processamento e extração dos documentos do início ao fim. |
| **Amazon CloudWatch** | Registrar logs de execução, monitorar erros de processamento e alertar a equipe sobre falhas. |
| **AWS IAM** | Controlar as permissões de acesso aos serviços com base no princípio do menor privilégio. |
| **AWS KMS** | Criptografar os arquivos em repouso no S3 e bancos de dados para garantir a proteção das informações. |
| **Amazon DynamoDB** | Guardar o catálogo central de metadados dos documentos de forma rápida, organizada e de baixa latência. |
| **Amazon OpenSearch Serverless** | Atuar como a base vetorial principal para armazenar os embeddings e realizar buscas semânticas eficientes. |
| **Amazon Cognito / Amazon Q Business** | Autenticar usuários e fornecer a interface de consulta simples e segura para fazer perguntas à Wiki. |
| **AWS CloudTrail** | Registrar a auditoria de acessos e o histórico de chamadas de API do Bedrock e demais serviços. |

Adicione, remova ou ajuste os serviços conforme sua proposta.

---

## 3. Fluxo de dados de ponta a ponta

Descreva o caminho dos dados desde a pasta `raw/` até a Wiki Inteligente.

```md
Exemplo de estrutura:

1. Arquivos estão inicialmente na pasta raw/
2. Arquivos são enviados para o Amazon S3
3. Documentos escaneados passam pelo Amazon Textract
4. Arquivos digitais têm seus textos extraídos
5. Textos são limpos e padronizados
6. Metadados são extraídos
7. Conteúdos são indexados em uma base pesquisável
8. Usuário pesquisa na Wiki
9. IA responde com base nos documentos originais
```

**Sua resposta:**

```md

1. Envio dos Arquivos: Os arquivos brutos (PDF, PNG, CSV) são enviados para o bucket do Amazon S3.
2. Identificação e Roteamento: O S3 dispara uma função AWS Lambda que identifica o tipo de arquivo e aplica as tags (etiquetas).
3. Extração de Texto:
   - Imagens e documentos escaneados passam pelo Amazon Textract (OCR).
   - PDFs digitais, arquivos de texto (.txt, .docx, .md) e planilhas CSV são processados via AWS Lambda.
4. Limpeza e Padronização: O texto extraído é limpo de ruídos e padronizado em formato Markdown/TXT.
5. Extração de Metadados via IA: O Amazon Bedrock analisa o texto e identifica automaticamente datas, decisões, responsáveis e temas, salvando os dados no Amazon DynamoDB.
6. Indexação Vetorial: O Amazon Bedrock Knowledge Bases divide o texto em chunks, gera os embeddings (vetores com Titan Embeddings v2) e armazena no Amazon OpenSearch Serverless.
7. Consulta do Usuário: O funcionário digita uma pergunta em linguagem natural na interface (Amazon Q Business / Web).
8. Resposta Fundamentada: A solução busca os trechos mais relevantes no OpenSearch, envia para o Bedrock e retorna a resposta informando a fonte exata no S3.

```

---

## 4. Diagrama textual da arquitetura

Crie um diagrama simples usando texto.

```md
Exemplo:

raw/ → Amazon S3 → Lambda/Step Functions → Textract → S3 Processado → Bedrock Knowledge Bases → Interface de Consulta → Usuário Final
```

**Sua resposta:**

```md

raw/ → Amazon S3 → AWS Lambda/Step Functions → Amazon Textract → Amazon S3 (Processado) → Amazon Bedrock & DynamoDB → Bedrock Knowledge Bases → Amazon OpenSearch Serverless → Amazon Q Business / Web → Usuário Final

```

---

## 5. Riscos e limitações

Liste possíveis desafios da sua solução.

```md
Exemplo:
- Documentos ilegíveis podem prejudicar a extração de texto.
- OCR pode gerar erros em documentos com baixa qualidade.
- Custos podem aumentar conforme o volume de documentos.
- Metadados inferidos por IA podem precisar de validação humana.
- Respostas geradas por IA devem sempre referenciar documentos de origem.
```

**Sua resposta:**

```md

- Imagens de baixíssima qualidade ou manuscritos muito ilegíveis podem afetar a precisão da leitura do Textract.
- Planilhas em CSV com formatos fora do padrão podem exigir ajustes na função Lambda de textualização.
- Risco de respostas imprecisas (alucinações) caso a temperatura do LLM não seja ajustada para valores baixos.
- Necessidade de monitoramento de custos caso haja um volume muito elevado de chamadas às APIs da AWS.
- Respostas da IA devem obrigatoriamente citar o documento de origem para permitirem validação humana.

```

---

## 6. Melhorias futuras

Descreva como a solução poderia evoluir.

```md
Exemplo:
- Criar uma interface web para consulta.
- Criar um chat interno para perguntas sobre atas.
- Adicionar controle de acesso por departamento.
- Criar dashboard de decisões e pendências.
- Gerar alertas automáticos sobre ações em aberto.
- Integrar com ferramentas corporativas.
```

**Sua resposta:**

```md

- Implementar uma interface web totalmente personalizada e amigável em React com AWS Amplify.
- Adicionar dashboards gráficos no Amazon QuickSight para analisar os dados de vendas do CRM.
- Criar alertas automáticos por e-mail (via Amazon SNS) para lembrar os responsáveis sobre prazos e pendências.
- Integrar a Wiki com ferramentas de comunicação interna (como Slack ou Microsoft Teams).
- Expandir o controle de acesso granular por departamento utilizando o Amazon Cognito.

```

---

# 🧠 Checklist Final

Antes de entregar, confirme se sua solução responde:

- [X] Como transformar documentos escaneados em texto?
- [X] Como lidar com diferentes formatos dentro da mesma pasta `raw/`?
- [X] Como armazenar os documentos originais?
- [X] Como preservar a rastreabilidade entre resposta e documento fonte?
- [X] Como organizar metadados?
- [X] Como criar busca semântica?
- [X] Como usar Amazon Bedrock na solução?
- [X] Como proteger documentos sensíveis?
- [X] Como monitorar falhas?
- [X] Como a empresa usaria essa Wiki no dia a dia?

---

# 🏁 Conclusão

Escreva uma breve conclusão defendendo sua solução como se estivesse apresentando para uma liderança técnica ou de negócio.

**Sua resposta:**

```md

A arquitetura proposta para a Wiki Corporativa Inteligente resolve de forma simples, eficiente e segura o problema dos arquivos acumulados na pasta raw/. Utilizando serviços totalmente Serverless da AWS — como Amazon S3, AWS Lambda, AWS Step Functions, Amazon Textract, Amazon DynamoDB e Amazon Bedrock —, a solução transforma documentos desorganizados em uma base de conhecimento pesquisável por linguagem natural. 

Com essa estrutura, qualquer liderança ou colaborador consegue encontrar rapidamente decisões, responsáveis, prazos e métricas de vendas em segundos, com a garantia de total rastreabilidade até o documento fonte original. Trata-se de uma proposta robusta, escalável e perfeitamente alinhada às práticas recomendadas de nuvem da AWS.

```
