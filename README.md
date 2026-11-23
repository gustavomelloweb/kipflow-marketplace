# Marketplace de plugins da Velosite

Este repositorio e um marketplace de plugins para o Claude. Instale sem baixar arquivo: cole a URL deste repositorio no Claude.

## O que e o velosite-kipflow

Enriquecimento de dados de empresas dentro do Claude. A partir do nome, do CNPJ ou do dominio de uma empresa, retorna dados cadastrais, telefones, emails e os decisores certos no LinkedIn (por cargo, area e senioridade).

O servidor roda remoto, hospedado pela Velosite, entao voce nao instala Node nem roda nada localmente. Cada usuario conecta a propria chave de API da Kipflow.

## Instalacao: um passo

### Adicione o conector

1. No Claude, va em Customize, depois Connectors, depois Add custom connector.
2. Em Nome, escreva: `Raio X de Empresas`
3. Em URL do servidor MCP remoto, cole:
   ```
   https://kipflow-mcp-vercel.vercel.app/api/mcp
   ```
4. Deixe os campos de OAuth vazios. Clique em Adicionar.

Pronto. O conector ja traz as ferramentas e as instrucoes de uso.

### A chave da Kipflow

Crie sua conta gratuita em platform.kipflow.io, va em API Keys e gere uma chave.

Na primeira consulta de cada conversa, o Claude vai pedir essa chave. Cole e ele reusa ate o
fim da conversa. Conversa nova, ele pede de novo: a chave e sua e nao fica guardada por
ninguem, nem pela Velosite.

### O plugin deste marketplace (opcional)

O conector sozinho ja funciona: ele entrega as ferramentas e as regras de uso. Este plugin
traz a versao completa da skill de enriquecimento, com o detalhamento do fluxo, das regras de
economia e do formato da ficha. E util para quem usa o Cowork com frequencia.

1. No Claude, va em Customize, depois Plugins.
2. Clique em Adicionar marketplace e cole a URL deste repositorio no GitHub.
3. Encontre o velosite-kipflow na lista, instale e ative.

## Como usar

Peca em linguagem natural, por exemplo:

- Enriqueca a empresa [nome]: dados, telefones e decisores.
- Busque o CNPJ da [empresa] e os telefones.
- Ache os decisores de marketing e vendas da [empresa] no LinkedIn.

## Custo

O nivel gratuito da Kipflow tem limite. Consultas alem disso sao pagas por consulta, direto na sua conta da Kipflow, sem mensalidade. A Velosite nao cobra pela ferramenta e nao recebe nada por consulta.

## Chave no conector (opcional)

Se preferir nao colar a chave em cada conversa, configure o conector com o header
`x-api-key: SUA_CHAVE` (ou `Authorization: Bearer SUA_CHAVE`), nos clientes que permitem
headers personalizados. Nesse caso o Claude nao precisa pedir a chave.

## Atualizacoes

As ferramentas rodam no servidor remoto da Velosite, entao melhorias chegam sem reinstalar nada. Mudancas no plugin chegam ao atualizar o marketplace.

Desde 23/11/2026 (conector 2.0.0, plugin 2.1.0), o conector usa a API V2 da Kipflow. Isso resolve o erro
`410 V1_DEPRECATED` que aparecia para contas Kipflow criadas a partir de 30/09/2026. A URL do
conector continua a mesma.
