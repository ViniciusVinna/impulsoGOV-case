<div align="center">
  <p>
    <img src="docs/logo.png" alt="ImpulsoGOV" width="550px" />
    <h2>Projeto de Arquitetura e ETL</h2>
  </p>
</div>

## Sumário

- [1 - Introdução](#)
- [2 - Mapeamento de componentes do sistema](#)
  - [2.1 - Modelagem do Domínio (DDD - Domain Driven Design)](#)
  - [2.2 - Digrama de Sequência](#)
- [3 - Análise Crítica e Identificação de Riscos](#)
  - [3.1 - Insights de "Quick Wins"](#)
  - [3.2 - ImpulsoGOV Tech Radar](#)
- [4 - Propostas de melhoria](#)
  - [4.1 - Novo Método de Extração e Transmissão - API Restful](#)
  - [4.2 - Implementação de Processo de ETL Apache Airflow](#)
  - [4.3 - Melhorias no Processo de Comunicação - API intermediária](#)
  - - [4.3.1 - Proposta de implementação](#)

---

## 1 - Introdução

A [ImpulsoGOV](https://www.impulsogov.org/) é uma organização de tecnologia do terceiro setor que faz uso inteligente de dados para transformar a saúde pública do Brasil.

[🎯 Desafio original](https://impulsogov.notion.site/Case-CTO-885231d00e494dc5bd2332f1053d3cbd)

## 2 - Mapeamento de componentes do sistema:

<details>
  <summary>
    🔧 &nbsp; Back-end & ETL - Arquitetura Atual</a>
  </summary>

- **Linguagem:** `Python`
- **Framework:** `FastAPI`

### Processo de ETL:

- O `transmissor` atual é um `conector do Postgres` aplicado ao banco de dados do município.
- `Conexão direta` entre o `transmissor` e o `banco de dados analítico` da ImpulsoGov.
- Uma `procedure` no `banco do município` executa `consultas` em tabelas locais e `insere` os resultados no banco de dados analítico.

### Banco do Município:

- Propriedade e gestão pertencentes ao próprio município.
- - Localizado em servidores locais ou em nuvem.
- - Variações na presença de pessoal de TI.
- Todos os municípios possuem um banco `padrão acoplado` ao `PEC (Software do SUS)`, resultando em uma modelagem consistente.

### Rotina de Transmissão:

- `Diariamente`, no início da manhã, iniciam-se os processos de transmissão.
- - Geralmente, no início da tarde, todos os dados do dia são recebidos.
- A instalação do transmissor é de responsabilidade da ImpulsoGov.

</details>

---

#### 🏗️ &nbsp; 2.1 - Modelagem do Domínio (DDD - Domain Driven Design)

<details>
  <summary>
    Entidades Principais
  </summary>
&nbsp;

| Entidades Principais | Definições                                                                                                                                                   |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ImpulsoGov           | Responsável pela lógica de aplicação, incluindo o Backend (Python com FastAPI), Processo de ETL, Rotina de Transmissão e Instalação do Transmissor.          |
|                      | `Agregados`: Backend (Python, FastAPI), Processo de ETL (Transmissor, Banco do Município, Rotina de Transmissão), Banco do Município, Rotina de Transmissão. |
|                      | `Objetos de Valor`: Conexão Direta, Procedure no Banco do Município, Modelagem Consistente.                                                                  |

</details>

<details>
  <summary>
    Contextos Delimitados
  </summary>
&nbsp;

| Contextos Delimitados | Definições                                                                                                                                                                   |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ImpulsoGov            | Desenvolvimento do `Backend` em `Python` com `FastAPI`, Processo de `ETL` (Extração, Transformação e Carregamento), Rotina de `Transmissão` e `Instalação` do `Transmissor`. |
| Banco do Município    | Gerencia propriedade, localização e variações de pessoal de TI nos bancos municipais.                                                                                        |
| Rotina de Transmissão | Define os processos `diários` de `transmissão` de dados.                                                                                                                     |

</details>

<details>
  <summary>
    Glossário
  </summary>
&nbsp;

|     | **Termo Técnico**                   | **Explicação Técnica**                                                                              | **Contexto ImpulsoGov**                                                                              |
| --- | ----------------------------------- | --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| 🔍  | **Entidades**                       | Objetos identificáveis e fundamentais para o domínio, mantendo uma existência ao longo do tempo.    | A `Entidade Backend` é central no sistema.                                                           |
| 🔄  | **Agregados**                       | Agrupamentos de entidades e objetos de valor para garantir consistência no domínio.                 | O `Processo de ETL` é um agregado com `Transmissor`, `Banco do Município` e `Rotina de Transmissão`. |
| 📦  | **Objetos de Valor**                | Objetos sem identidade própria, definidos por atributos, trazendo valor pela composição.            | Exemplo de objeto de valor é a `Conexão Direta`.                                                     |
| 🌐  | **Contextos Delimitados**           | Áreas do domínio com regras específicas, onde termos têm significados isolados.                     | Dentro do `Banco do Município`, estabelecemos nossas próprias regras.                                |
| 💻  | **Backend**                         | Lógica de aplicação, processamento de dados e interação com o usuário.                              | Nosso `Backend` é desenvolvido em `Python` com `FastAPI`.                                            |
| 🔄  | **Processo de ETL**                 | Atividades de Extração, Transformação e Carregamento de dados entre sistemas ou bancos de dados.    | O `Transmissor` é crucial no `Processo de ETL`.                                                      |
| 📡  | **Transmissor (Postgres)**          | Componente que extrai dados de um banco PostgreSQL e os transmite para outro local.                 | O `Transmissor` conecta o `Banco do Município` ao `Banco Analítico`.                                 |
| 🔄  | **Procedure no Banco do Município** | Rotina armazenada no banco de dados que executa consultas e insere resultados.                      | As `Procedures` no `Banco do Município` manipulam dados no `Banco Analítico`.                        |
| 🏗️  | **Modelagem Consistente**           | Abordagem para manter um padrão uniforme na estrutura e design dos dados.                           | Garantimos uma `Modelagem Consistente` em todos os `Bancos do Município`.                            |
| 🔗  | **Conexão Direta**                  | Estabelecimento de uma ligação direta entre dois componentes para transferência eficiente de dados. | A `Conexão Direta` entre o `Transmissor` e os `Bancos` garante transferência eficiente.              |

</details>

---

#### 🔄 &nbsp; 2.2 - Digrama de Sequência

```mermaid

sequenceDiagram
  participant Transmissor
  participant BancoMunicipio
  participant BancoAnalitico

  Transmissor ->> BancoMunicipio: Conectar ao BancoMunicipio
  activate BancoMunicipio

  Transmissor ->> BancoMunicipio: Executar consulta em tabelas locais
  note over BancoMunicipio: Procedure no BancoMunicipio

  BancoMunicipio -->> Transmissor: Resultados da consulta

  Transmissor ->> BancoAnalitico: Conectar ao BancoAnalitico da ImpulsoGov
  activate BancoAnalitico

  Transmissor ->> BancoAnalitico: Inserir resultados no BancoAnalitico
  note over BancoAnalitico: Transformação e carga de dados

  BancoAnalitico -->> Transmissor: Confirmação de carga

  deactivate BancoMunicipio
  deactivate BancoAnalitico

```

---

## 🧐 &nbsp; 3 - Análise Crítica e Identificação de Riscos

> 📢 Nota: Eu fiz a avaliação exclusivamente com base nas informações e tecnologias fornecidas durante os processos de entrevistas com [Pedro Drummond](https://www.linkedin.com/in/pedro-drummond/), [Gabrielle Arruda](https://www.linkedin.com/in/gabrielle-arruda/) e o escopo do case proposto pelo **Levi**.
>
> <img src="docs/bruce-buffer.png" align="left" alt="VAR" width="120" border="5px solid transparent" />
>
> ```
> ██╗████████╗███████╗    ████████╗██╗███╗   ███╗███████╗██╗
> ██║╚══██╔══╝██╔════╝    ╚══██╔══╝██║████╗ ████║██╔════╝██║
> ██║   ██║   ███████╗       ██║   ██║██╔████╔██║█████╗  ██║
> ██║   ██║   ╚════██║       ██║   ██║██║╚██╔╝██║██╔══╝  ╚═╝
> ██║   ██║   ███████║       ██║   ██║██║ ╚═╝ ██║███████╗██╗
> ╚═╝   ╚═╝   ╚══════╝       ╚═╝   ╚═╝╚═╝     ╚═╝╚══════╝╚═╝
> ```

A partir do diagrama de sequência identifiquei alguns pontos **"protocolares"** são importantes ser validados antes de iniciativas de escala: Inspecionar a saúde do sistema e de seus componentes de forma análoga [aviação (checklist e preflight)](https://hangarmma.com.br/blog/o-que-sao-checklists-de-voo/):

| **Aspecto**                 | **Vantagens**                           | **Pontos de Atenção**                                 | **Validações**                                                                                                                                                                                          | **Mitigação de Risco**                                                                                                                                                                           | **Sugestões**                                                                                                                                                                                              |
| --------------------------- | --------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🛡️ **Segurança da Conexão** | 🚀 Conexão rápida                       | 🔐 Segurança                                          | ✅ Validar tempos de resposta para garantir eficiência. <br/><br/> ✅ Auditar configurações de segurança, incluindo autenticação e criptografia. <br/><br/> ✅ Implementar monitoramento em tempo real. | 📋 Reforçar segurança por meio de patches contínuos. <br/><br/> 📋 Sanitização dos dados de entrada das rotas para prevenir ataques comuns, como injeção de SQL e XSS (Cross-Site Scripting)     | 👉 Implementar autenticação e criptografia _OAuth2_ e _JWT_. <br/><br/> 👉 Configurar alertas automáticos para monitoramento contínuo com RapidAPI.                                                        |
|                             | 📊 Padronização: Facilita manutenção.   | 🛑 Variação de Pessoal de TI: Pode afetar segurança.  | ✅ Revisar documentação para garantir conformidade. <br/><br/> ✅ Implementar treinamentos remotos para padronização. <br/><br/> ✅ Monitorar atividades e fornecer suporte técnico remoto.             | 📋 Estabelecer diretrizes claras para segurança consistente. <br/><br/> 📋 Oferecer treinamento remoto adicional, se necessário.                                                                 | 👉 Criar documentação técnica com exemplos de segurança usando FastAPI e [Swagger](https://github.com/swagger-api/swagger-ui) <br/><br/> 👉 Oferecer webinars ou tutoriais online para treinamento remoto. |
| 🚀 **Desempenho**           | 🔄 Atualização Diária: Processo eficaz. | ⚠️ Possíveis Gargalos: Risco de eficiência.           | ✅ Realizar testes de carga para identificar gargalos. <br/><br/> ✅ Monitorar tempos de processamento diário. <br/><br/> ✅ Implementar técnicas de otimização.                                        | 📋 Avaliar e implementar soluções escaláveis. <br/><br/> 📋 Explorar ferramentas avançadas de monitoramento para identificar gargalos.                                                           | 👉 Utilizar índices eficientes no PostgreSQL para otimizar recuperação de dados. <br/><br/> 👉 Explorar ferramentas de otimização de consulta para melhor desempenho do PostgreSQL.                        |
|                             | 🚀 Eficiência com Grandes Dados.        | 🌐 Impacto na Infraestrutura: Pode afetar desempenho. | ✅ Realizar testes de desempenho com grandes volumes de dados. <br/><br/> ✅ Monitorar tempos de resposta em diferentes ambientes. <br/><br/> ✅ Estabelecer diretrizes claras.                         | 📋 Investir em análise de infraestrutura em municípios com variações significativas. <br/><br/> 📋 Considerar caches locais para melhor eficiência no processamento de grandes volumes de dados. | 👉 Utilizar técnicas de particionamento no PostgreSQL para otimizar consultas em grandes volumes de dados. <br/><br/> 👉 Explorar índices adequados para consultas frequentes.                             |

---

> 👌 Sem _"chover no molhado"_, O `PostgreSQL` já reúne uma lista exaustiva de [vantagens](https://www.postgresql.org/about/featurematrix/) em relação a outros bancos de dados opensource - sendo praticamente um padrão de mercado para soluções ETL Data Warehouse.

<details>
  <summary>
    🌟 Postgres - Destaques
  </summary>
&nbsp;

- [x] O PostgreSQL adota o `Multi-Version Concurrency Control (MVCC)`, que possibilita operações `concorrentes eficientes`, importante no processamento de grandes volumes de dados dos municípios que exijam escrita e leitura (nas transformações) da `instância analítica` sem `locks`, garantindo isolamento transacional e consistência nos resultados.
- [x] Suporte a tipos de dados avançados dando mais felxibilidade em transformações complexas durante as etapas de ETL.
- [x] Desempenho robusto. É capaz de lidar eficientemente com `grandes volumes` de dados.
- [x] O suporte completo a `transações ACID` (Atomicidade, Consistência, Isolamento e Durabilidade), incluindo o `MVCC`, assegura uma recuperação consistente em situações de falhas.

</details>

---

#### 💡 &nbsp; 3.1 - Insights de "Quick Wins":

- 👉 Boa parte das validações implicam no monitoramento e o **FastAPI** possui suporte nativo para [Prometheus](https://prometheus.io/) para coletar métricas de desempenho, latência e etc, que podem ser integradas diretamente outras soluções de visualização de métricas como o [Grafana](https://grafana.com/) - [Tutorial de exemplo](https://dev.to/ken_mwaura1/getting-started-monitoring-a-fastapi-app-with-grafana-and-prometheus-a-step-by-step-guide-3fbn), e ambas também podem ser integradas a ferramentas de instrumentalização como o **OpenTelemetry** para a gestão de dados de telemetria garantindo `visão sistêmica holística` de todos os `domínios de aplicações` da **ImpulsoGOV** - [Tutorial de exemplo](https://grafana.com/blog/2022/05/10/how-to-collect-prometheus-metrics-with-the-opentelemetry-collector-and-grafana/.)

---

## 🧭 &nbsp; 3.2 - ImpulsoGOV Tech Radar

A partir do contexto e do mapeamento eu produzi uma versão _muuito_ mais simplificada de um [tech radar](https://www.thoughtworks.com/en-br/radar) (_com o objetivo de manter o escopo do desafio_) mas que ajuda a construir uma representação visual clara do cenário tecnológico, facilita a tomada de decisões estratégicas e o alinhamento das escolhas tecnológicas com os objetivos de negócios - já adicionando algumas das sugestões de melhorias e integrações.

[![](https://mermaid.ink/img/pako:eNqVlF2TmjAUhv9KJh2vijtqYFUuOuP4sd2dbmtXpp_sRQoHySwQGoKVOvz3hkQrdnTGcsHF-zw5CSE5OxzwELCLO50dy5h00c7HP0saCprJaUyF9HGTZUg9Pg6a5DMLZdzEI9Kz0Al6C2wdyz2rj1DGkMInKhj9kUDRKmnwYcL-giVJQ338isxvp_bAx4cip-agZfYXg6k9u2SSljlcEIc4l0y7bc6HczJW5tllerCVLTmKoovrvF4l16v2WfWcueQsu7KoVq8u_GWyZcU_tjOckplzYcDX_x3gMZnAGdm4NarrTsfPTs-qQrIZhu7TvEwKfsc3yIMgRk80pKLB2y5V60DzDZNUoG73DZqEXBpWGeZB8ZdtaMIMPEzU7aP5NqdZyE7jAVoKnvINnMYEPQE9U8VGj5DEXJj4vdqXh5WLvvdubi2kXs9NuqxkzDP0Gi1oISfLe82HTiMMHWOoKUFdrrI4jh0ZdCdoHn98p_OeBo4BXpVDEQiWS80GTot9yCHzIFGXVYpKY1vjMTnUjGhGW2A_2YwHLyB0bsrpdAVCbYe68MWREIMe1J60FmE3yB5oNMlpEAOaMBEl_Ffrmw1e8kKuBRSr_bcZdus8YwunIFLKQtXOdHvZ9x1zftT_f2kOT608Wkq-qrIAu1KUYOEyD6mEGaNrQVPsRjQpVKp-MnZ3eIvdnoUr7PZHN05t4d-cK0ftiXnsHhmrpuL0LQwhk1w8moaq-6ou8k0PaGaq_wAwQ58w?type=png)](https://mermaid.live/edit#pako:eNqVlF2TmjAUhv9KJh2vijtqYFUuOuP4sd2dbmtXpp_sRQoHySwQGoKVOvz3hkQrdnTGcsHF-zw5CSE5OxzwELCLO50dy5h00c7HP0saCprJaUyF9HGTZUg9Pg6a5DMLZdzEI9Kz0Al6C2wdyz2rj1DGkMInKhj9kUDRKmnwYcL-giVJQ338isxvp_bAx4cip-agZfYXg6k9u2SSljlcEIc4l0y7bc6HczJW5tllerCVLTmKoovrvF4l16v2WfWcueQsu7KoVq8u_GWyZcU_tjOckplzYcDX_x3gMZnAGdm4NarrTsfPTs-qQrIZhu7TvEwKfsc3yIMgRk80pKLB2y5V60DzDZNUoG73DZqEXBpWGeZB8ZdtaMIMPEzU7aP5NqdZyE7jAVoKnvINnMYEPQE9U8VGj5DEXJj4vdqXh5WLvvdubi2kXs9NuqxkzDP0Gi1oISfLe82HTiMMHWOoKUFdrrI4jh0ZdCdoHn98p_OeBo4BXpVDEQiWS80GTot9yCHzIFGXVYpKY1vjMTnUjGhGW2A_2YwHLyB0bsrpdAVCbYe68MWREIMe1J60FmE3yB5oNMlpEAOaMBEl_Ffrmw1e8kKuBRSr_bcZdus8YwunIFLKQtXOdHvZ9x1zftT_f2kOT608Wkq-qrIAu1KUYOEyD6mEGaNrQVPsRjQpVKp-MnZ3eIvdnoUr7PZHN05t4d-cK0ftiXnsHhmrpuL0LQwhk1w8moaq-6ou8k0PaGaq_wAwQ58w)

## 🔩 &nbsp; 4 - Proposta de Melhorias no Processo de ETL e Comunicação de Dados:

> 📢 Nota: Com base nas informações compartilhadas durante os processos de entrevistas com [Pedro Drummond](https://www.linkedin.com/in/pedro-drummond/) e [Gabrielle Arruda](https://www.linkedin.com/in/gabrielle-arruda/) entendi que a **ImpulsoGOV** utiliza as soluções cloud **GCP**. Optei por propor **soluções incrementais** - utilizando o serviço adotado.

#### 4.1 - 📥 Novo Método de Extração e Transmissão - API Restful:

- [ ] `Introdução de API Restful para Extração`:
  - **Motivação**: Minimizar os riscos de `segurança (da comunicação direta entre os bancos)` e otimizar a eficiência da extração de dados dos **bancos municipais** na infraestrutura da Google Cloud Platform (GCP).

**Benefícios**:

- **Utilização das tecnologias já adotadas pela ImpulsoGOV**: - **Python** + **FastAPI**, integrando com [Cloud Endpoints](https://cloud.google.com/endpoints?hl=pt-br) na GCP.
- **Segurança**: A API permitirá autenticação usando serviços da GCP, como [Identity and Access Management (IAM)](https://cloud.google.com/security/products/iam?hl=pt-br), garantindo uma camada adicional de segurança:

```mermaid

sequenceDiagram
  participant Transmissor
  participant API_Restful
  participant BancoMunicipio
  participant GCP

  Transmissor ->> API_Restful: Solicitar dados via API
  activate API_Restful

  API_Restful ->> BancoMunicipio: Autenticar e Extrair Dados
  note over BancoMunicipio: Processo seguro usando IAM

  BancoMunicipio -->> API_Restful: Dados Extraídos

  API_Restful -->> Transmissor: Resposta com Dados

  API_Restful ->> GCP: Transmitir dados para o banco analítico
  note over GCP: Utilizando Cloud Endpoints

  GCP -->> API_Restful: Confirmação de Recebimento

  deactivate API_Restful

```

> O Transmissor solicita dados por meio da API Restful, que autentica e extrai dados de forma segura do Banco Municipal utilizando o IAM. Posteriormente, os dados são transmitidos para o banco analítico na GCP através do Cloud Endpoints, com confirmação de recebimento pela GCP.

---

#### 4.2 - 🔄 Implementação de Processo de ETL Apache Airflow:

> 📢 Nota: Como não sou especialista em egenharia de dados e a ImpulsoGOV já possui um **time com mais experiência** no domínio e as necessidades da demanda atual, segui o caminho sugerido no desafio, utilizando o [Apache Airflow](https://airflow.apache.org/) e também com base em algumas premissas compartilhadas pelo [Pedro Drummond](https://www.linkedin.com/in/pedro-drummond/) sobre o cenário atual:
>
> - `650 municipios` atualmente com uma **média** entre a `5 - 8 usuários`
> - A consolidação é feita diariamente, _logo_ **não** se trata de uma `aplicação real time`, o que _descarta_ integrações sistêmicas mais robustas como Apache Kafka e comunicação entre microserviços.

- **Motivação**: Garantir maior confiabilidade, monitoramento e controle nas operações de ETL na infraestrutura da GCP.

**Benefícios**:

- **Orquestração Avançada na GCP**: Configurar DAGs no Apache Airflow para representar o fluxo do processo ETL, integrando ao [Cloud Composer](https://cloud.google.com/composer?hl=pt_br), permitindo monitorar as pipelines.

- 🙌 Integrar Cloud Monitoring e Cloud Logging para monitoramento detalhado, e/ou a partir das soluções sugeridas de melhoria na tabela `Análise Crítica e Identificação de Riscos` - como integrações entre [OpenTelemetry](https://cloud.google.com/stackdriver/docs/managed-prometheus/setup-otel?hl=pt-br), [Prometheus](https://prometheus.io/) e [Grafana](https://grafana.com/).

<details>
  <summary>
    🌟 Apache Airflow - Destaques
  </summary>
&nbsp;

| Vantagem                            | Descrição                                                                                                                                                      |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **🔄 Programação Declarativa**      | Permite a definição de fluxos de trabalho (DAGs) usando código Python de forma declarativa, facilitando a leitura e manutenção do código.                      |
| **🎭 Orquestração Flexível**        | Oferece uma plataforma flexível para a orquestração de tarefas, permitindo a execução de fluxos de trabalho complexos e cronogramas personalizados.            |
| **🔗 Extensibilidade**              | Possui uma arquitetura modular e extensível que permite a adição de novos operadores, conexões e plugins para integrar com diversas tecnologias e serviços.    |
| **👀 Monitoramento e Visualização** | Fornece uma interface web amigável para monitorar o status dos fluxos de trabalho, visualizar logs e métricas, facilitando o diagnóstico de problemas.         |
| **♻️ Reutilização de Código**       | Permite a reutilização de código Python para definir tarefas, promovendo a modularidade e a fácil manutenção de diferentes partes do fluxo de trabalho.        |
| **🗓️ Agendamento Dinâmico**         | Oferece um agendador dinâmico que permite a configuração de cronogramas flexíveis e a execução de tarefas com base em eventos externos ou condições.           |
| **🚀 Suporte a Execução Paralela**  | Facilita a execução paralela de tarefas, o que é crucial para otimizar o desempenho em fluxos de trabalho complexos e distribuídos.                            |
| **🔗 Controle de Dependências**     | Gerencia automaticamente as dependências entre tarefas, garantindo que uma tarefa seja executada apenas quando suas dependências forem concluídas com sucesso. |
| **⚖️ Escalabilidade Horizontal**    | Pode ser escalado horizontalmente para lidar com grandes volumes de trabalho, distribuindo tarefas em vários nós de execução.                                  |
| **🤝 Comunidade Ativa e Suporte**   | Beneficia-se de uma comunidade ativa de desenvolvedores e usuários, com uma ampla gama de documentação, tutoriais e suporte online disponíveis.                |

</details>

> A camada de implementação do processo de ETL com o Apache Airflow é adicionada à interação entre o Transmissor, a API Restful e a infraestrutura da GCP. Após a extração e transmissão dos dados, a API aciona uma DAG no Apache Airflow, que executa as tarefas ETL na GCP usando o Cloud Composer. Em seguida, as métricas detalhadas são registradas utilizando OpenTelemetry para dados de monitoramento e Prometheus para métricas detalhadas. Essas métricas são visualizadas no Grafana, proporcionando uma visão abrangente e detalhada do processo de ETL:

```mermaid

graph TD
  A[Transmissor] -->|Solicitar dados via API| B[API Restful]
  B -->|Autenticar e Extrair Dados| C[Banco Municipal]
  C -->|Dados Extraídos| B
  B -->|Acionar DAG no Apache Airflow| D[Apache Airflow]
  D -->|Executar Tarefas ETL| E[GCP]
  E -->|Confirmação de Execução| D
  D -->|Registrar Dados de Monitoramento| F[OpenTelemetry]
  D -->|Registrar Métricas Detalhadas| G[Prometheus]
  F -->|Visualização de Dados de Monitoramento| H[Grafana]
  G -->|Visualização de Métricas Detalhadas| H

```

```mermaid

sequenceDiagram
  participant Transmissor
  participant API_Restful
  participant Apache_Airflow
  participant BancoMunicipio
  participant GCP
  participant Cloud_Composer
  participant OpenTelemetry
  participant Prometheus
  participant Grafana

  Transmissor ->> API_Restful: Solicitar dados via API
  activate API_Restful

  API_Restful ->> BancoMunicipio: Autenticar e Extrair Dados
  note over BancoMunicipio: Processo seguro usando IAM

  BancoMunicipio -->> API_Restful: Dados Extraídos

  API_Restful -->> Transmissor: Resposta com Dados

  API_Restful ->> Apache_Airflow: Acionar DAG no Apache Airflow
  activate Apache_Airflow

  Apache_Airflow ->> GCP: Executar Tarefas ETL
  note over GCP: Utilizando Cloud Composer para DAGs

  GCP -->> Apache_Airflow: Confirmação de Execução

  Apache_Airflow ->> OpenTelemetry: Registrar Dados de Monitoramento
  Apache_Airflow ->> Prometheus: Registrar Métricas Detalhadas

  OpenTelemetry -->> Grafana: Visualização de Dados de Monitoramento
  Prometheus -->> Grafana: Visualização de Métricas Detalhadas

  deactivate API_Restful
  deactivate Apache_Airflow

```

---

#### 4.3 - 🕵️‍♂️ Melhorias no Processo de Comunicação - API intermediária:

Introdução de Camada de Serviços Intermediária utilizando[Cloud Functions](https://cloud.google.com/functions?hl=pt_br) ou [Cloud Run](https://cloud.google.com/run?hl=pt_br) para criar uma API intermediária.

- Utilizar [Cloud Pub/Sub](https://cloud.google.com/pubsub?hl=pt_br) para gerenciar mensagens entre o `transmissor` e o `banco de dados do município`.

**Motivação:** Simplificar e fortalecer a comunicação entre o `banco de dados do município` e a `base de dados na GCP`.

**Benefícios:**

- **Padronização na GCP**: Integrar serviços de mensagens, como `Cloud Pub/Sub`, para padronizar a comunicação entre o `transmissor` e o `banco de dados do município`.
- `Túnel Seguro`: Configurar uma VPN na GCP para estabelecer um túnel seguro entre o transmissor e o banco de dados do município especialmente ao lidar com dados sensíveis.
- `Isolamento de Rede`: Utilizar as políticas de segurança de rede (Firewall) da GCP para criar uma rede privada virtual, isolando a comunicação.

---

#### 4.3.1 - 🚀 Proposta de implementação:

> - O Transmissor inicia o processo solicitando dados por meio de uma API para a API Intermediária.
> - A API Intermediária utiliza o Cloud Pub/Sub para publicar uma mensagem, gerenciando eficientemente a comunicação entre o Transmissor e o Banco de Dados do Município.
> - A Cloud Functions é acionada pela mensagem no Cloud Pub/Sub, realizando de forma segura a autenticação e extração de dados do Banco do Município.
> - Após a extração, a Cloud Functions responde ao Cloud Pub/Sub, que efetua a entrega eficiente da mensagem à API Intermediária.
> - Esta, por sua vez, responde ao Transmissor com os dados extraídos.
> - O restante do processo, incluindo a orquestração com o Apache Airflow e o monitoramento avançado com OpenTelemetry, Prometheus e Grafana, prossegue conforme representado no diagrama de sequência anterior.

```mermaid

sequenceDiagram
  participant Transmissor
  participant API_Intermediaria
  participant Cloud_PubSub
  participant Cloud_Functions
  participant BancoMunicipio
  participant API_Restful
  participant Apache_Airflow
  participant GCP
  participant Cloud_Composer
  participant OpenTelemetry
  participant Prometheus
  participant Grafana

  Transmissor ->> API_Intermediaria: Solicitar dados via API
  activate API_Intermediaria

  API_Intermediaria ->> Cloud_PubSub: Publicar mensagem
  activate Cloud_PubSub

  Cloud_PubSub -->> Cloud_PubSub: Gerenciar Mensagens

  Cloud_PubSub -->> Cloud_Functions: Acionar Função
  activate Cloud_Functions

  Cloud_Functions ->> BancoMunicipio: Autenticar e Extrair Dados
  note over BancoMunicipio: Processo seguro usando IAM

  BancoMunicipio -->> Cloud_Functions: Dados Extraídos

  Cloud_Functions -->> Cloud_PubSub: Resposta com Dados

  Cloud_PubSub -->> API_Intermediaria: Receber Mensagem

  API_Intermediaria -->> Transmissor: Resposta com Dados

  API_Intermediaria ->> Apache_Airflow: Acionar DAG no Apache Airflow
  activate Apache_Airflow

  Apache_Airflow ->> GCP: Executar Tarefas ETL
  note over GCP: Utilizando Cloud Composer para DAGs

  GCP -->> Apache_Airflow: Confirmação de Execução

  Apache_Airflow ->> OpenTelemetry: Registrar Dados de Monitoramento
  Apache_Airflow ->> Prometheus: Registrar Métricas Detalhadas

  OpenTelemetry -->> Grafana: Visualização de Dados de Monitoramento
  Prometheus -->> Grafana: Visualização de Métricas Detalhadas

  deactivate API_Intermediaria
  deactivate Cloud_PubSub
  deactivate Cloud_Functions
  deactivate Apache_Airflow

```
