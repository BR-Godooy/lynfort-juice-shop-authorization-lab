# Escopo e metodologia

## Pergunta de trabalho

Uma conta de teste consegue ler o carrinho de outra conta ao alterar somente o identificador solicitado, mantendo a própria autenticação?

O comportamento protegido esperado é permitir A → A e B → B, negando A → B e B → A sem revelar dados do outro titular. O Juice Shop é intencionalmente vulnerável; essa regra define a fronteira estudada, não uma promessa de segurança do aplicativo de treinamento.

## Autorização e ambiente

Victor Godoy executou manualmente o laboratório próprio no Juice Shop em Docker dentro do Kali, com porta publicada em `127.0.0.1:3000`. Foram usadas apenas `conta.a@lynfort.invalid` e `conta.b@lynfort.invalid`, contas fictícias do executor. `.invalid` identifica os endereços fictícios usados no exercício; nenhuma caixa postal foi acessada.

O trabalho usou o contêiner existente e Firefox DevTools. Não foi necessário Burp, scanner, script de ataque ou serviço externo. Leituras de referências oficiais para escrever o relatório são pesquisa documental, separada do tráfego testado.

O plano inicial permanece como registro histórico local, preservado literalmente e excluído de uma futura publicação. Seus termos ambíguos não ampliam a autorização; o escopo operacional é o descrito neste documento.

## Limites práticos

- Operação investigada: consulta GET do carrinho próprio e do objeto conhecido da outra conta.
- Sem alteração ou exclusão de dados alheios, compras, força bruta, carga, varredura ou enumeração ampla.
- Sem coleta de credenciais reais, publicação de sessão, cookies, tokens ou HAR bruto.
- Sem teste no Juice Shop público ou em aplicações de terceiros.
- Publicação do portfólio somente após autorização específica do autor.

## Controles e atribuição

A conta A usa a janela normal e recebe Apple Juice; a conta B usa janela privativa e recebe Green Smoothie. Produtos diferentes permitem distinguir o conteúdo recebido. A propriedade é associada pelas telas dos respectivos carrinhos e pelos controles legítimos.

Na consulta cruzada, o procedimento relatado preserva a sessão de origem e muda apenas o identificador do carrinho. As capturas comprovam os campos retornados; a credencial efetivamente enviada não foi preservada de forma verificável no pacote. A interpretação usa essa confirmação do executor e informa a limitação.

## Critérios de interpretação

HTTP 200 ou `status: success` sozinho não confirma acesso indevido. É necessário comparar o carrinho, seu titular e o produto com o controle da outra conta. Um produto público no catálogo não prova leitura de carrinho privado: neste estudo, a evidência relevante é o retorno da associação do produto ao carrinho e ao UserId de outro titular.

O achado é uma reprodução didática documentada, com conclusão limitada ao procedimento relatado. Não é uma validação independente de sessão, avaliação completa de segurança ou revisão do código-fonte. A ausência do arquivo B → A reduz a documentação desse sentido; não foi suprida por imagem simulada.

## Duas execuções

Na primeira execução foram acompanhados controles e consultas cruzadas. Após reinício, as credenciais anteriores falharam e as contas foram cadastradas novamente, conforme relato. A execução 02 contém somente novos controles. IDs iguais não provam continuidade de dados: os timestamps de criação diferem. Os resultados foram mantidos separados.

## Encerramento

E-10 registra o contêiner parado. O retorno `Exited (255)` foi preservado sem atribuir uma causa não investigada. Não houve correção da aplicação ou reteste após correção.
