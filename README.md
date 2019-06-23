# Eventix

Projeto destinado a criação de uma aplicação Elixir para gerenciar eventos da comunidade. Bem como, para servir de case open source do uso de Phoenix LiveView.

Este documento serve como um controlador das funcionalidades que foram, estão sendo e serão feitas na aplicação.

## Funcionalidades

    * Cadastro de usuários (a fazer);
    * Cadastro de grupos de encontros (a fazer);
    * Agendamento de eventos (a fazer);
    * Controle de propostas de palestras (a fazer);
    * Fórum de discussão (a fazer - porém com os grupos em telegram não sei até onde isso é viável);
    * Envio de notificações (a fazer);

## Stack usada

    * Erlang (mínima versão);
    * Elixir (mínima versão);
    * Phoenix com LiveView (1.4 mínima versão);
    * Postgres (mínima versão);

## Arquitetura sugerida

    * Apresentação - Aplicação Phoenix
    * Domínio - Aplicação Elixir
    * Infraestrutura - Aplicação Elixir com Ecto.Repo

Sugiro o uso de uma aplicação umbrella contendo as seguintes aplicações, cada uma representando uma camada com suas respectivas responsabilidade.

A camada de apresentação será responsável por gerenciar as chamadas http da aplicação, recebendo requisições e enviando respostas. É na camada de aplicação também que ficarão os contextos, que serão os orquestradores do uso da camada de Domínio e Infraestrutura, em outras palavras, as chamadas para essas outras duas camadas serão feitas nos contextos da camada de apresentação. Por isso, a camada de apresentação terá como dependências as camadas de Domínio e Infraestrutura.

A camada de Domínio ficará responsável pela execução de toda e qualquer regra de negócio. Dessa forma as demais camadas serão invólucros burros que desconhecem regras de negócio. Validadores, autenticadores, transformadores, processadores, etc., serão implementados dentro dessa camada. Essa camada não possui nenhuma dependência. O Domínio é completamente desvinculado da estratégia de apresentação e de infraestrutura.

A camada de Infraestrutura é a responsável por fazer acessos ao banco de dados, arquivos, entre outros. Assim como a camada de domínio, é completamente desvinculada das outras camadas.

Essa arquitetura auxilia a padronizar a aplicação como um todo e deixá-la flexível. Se algum dia a aplicação Phoenix for substituída, ninguém sentirá falta.

Pode-se criticar que a camada de apresentação possui alto acoplamento e que isso torna a mudança dificil. Porém, isso não se faz verdade caso seja respeitado o uso dos contextos como orquestradores. Se isso ocorrer, então mudanças serão simples e faceis de serem feitas.

No geral, a escolha dessa arquitetura é exatamente para facilitar mudanças. Em minha definição, uma boa arquitetura é aquela em que mudanças são faceis de serem feitas.

## Exemplos 

Um projeto, de minha autoria, que apresenta o uso dessa arquitetura pode ser visto em: [Phone_Billing](https://github.com/danielfoxp2/phone_billing)

Esse projeto foi feito como portfólio, usando os requisitos descritos em [Trabalhe na Olist](https://github.com/olist/work-at-olist). Nunca foi apresentado à Olist para saber se eles se interessariam em mim como dev, mas eu gostei muito do resultado.

### Exemplo de orquestramento

[Contexto Calls](https://github.com/danielfoxp2/phone_billing/blob/master/apps/billing_gateway/lib/billing_gateway/calls/calls.ex)

Pode-se ver no link acima o que seria essa orquestração a que me refiro. O contexto não tem responsabilidade além de receber uma requisição do controller, passar essa requisição para os módulos responsáveis em processá-la e responder ao controller o resultado do processamento.
Isso pode ser visto na função `process/2`, que utiliza módulos tanto da camada Infraestrutura quanto da camada Domínio. 

O contexto é, então, quem conhece os módulos que precisam ser chamados para que o fluxo de processamento ocorra. E só.

Outro exemplo consta no link abaixo, mais precisamente na função `calculate/1`.

[Contexto Bills](https://github.com/danielfoxp2/phone_billing/blob/master/apps/billing_gateway/lib/billing_gateway/bills/bills.ex)
    
p.s.: Olhando agora percebo que faltou refatorar o método `calculate_bill/1` e extrair a inserção de tarifas, de modo que pudesse ser apenas mais uma chamada no `calculate/1`, deixando a função `calculate_bill/1` com apenas uma responsabilidade.
p.s.2: Após discussão dos participantes sobre a arquitetura, todo o conteúdo a partir de `Exemplos` será removido e o conteúdo em `Arquitetura Sugerida` será alterado para refletir a arquitetura que de fato será utilizada, podendo também vir a ser removido.
