# Remediação conceitual e critérios de reteste

## O que precisa mudar em uma aplicação protegida

Toda leitura de carrinho deve verificar no servidor se a identidade autenticada pode acessar o objeto solicitado. Essa decisão precisa preceder a devolução dos dados. A consulta pode restringir o objeto pela combinação entre identificador e titular autorizado, ou aplicar uma política equivalente verificada no servidor.

O titular permitido deve vir de uma sessão autenticada e validada, não de um UserId declarado livremente pelo cliente. Se houver compartilhamento ou delegação, a regra deve ser explícita e testada. Negar por padrão quando não houver permissão.

Uma resposta negada não deve incluir produtos, titular, metadados ou outros detalhes do carrinho alheio. A escolha entre 403 e 404 depende do contrato da aplicação e da política de exposição da existência do objeto. O critério principal é impedir a leitura indevida, mantendo um comportamento consistente.

Usar UUIDs dificulta adivinhação, mas não substitui autorização. Esconder elementos da interface ou confiar em um identificador no navegador também não protege a operação do servidor.

## Estado real deste trabalho

Nenhum código do Juice Shop foi alterado. Nenhum patch, nova versão corrigida ou teste pós-correção foi executado. O código-fonte exato da imagem não foi revisado; a localização de uma checagem ausente ou incorreta continua uma hipótese de implementação, não uma constatação em uma linha de código.

O produto é um ambiente de ensino intencionalmente vulnerável. Uma demonstração de correção em aplicação própria seria uma extensão separada, não uma correção do Juice Shop.

## Matriz de aceitação de uma correção futura

Todos os casos abaixo são **planejados e não executados após correção**.

| Caso | Contexto | Resultado exigido |
| --- | --- | --- |
| RET-01 | A solicita carrinho A | Retorna somente os dados permitidos de A |
| RET-02 | B solicita carrinho B | Retorna somente os dados permitidos de B |
| RET-03 | A solicita carrinho B | Nega sem revelar dados de B |
| RET-04 | B solicita carrinho A | Nega sem revelar dados de A |
| RET-05 | Consulta sem autenticação | Trata a ausência de sessão conforme contrato, sem revelar carrinho privado |
| RET-06 | Identificador inexistente | Resposta consistente, sem dados de outros objetos |

Os casos adicionais de ausência de sessão e objeto inexistente ampliariam a validação defensiva; não foram testados na execução original e exigem inclusão explícita no próximo roteiro.

## Evidência necessária para encerrar como corrigido

1. Identificar o código alterado e a versão implantada no ambiente retestado.
2. Confirmar que a mudança chegou ao processo que atende às consultas.
3. Recriar contas e objetos próprios e registrar a associação de cada titular.
4. Executar os controles permitidos e negados, preservando a identidade de sessão e sem publicar segredos.
5. Vincular as respostas revisadas à versão e aos casos de reteste, indicando data, ambiente e limitações.

Um commit ou resposta 403 isolados não bastam para encerrar. Enquanto não houver implantação comprovada e resultados comparativos, o estado permanece **correção não implementada neste laboratório**.
