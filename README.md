<div align="center">
  <p>
    <img src="docs/logo.png" alt="ImpulsoGOV" width="550px" />
    <h2>Projeto de Arquitetura e ETL</h2>
  </p>
</div>

## Introdução

A [ImpulsoGOV](https://www.impulsogov.org/) é uma organização de tecnologia do terceiro setor que faz uso inteligente de dados para transformar a saúde pública do Brasil.

[🎯 Desafio original](https://impulsogov.notion.site/Case-CTO-885231d00e494dc5bd2332f1053d3cbd)

## Mapeamento de componentes do sistema:

<details>
  <summary>
    🔧 &nbsp; Back-end - Arquitetura Atual</a>
  </summary>

- **Linguagem:** Python
- **Framework:** FastAPI

**Processo de ETL:**

- O transmissor atual é um conector do Postgres aplicado ao banco de dados do município.
- Conexão direta entre o transmissor e o banco de dados analítico da ImpulsoGov.
- Uma procedure no banco do município executa consultas em tabelas locais e insere os resultados no banco de dados analítico.

**Banco do Município:**

- Propriedade e gestão pertencentes ao próprio município.
- Localizado em servidores locais ou em nuvem.
- Variações na presença de pessoal de TI.
- Todos os municípios possuem um banco padrão acoplado ao PEC (Software do SUS), resultando em uma modelagem consistente.

**Rotina de Transmissão:**

- Diariamente, no início da manhã, iniciam-se os processos de transmissão.
- Geralmente, no início da tarde, todos os dados do dia são recebidos.
- A instalação do transmissor é de responsabilidade da ImpulsoGov.

</details>
