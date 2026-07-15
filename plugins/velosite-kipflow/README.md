# Marketplace de plugins da Velosite

Este repositorio e um marketplace de plugins para o Claude. Instale sem baixar arquivo: cole a URL deste repositorio no Claude.

## O que e o velosite-kipflow

Enriquecimento de dados de empresas dentro do Claude. A partir do nome, do CNPJ ou do dominio de uma empresa, retorna dados cadastrais, telefones, emails e os decisores certos no LinkedIn (por cargo, area e senioridade).

O servidor roda remoto, hospedado pela Velosite, entao voce nao instala Node nem roda nada localmente. Cada usuario conecta a propria chave de API da Kipflow.

## Instalacao: dois passos, uma unica vez

Voce faz isso uma vez. Depois nunca mais precisa informar a chave.

### Passo 1: adicione o conector (e a sua chave)

E aqui que a sua chave fica guardada com seguranca pelo Claude, e enviada em toda consulta automaticamente.

1. Crie sua conta gratuita em platform.kipflow.io, va em API Keys e gere uma chave. Copie.
2. No Claude, va em Customize, depois Connectors, depois Add custom connector.
3. Em URL, cole:
   ```
   https://kipflow-mcp-vercel.vercel.app/api/mcp
   ```
4. Em Request headers, adicione:
   - Nome do header: `Authorization`
   - Valor: `Bearer SUA_CHAVE` (troque SUA_CHAVE pela chave que voce copiou, mantendo a palavra Bearer e o espaco)
5. Salve.

Pronto. A chave fica no armazenamento seguro do Claude, nunca aparece no historico das conversas e nao precisa ser informada de novo.

### Passo 2: instale o plugin (a inteligencia de uso)

1. No Claude, va em Customize, depois Plugins.
2. Clique em Adicionar marketplace e cole a URL deste repositorio no GitHub.
3. Encontre o velosite-kipflow na lista e clique em instalar. Ative.

O plugin traz a skill que ensina o Claude a enriquecer um lead do jeito certo e economico: resolver o nome da empresa em CNPJ, puxar so os dados necessarios e montar a ficha de prospeccao.

## Como usar

Peca em linguagem natural, por exemplo:

- Enriqueca a empresa [nome]: dados, telefones e decisores.
- Busque o CNPJ da [empresa] e os telefones.
- Ache os decisores de marketing e vendas da [empresa] no LinkedIn.

## Custo

O nivel gratuito da Kipflow tem limite. Consultas alem disso sao pagas por consulta, direto na sua conta da Kipflow, sem mensalidade. A Velosite nao cobra pela ferramenta e nao recebe nada por consulta.

## Se voce pular o Passo 1

As ferramentas funcionam mesmo sem o conector configurado com header: nesse caso o Claude vai pedir a sua chave na conversa e reusa-la nas chamadas seguintes daquela conversa. E uma rede de seguranca, mas tem duas desvantagens: a chave fica visivel no historico do chat e voce precisa informar de novo a cada nova conversa. O Passo 1 resolve os dois.

## Atualizacoes

As ferramentas rodam no servidor remoto da Velosite, entao melhorias chegam sem reinstalar nada. Mudancas no plugin chegam ao atualizar o marketplace.
