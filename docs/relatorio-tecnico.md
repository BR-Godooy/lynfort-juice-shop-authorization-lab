# Lynfort Lab — Relatório técnico de autorização horizontal

Aplicação: OWASP Juice Shop 20.3.0 · Cenário: View Basket · Executor: Victor Godoy · Revisão documental: 30/10/2026.

## Resumo executivo

Na primeira execução manual, Victor Godoy informou que preservou a sessão da conta fictícia A e alterou a consulta do seu carrinho 6 para o carrinho conhecido 7, pertencente à conta fictícia B. E-05 e E-06 são descritas nos registros como uma resposta HTTP 200, com `status: success`, `data.id: 7`, `UserId: 26` e o produto `id: 22`, `name: Green Smoothie`. O controle próprio de A havia retornado carrinho 6, usuário 25 e Apple Juice. Se a sessão A foi mantida conforme informado, o servidor devolveu o conteúdo de B em uma consulta que deveria respeitar a separação entre as contas.

O efeito documentado é leitura cruzada de carrinho no laboratório local. Não foi demonstrado furto ou troca de sessão, modificação de carrinho, mudança de privilégio ou acesso a dados reais de terceiros. As capturas descritas mostram o objeto e seu conteúdo, mas não identificam isoladamente a credencial enviada; essa parte do procedimento se apoia no relato do executor.

A revisão documental, com apoio de IA, abrangeu os registros de ambiente, a matriz e as capturas preservadas. Victor executou os testes manuais; a IA não realizou consultas na VM. Não houve exame do código-fonte. Este relatório descreve um comportamento intencional de treinamento do Juice Shop, sem reivindicação de descoberta inédita. A versão registrada é 20.3.0; não foram estabelecidos uma primeira versão afetada, um intervalo de versões ou uma versão corrigida.

## Contexto

O cenário usa duas contas fictícias de mesmo nível e carrinhos distintos. A pode acessar seu próprio carrinho e B pode acessar o seu; a autenticação de uma delas não deve autorizar a leitura do objeto da outra. A propriedade foi associada ao conteúdo exibido em cada conta e aos controles legítimos relatados.

| Elemento | Registro disponível |
| --- | --- |
| Ambiente | Kali em máquina virtual VirtualBox, com Docker |
| Aplicação | OWASP Juice Shop 20.3.0, conforme E-02 |
| Contêiner e imagem | `juice-shop` e `bkimminich/juice-shop`, conforme E-01 |
| Porta publicada | `127.0.0.1:3000` para `3000/tcp` no Kali, conforme E-01 |
| Data exibida | `2026-10-29T13:48:43-03:00`, conforme E-02; nome do fuso não consultado |
| Conta A, primeira execução | Carrinho 6, `UserId: 25`, Apple Juice |
| Conta B, primeira execução | Carrinho 7, `UserId: 26`, Green Smoothie |

E-01 registra o ID de conteúdo e o digest local da imagem, transcritos em [Ambiente](ambiente.md). Não houve comparação desses metadados com um registro remoto nem revisão do código exato da imagem. A configuração interna de autorização não foi inspecionada. O vínculo loopback registrado pertence ao contexto de rede do Kali; não estabelece acesso pela mesma URL no sistema hospedeiro.

O escopo foi restrito ao laboratório local, a duas contas próprias e aos objetos conhecidos dessas contas. Não foram realizados enumeração ampla, alteração de carrinho alheio, avaliação de serviços externos ou análise de tráfego de terceiros.

## Detalhes da falha

Os controles da primeira execução associaram A ao carrinho 6, usuário 25 e Apple Juice, e B ao carrinho 7, usuário 26 e Green Smoothie. E-03 e E-04, além de URL e JSON fornecidos pelo executor no histórico, são as referências registradas para esses acessos legítimos. Essa distinção de conteúdo é necessária para interpretar a consulta cruzada: uma resposta bem-sucedida sem os dados de B não bastaria para demonstrar a falha.

