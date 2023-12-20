<div align="center">
  <p>
    <img src="docs/logo.png" alt="ImpulsoGOV" width="550px" />
    <h2>Projeto de Arquitetura e ETL</h2>
  </p>
</div>

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
