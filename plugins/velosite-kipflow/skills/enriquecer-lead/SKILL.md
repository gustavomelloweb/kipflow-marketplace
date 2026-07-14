---
name: enriquecer-lead
description: Enriquece um lead B2B brasileiro (nome de empresa, dominio ou CNPJ) com dados cadastrais, telefones, emails corporativos e decisores no LinkedIn usando a API da Kipflow. Use quando o usuario pedir para enriquecer lead, qualificar empresa, achar CNPJ, achar telefone ou email de empresa, encontrar decisores, montar ficha de prospeccao ou pesquisar uma lista de empresas. Triggers em portugues: enriquecer lead, enriquecer empresa, qualificar lead, buscar CNPJ, achar telefone da empresa, gerar email corporativo, encontrar decisores, ficha de prospeccao, enriquecer lista de leads. Triggers in English: enrich lead, enrich company, qualify lead, find CNPJ, find company phone, generate corporate email, find decision makers, prospecting card, enrich lead list.
---

# Enriquecer Lead (Kipflow)

Esta skill orienta como usar as ferramentas MCP `kipflow_*` para transformar um nome de
empresa, dominio ou CNPJ em uma ficha de prospeccao completa: dados cadastrais, telefones,
emails corporativos e decisores no LinkedIn.

A API da Kipflow e paga por dataset e por registro retornado. Cada resposta inclui um campo
`cost` (e `costFormatted`, ja em reais). Seja economico: peca so o que o objetivo da tarefa
exige.

## Fluxo recomendado

### 1. Resolver a identidade da empresa

Se o usuario deu apenas um nome de empresa (sem CNPJ nem dominio), comece por
`kipflow_empresa_por_nome` para achar o CNPJ certo via score de similaridade. Escolha o
resultado com maior `similarity` e confirme com o usuario se o nome for ambiguo (varias
empresas com nomes parecidos).

Se o usuario ja deu um CNPJ ou dominio, pule direto para o passo 2.

### 2. Dados cadastrais

Use `kipflow_buscar_empresa` com `cnpj` ou `domain`. Escolha os `datasets` de acordo com o
que a tarefa pede:

- `basic`: cadastro essencial (razao social, situacao cadastral, CNAE). Use isso como padrao
  minimo para qualquer enriquecimento.
- `complete`: capital social, porte, faturamento, funcionarios. Peca quando o usuario quer
  qualificar o tamanho da empresa (ex: fit de ICP).
- `address`: endereco e geolocalizacao. Peca quando o usuario precisa de localizacao.
- `online_presence`: sites e redes sociais. Peca quando for prospectar por canais digitais.
- `partners`: quadro societario (nomes e CPF mascarado dos socios). So peca se o usuario
  precisar identificar socios/decisores formais; ver nota de LGPD abaixo.
- `debts`: divida ativa da Uniao. So peca se o usuario pedir explicitamente analise de risco
  de credito.

Nao peca todos os datasets "por garantia": cada um tem custo proprio. Se o pedido for so
"confirma que essa empresa existe e esta ativa", `basic` sozinho basta.

Para varios leads de uma vez (ate 50), use `kipflow_buscar_empresas_lote_cnpj` ou
`kipflow_buscar_empresas_lote_dominio` em vez de chamar `kipflow_buscar_empresa` em loop.

### 3. Telefones

Use `kipflow_telefones_empresa` com `cnpj` ou `domain`. O custo e por telefone retornado,
entao defina `phone_limit` (por exemplo 1 a 3) em vez de puxar a lista inteira sem limite.
Se o objetivo for WhatsApp, use `only_whatsapp: true`.

### 4. Emails

Duas estrategias, escolha a que fizer sentido:

- **Pessoa especifica conhecida**: se o usuario ja sabe o nome completo do contato, use
  `kipflow_gerar_email_nome_dominio` (com dominio) ou `kipflow_gerar_email_nome_cnpj` (com
  CNPJ, a Kipflow resolve o dominio internamente). Mais barato e mais preciso.