Em AUT-03, o valor controlado descrito pelo executor foi o identificador do carrinho na consulta: de 6 para 7. Ele confirmou que usou a janela normal e manteve a sessão A. As URLs fornecidas no histórico foram `http://127.0.0.1:3000/rest/basket/6` para A e `http://127.0.0.1:3000/rest/basket/7` para B. As capturas preservam o final da consulta; os cabeçalhos de autenticação não integram o pacote.

E-05 mostra, segundo o registro de evidência, a requisição GET com final 7 e HTTP 200. A resposta contém `status: success`, `data.id: 7` e `UserId: 26`, com `Products` recolhido. E-06 complementa esse retorno com `Products` expandido, incluindo `id: 22` e `name: Green Smoothie`. Assim, as duas capturas documentam o objeto de B e o produto usado no controle de B. A identidade da sessão A não pode ser extraída dessas capturas isoladamente.

Em AUT-04, a matriz registra uma captura enviada no histórico com janela privativa, GET de final 6, HTTP 200, `UserId: 25` e Apple Juice. O arquivo correspondente, `01.png`, não está disponível no projeto. O registro sustenta que esse resultado foi apresentado no histórico, mas a ausência do original impede sua inspeção independente a partir do conjunto local. Não foi criada uma captura substituta nem atribuído hash ao arquivo ausente.

O comportamento descrito é compatível com autorização insuficiente sobre o objeto solicitado: uma conta autenticada recebe o carrinho associado a outra conta. A causa interna permanece indeterminada, pois não houve inspeção de código, de verificações no servidor ou do histórico de versões. Não há fundamento para indicar uma função, uma linha, uma dependência ou uma alteração que introduziu o comportamento.

