# Matriz de testes — revisão das evidências

Projeto: Lynfort Lab — Teste de autorização entre contas no Juice Shop.
Revisão: 30/10/2026. Execução manual por Victor Godoy, em laboratório próprio.

## Primeira execução

| Caso | Sessão informada | Objeto solicitado | Regra esperada | Resultado documentado | Base disponível |
| --- | --- | --- | --- | --- | --- |
| AUT-01 | A | Carrinho 6, de A | Permitir | Apple Juice, UserId 25 | E-03; URL e JSON fornecidos no chat |
| AUT-02 | B | Carrinho 7, de B | Permitir | Green Smoothie, UserId 26 | E-04; URL e JSON fornecidos no chat |
| AUT-03 | A | Carrinho 7, de B | Negar sem revelar dados | HTTP 200; carrinho 7, UserId 26, Green Smoothie | E-05 e E-06; executor confirmou manutenção da sessão A |
| AUT-04 | B | Carrinho 6, de A | Negar sem revelar dados | Anexo observado no histórico mostra janela privativa, GET final 6, HTTP 200, UserId 25 e Apple Juice | Arquivo original indisponível; sem captura distribuível |

## Interpretação e limites

As consultas cruzadas sustentam a reprodução didática de leitura de carrinho entre contas, considerando o procedimento e a identidade de sessão informados pelo executor. As capturas mostram objetos e conteúdo retornado; não permitem verificar isoladamente qual credencial foi enviada. Não foram coletados tokens ou HAR bruto. A janela privativa distingue contextos, mas sozinha não prova a identidade autenticada.

A associação de propriedade foi estabelecida pelos carrinhos exibidos nas contas fictícias e pelos controles legítimos. HTTP 200 e status success não foram usados sozinhos como prova: os produtos diferentes e os identificadores dos carrinhos sustentam a comparação.

Nenhuma alteração de carrinho alheio, enumeração ampla ou acesso externo foi testado. O laboratório é intencionalmente vulnerável; o resultado não é uma descoberta inédita. Nenhuma correção foi implementada ou retestada.

## Segunda execução, após reinício

E-08 e E-09 registram somente os controles próprios A e B. Não houve repetição documentada das consultas cruzadas nesta execução. Os IDs coincidiram com os anteriores, mas os registros não foram combinados como se pertencessem à mesma execução. A falha de login anterior e o novo cadastro foram relatados; não houve investigação da causa de reinicialização dos dados.

## Arquivo ausente aceito como limitação

O anexo original denominado `01.png`, da consulta B para A, não estava mais acessível durante esta revisão. O conteúdo foi observado no histórico, mas não há cópia desse arquivo na pasta do projeto. O executor confirmou em 30/10/2026 que não dispõe mais da imagem e autorizou concluir a documentação com essa limitação. Não foi gerada captura substituta nem atribuído hash ao arquivo ausente. E-07 permanece reservado, sem arquivo e fora dos manifestos.
