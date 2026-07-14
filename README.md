# Marketplace de plugins da Velosite

Este repositorio e um marketplace de plugins para o Claude (Cowork). Instale sem baixar arquivo: cole a URL deste repositorio no Claude.

## Plugin disponivel

### velosite-kipflow
Enriquecimento de dados de empresas dentro do Claude. A partir do nome, CNPJ ou dominio de uma empresa, retorna dados cadastrais, telefones, emails e os decisores certos no LinkedIn (por cargo, area e senioridade).

O servidor roda remoto (hospedado pela Velosite), entao voce nao precisa instalar Node nem rodar nada localmente. Cada usuario conecta a propria chave de API do Kipflow.

## Como instalar (sem download)

1. No Claude, abra Customize e depois Plugins.
2. Clique em Adicionar marketplace e cole a URL deste repositorio no GitHub.
3. Encontre o velosite-kipflow na lista e clique em instalar.
4. Ative o plugin. Ao ativar, informe a sua chave de API do Kipflow no campo pedido.
   Se a caixa da chave nao aparecer na hora, entre no menu do plugin e escolha Configure options para colar a chave.

A chave fica guardada com seguranca no cofre do sistema, nunca em arquivo aberto.

## Como obter a chave do Kipflow

1. Crie uma conta em platform.kipflow.io.
2. Va em API Keys e gere uma nova chave.
3. Cole essa chave no Claude quando pedido.

O nivel gratuito do Kipflow tem limite. Consultas alem disso sao pagas por consulta, na conta do proprio usuario. A Velosite nao cobra pela ferramenta.

## Como usar

Peca em linguagem natural, por exemplo:
- Enriqueca a empresa [nome]: dados, telefones e decisores.
- Busque o CNPJ da [empresa] e os telefones.
- Ache os decisores de marketing e vendas da [empresa] no LinkedIn.

## Atualizacoes

O plugin aponta para o servidor remoto da Velosite. Melhorias nas ferramentas chegam sem reinstalar. Mudancas no proprio plugin chegam ao atualizar o marketplace.