Como classificação conceitual, esse padrão corresponde a IDOR e é compatível com [CWE-639](https://cwe.mitre.org/data/definitions/639.html), pelo uso de uma chave de objeto controlada pelo cliente na leitura de dados de outro titular. A comparação entre contas se alinha ao [WSTG-ATHZ-04](https://wstg.owasp.org/v4.2/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References/). Esses mapeamentos não acrescentam evidência sobre a implementação da imagem instalada.

## Análise de explorabilidade

A capacidade estreita sustentada pelos registros é consultar um carrinho conhecido de outra conta usando a própria sessão autenticada, desde que a preservação dessa sessão tenha ocorrido conforme relatado. A fronteira relevante é a propriedade dos dados entre duas contas de mesmo nível; não há evidência de mudança de papel ou obtenção de privilégios administrativos.

As condições descritas são acesso autenticado à aplicação local, conhecimento prévio do identificador do outro carrinho fictício e manutenção do contexto de autenticação da conta solicitante. Os identificadores foram obtidos dos controles próprios dessas duas contas. Nada nos registros estabelece facilidade de descoberta de outros identificadores ou alcance sobre outros recursos.

Os controles legítimos A para A e B para B ajudam a associar os produtos aos seus respectivos carrinhos. O retorno do produto distinto de B em AUT-03 afasta a interpretação de que somente um código HTTP de sucesso ou um carrinho vazio foi apresentado. Contudo, os artefatos não excluem, por si só, uma mistura de sessões: a confirmação de que a credencial permaneceu sendo A é do executor. A janela privativa de AUT-04 separa contextos de navegação, mas também não comprova sozinha qual conta estava autenticada.

A ausência de autenticação, uma rejeição de acesso cruzado em uma aplicação corrigida e outras variantes não possuem resultados documentados. Nenhuma inferência de escrita, acesso administrativo, execução de código ou exposição de dados reais decorre das evidências disponíveis.

O impacto estudado incide sobre a confidencialidade da associação entre titular e itens do carrinho. No ambiente local com dados fictícios, o alcance é restrito ao exercício. Não foi medida frequência de sucesso em execuções repetidas nem probabilidade de exploração em produção. Por isso, não foi atribuída uma severidade numérica ou extrapolada uma classificação de risco para aplicações reais. A inexistência de dano real neste cenário não elimina a relevância da regra de autorização.

## Prova de conceito

A prova disponível é documental e se refere a uma execução manual já realizada pelo executor no laboratório. Não há programa de reprodução ou execução nova associado a este relatório.

| Evidência | Papel no cenário | Limitação relevante |
| --- | --- | --- |
| E-01 e E-02 | Imagem e digest local, porta, versão e data registrada | Sem revisão de fonte ou verificação do digest em registro remoto |
| E-03 | Tela da conta A com Apple Juice | Carrinho 6 e UserId 25 constam do JSON fornecido no histórico, não dessa tela |
| E-04 | Tela da conta B com Green Smoothie | Carrinho 7 e UserId 26 constam do JSON fornecido no histórico, não dessa tela |
| E-05 | Consulta cruzada ao carrinho 7; HTTP 200, objeto 7, usuário 26 | Produto recolhido e identidade da sessão não visível isoladamente |
| E-06 | Conteúdo expandido do carrinho 7 com Green Smoothie, produto 22 | Preservação da sessão A continua baseada no relato |
| Anexo `01.png` do histórico | Resultado inverso relatado, B para A | Original ausente do projeto; inspeção local indisponível |
| E-08 e E-09 | Controles próprios da segunda execução | Não são repetição das consultas cruzadas |
| E-10 | Contêiner encerrado e estado posterior `Exited (255)` | Causa do código e horário exato do encerramento não determinados |

Na segunda execução, após reinício, o executor relatou carrinho vazio, falha de login com as credenciais anteriores e novo cadastro/login de A. E-08 registra o controle de A com carrinho 6, usuário 25 e Apple Juice. E-09 registra o controle de B com carrinho 7, usuário 26 e Green Smoothie. A coincidência dos identificadores não torna esses registros parte da primeira execução. Não houve repetição documentada de AUT-03 ou AUT-04 nessa segunda execução, e a causa da mudança de estado dos dados não foi investigada.

Não foram coletados tokens, cookies ou arquivo bruto de tráfego para este relatório. E-10 foi recebida em 30/10/2026 e documenta o contêiner parado; essa data de recebimento não comprova o instante de execução mostrado na captura.

## Remediação

A proteção necessária é uma decisão de autorização no servidor antes de retornar o carrinho. A identidade autenticada e o proprietário do objeto solicitado devem permanecer valores distintos durante essa decisão. O servidor deve permitir o acesso somente quando a conta tiver a permissão correspondente, e deve negar uma consulta sem permissão sem devolver os dados do carrinho.

Uma alternativa de desenho é resolver o carrinho a partir da identidade autenticada quando a operação representar exclusivamente o carrinho próprio. Quando a operação aceitar um identificador de objeto, esse identificador deve ser acompanhado da verificação efetiva de propriedade ou permissão. A adequação ao código instalado não foi avaliada.

Esconder o identificador na interface ou usar identificadores difíceis de adivinhar não estabelece essa permissão. O acesso legítimo ao próprio carrinho precisa continuar possível sob a política de autorização adotada.

As medidas acima são propostas conceituais fundamentadas no comportamento descrito. Não houve alteração no Juice Shop, inspeção de correção publicada ou reteste de uma versão corrigida. Portanto, não se declara remediação concluída.

## Síntese

O cenário documentado no Juice Shop 20.3.0 sustenta leitura horizontal de carrinho entre duas contas fictícias, considerando a manutenção das sessões informada pelo executor. AUT-03 possui registro do retorno do carrinho e produto de B; a identidade da credencial permanece uma limitação de proveniência. AUT-04 possui relato e descrição de captura no histórico, com o arquivo original ausente.

Os controles da segunda execução foram mantidos separados dos resultados cruzados da primeira. O contêiner foi encerrado conforme E-10. Não foram estabelecidos causa em código, alcance sobre outras versões, impacto além de leitura ou correção aplicada. Os registros de referência são [ambiente observado](ambiente.md), [matriz de testes](matriz-de-testes.md), [escopo](escopo-e-metodologia.md) e [índice de evidências](../evidencias/README.md).
