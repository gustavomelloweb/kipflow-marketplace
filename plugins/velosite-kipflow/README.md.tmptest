# Kipflow MCP (velosite-kipflow)

Plugin Claude Code / Cowork que conecta a API de enriquecimento B2B da Kipflow
(https://docs.kipflow.io/) como um servidor MCP local, mais uma skill que ensina o Claude a
usar essas ferramentas para enriquecer leads (nome de empresa, dominio ou CNPJ) com dados
cadastrais, telefones, emails corporativos e decisores no LinkedIn.

A Kipflow e uma API brasileira de dados empresariais (Receita Federal, redes sociais,
LinkedIn e fontes proprias). A cobranca e por dataset consultado ou por registro retornado:
cada resposta traz um campo `cost` (e `costFormatted`) mostrando o quanto aquela chamada
custou.

## O que este plugin traz

- Um servidor MCP (`server/index.js`, Node.js) que fala com `https://api.kipflow.io` usando
  o header `X-API-Key`.
- Nove ferramentas MCP, uma por endpoint da Kipflow (ver lista abaixo).
- Uma skill (`skills/enriquecer-lead/SKILL.md`) que orienta o Claude sobre a ordem certa de
  chamadas, como economizar (pedir so os datasets necessarios) e cuidados de LGPD.

## Ferramentas disponiveis

| Ferramenta | Endpoint Kipflow | Para que serve |
| --- | --- | --- |
| `kipflow_buscar_empresa` | `GET /companies/v1/search` | Dados cadastrais de uma empresa por CNPJ ou dominio |
| `kipflow_buscar_empresas_lote_cnpj` | `POST /companies/v1/search/batch/cnpj` | Ate 50 empresas de uma vez, por CNPJ |
| `kipflow_buscar_empresas_lote_dominio` | `POST /companies/v1/search/batch/domain` | Ate 50 empresas de uma vez, por dominio |
| `kipflow_empresa_por_nome` | `GET /intelligence/v1/company-match` | Encontra o CNPJ certo a partir de um nome parcial, com score de similaridade |
| `kipflow_telefones_empresa` | `GET /contacts/v1/phones` | Telefones (e WhatsApp) de uma empresa |
| `kipflow_gerar_email_nome_dominio` | `POST /contacts/v1/emails/generate-by-domain` | Gera e valida o email de uma pessoa a partir do nome + dominio |
| `kipflow_gerar_email_nome_cnpj` | `POST /contacts/v1/emails/generate-by-cnpj` | Gera e valida o email de uma pessoa a partir do nome + CNPJ |
| `kipflow_gerar_emails_empresa` | `GET /contacts/v1/emails` | Gera emails dos contatos de maior senioridade de uma empresa |
| `kipflow_personas_linkedin_empresa` | `POST /social/v1/personas/search` | Busca decisores no LinkedIn por cargo, senioridade e area |

Todas as ferramentas repassam a resposta JSON da Kipflow como veio da API (incluindo o campo
`cost`), sem reformatar ou inventar campos.

## Custo e uso responsavel

Este plugin nao controla nem limita gastos: cada chamada as ferramentas acima consome saldo
da sua carteira Kipflow. Recomendacoes:

- Peca so os `datasets` que a tarefa exige (ver detalhes na skill `enriquecer-lead`).
- Prefira os endpoints em lote ao enriquecer varias empresas.
- Use os parametros `phone_limit`, `email_limit` e `$size` para limitar quantos registros
  pagos sao retornados por chamada.
- Acompanhe o campo `cost` de cada resposta.
- Um erro HTTP 402 significa que o saldo da carteira acabou: adicione credito em
  platform.kipflow.io/pricing antes de continuar.

## LGPD

Varios datasets retornam dados pessoais (nome, CPF mascarado de socios, telefone, email,
cargo de decisores). Use esses dados apenas para a finalidade de prospeccao B2B legitima
declarada pelo usuario, sem cruzar com outras bases sem base legal, e esteja preparado para
excluir os dados se o titular solicitar. Detalhes praticos estao na skill
`enriquecer-lead/SKILL.md`.

## Como funciona por baixo dos panos

- `server/kipflow.js`: cliente HTTP fino (usa `fetch` nativo do Node 18+), monta a URL,
  injeta o header `X-API-Key`, trata os codigos de erro documentados (400, 401, 402, 404,
  408, 429, 500) com mensagens claras e faz um retry simples (ate 2 tentativas, com espera
  fixa) em caso de 429 (rate limit).
- `server/index.js`: servidor MCP via stdio (`@modelcontextprotocol/sdk`), declara as nove
  ferramentas com `inputSchema` em JSON Schema e delega cada chamada ao cliente HTTP.
- `.mcp.json`: declara o servidor `kipflow` para o Claude Code/Cowork, rodando
  `node ${CLAUDE_PLUGIN_ROOT}/server/index.js` com `KIPFLOW_API_KEY` vindo do
  `user_config.kipflow_api_key` (preenchido no momento da instalacao do plugin).
- `.claude-plugin/plugin.json`: manifesto do plugin, com o campo `userConfig` que faz o
  Claude Code pedir a chave de API de forma segura (marcada como `sensitive`) ao instalar.

## Instalacao

Veja o passo a passo para usuarios nao tecnicos em [INSTALL.md](./INSTALL.md).

## Estado de validacao

Este plugin foi validado estaticamente (sintaxe JavaScript, JSON valido dos manifests). Ele
ainda **nao foi testado contra a API real da Kipflow** (isso exigiria uma chave de API paga).
Antes de distribuir para usuarios finais, rode um teste manual com uma chave real cobrindo
pelo menos: uma busca por CNPJ, uma busca por nome com similaridade e uma geracao de email,
conferindo se o campo `cost` aparece como esperado e se os erros 401/402/404 sao tratados
corretamente.