- **Descobrir contatos da empresa**: se o usuario nao tem um nome especifico e quer emails
  de pessoas relevantes da empresa, use `kipflow_gerar_emails_empresa` com `email_limit`
  baixo (2 a 5) para controlar o custo. Essa ferramenta usa perfis do LinkedIn por
  senioridade internamente.

### 5. Decisores no LinkedIn

Use `kipflow_personas_linkedin_empresa` quando o usuario quiser encontrar pessoas por cargo,
senioridade ou area (ex: "quero o CFO ou o gerente financeiro dessa empresa"). Informe
`company_id` ou `company_public_id` (geralmente disponivel no campo `linkedin_url` retornado
por `kipflow_buscar_empresa` com dataset `online_presence`, extraindo o slug depois de
`linkedin.com/company/`).

Use `seniority` e `area` para filtrar (ver valores documentados na ferramenta) e mantenha
`size` baixo (3 a 10) na primeira tentativa: o custo e cobrado por resultado retornado.

## Seja economico (custo por dataset)

- Comece sempre pelo dataset/ferramenta mais barato que resolve a pergunta do usuario.
- Nao rode todas as ferramentas "para garantir" se o usuario so pediu, por exemplo, o
  telefone da empresa.
- Ao enriquecer uma lista, prefira os endpoints em lote (`kipflow_buscar_empresas_lote_cnpj`,
  `kipflow_buscar_empresas_lote_dominio`) a chamar a ferramenta individual em loop.
- Sempre que possivel, informe ao usuario o `cost` acumulado da sessao de enriquecimento
  (some os campos `cost` de cada resposta) para ele acompanhar o gasto.
- Se uma resposta vier com erro 402 (pagamento necessario), avise o usuario que o saldo da
  carteira Kipflow acabou e que ele precisa adicionar credito em platform.kipflow.io antes de
  continuar. Nao tente contornar isso.

## Cuidado com LGPD

Os datasets `partners` (socios, com CPF mascarado) e os dados de pessoas fisicas (nome,
cargo, email, telefone de decisores) sao dados pessoais protegidos pela LGPD. Ao usar esta
skill:

- So peca dados de pessoas fisicas quando isso for necessario para o objetivo declarado
  pelo usuario (prospeccao B2B legitima). Nao colete "por via das duvidas".
- Nunca junte esses dados a outras bases sem uma base legal clara (em geral, legitimo
  interesse para contato comercial B2B, respeitando opt-out).
- Ao entregar a ficha final, lembre o usuario de que os dados de contato pessoal (telefone,
  email, LinkedIn) devem ser tratados com finalidade especifica, guardados com seguranca e
  removidos se o titular solicitar (direito de exclusao da LGPD).
- Nunca exponha CPF completo: a Kipflow ja retorna CPF mascarado nos dados de socios; nao
  tente completar ou cruzar esse dado com outras fontes.

## Formato de saida: ficha de enriquecimento

Ao final, monte uma ficha tidy para o usuario, por exemplo:

```
## [Razao Social] ([Nome Fantasia])
CNPJ: 00.000.000/0001-00 | Situacao: ATIVA | CNAE: [descricao]
Porte: [porte] | Faturamento: [faixa] | Funcionarios: [faixa]
Endereco: [endereco completo]
Site: [dominio] | LinkedIn: [url]

Telefones:
- [telefone] (whatsapp: sim/nao)

Contatos gerados:
- [nome] - [cargo/senioridade] - [email] ([status de validacao])

Decisores no LinkedIn:
- [nome] - [cargo atual] - [perfil]

Custo total desta consulta: R$ [soma dos campos cost]
```

Omita secoes para as quais nenhum dado foi consultado (nao invente valores "N/A" para
datasets que o usuario nao pediu).
