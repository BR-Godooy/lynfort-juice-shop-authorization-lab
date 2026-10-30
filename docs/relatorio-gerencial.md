# Lynfort Lab — Relatório gerencial de autorização entre contas

Aplicação: OWASP Juice Shop 20.3.0 · Executor: Victor Godoy · Revisão documental: 30/10/2026.

## Resumo executivo

Na primeira execução do laboratório, Victor Godoy informou que manteve a conta fictícia A autenticada e consultou o carrinho conhecido da conta fictícia B. As capturas E-05 e E-06, descritas nos registros do projeto, mostram o retorno do carrinho 7, associado ao usuário 26, com o produto Green Smoothie. O controle da conta A havia mostrado o carrinho 6, associado ao usuário 25, com Apple Juice. A separação entre os dados das duas contas deveria impedir essa leitura cruzada.

A documentação sustenta uma reprodução didática de leitura de carrinho entre contas, com uma limitação importante: a identidade da sessão usada na consulta cruzada depende da confirmação do executor. As capturas mostram o conteúdo retornado, mas não comprovam isoladamente qual credencial acompanhou a requisição. Não há evidência de furto de sessão, alteração de carrinho ou acesso a dados reais de terceiros.

A revisão documental foi feita com apoio de IA sobre registros e capturas fornecidos por Victor, responsável pela execução manual. A IA não acessou a VM e o código-fonte não foi revisado. O resultado pertence a uma aplicação intencionalmente vulnerável para treinamento, em ambiente local. Não constitui descoberta inédita nem avaliação de uma aplicação comercial da Lynfort. A versão identificada foi 20.3.0; outras versões e uma versão corrigida não foram avaliadas.

## Contexto

O objetivo do projeto foi aprender a diferença entre autenticação e autorização. Autenticação identifica a conta conectada. Autorização decide se essa conta pode acessar um objeto específico, como o carrinho de outra pessoa. Estar conectado não deve conceder acesso aos dados de todas as contas.

O ambiente registrado utiliza Kali em uma máquina virtual VirtualBox e um contêiner chamado `juice-shop`, com a imagem `bkimminich/juice-shop`. A porta publicada aparece vinculada a `127.0.0.1:3000` dentro do Kali. A versão 20.3.0 e a data `2026-10-29T13:48:43-03:00` foram registradas em E-02. E-01 registra a identidade da imagem; não houve comparação com registro remoto. O nome do fuso não foi consultado.

O escopo compreendeu duas contas fictícias próprias, seus carrinhos e consultas locais. O produto diferente em cada carrinho permitiu reconhecer o conteúdo de A e de B. A operação analisada foi somente leitura.

## Detalhes da falha

A regra esperada era simples: A poderia consultar o carrinho de A; B poderia consultar o carrinho de B; cada conta deveria ser impedida de ler o carrinho da outra.

| Caso da primeira execução | Resultado documentado | Alcance da evidência |
| --- | --- | --- |
| A consulta o próprio carrinho — AUT-01 | Carrinho 6, usuário 25, Apple Juice | Controle legítimo registrado em E-03 e nas notas do executor |
| B consulta o próprio carrinho — AUT-02 | Carrinho 7, usuário 26, Green Smoothie | Controle legítimo registrado em E-04 e nas notas do executor |
| A consulta o carrinho de B — AUT-03 | HTTP 200 e retorno do carrinho 7, usuário 26, Green Smoothie | E-05 e E-06; manutenção da sessão A informada pelo executor |
| B consulta o carrinho de A — AUT-04 | Retorno de carrinho 6, usuário 25, Apple Juice, descrito no histórico | Anexo `01.png` observado no histórico; arquivo ausente no projeto |

O conteúdo do carrinho, e não apenas a resposta HTTP 200, sustenta a comparação. Para AUT-03, os registros descrevem a troca somente do identificador conhecido do carrinho, mantendo a sessão A. Se essa sessão foi preservada conforme relatado, o retorno dos dados de B demonstra uma falha de autorização horizontal: duas contas de mesmo nível deixam de ter seus dados separados.

O código-fonte não foi analisado. Portanto, não se atribui o resultado a uma função específica, a uma linha de código ou a uma correção conhecida. O comportamento relatado é compatível com uma decisão insuficiente de autorização ao consultar o carrinho, mas o mecanismo interno não foi determinado.

## Análise de explorabilidade

O efeito sustentado no cenário é a leitura do carrinho de outra conta fictícia por uma conta autenticada que conhece aquele identificador. A propriedade dos carrinhos foi associada aos controles legítimos e ao procedimento informado pelo executor. Não houve enumeração ampla de identificadores.

Em uma aplicação real com o mesmo comportamento, a exposição de itens de outra pessoa poderia comprometer a privacidade de suas escolhas de compra. Essa é uma consequência possível do mecanismo, não um dano ocorrido neste laboratório. Não foram demonstrados alteração de dados, mudança de privilégios, acesso administrativo ou acesso a outros objetos.

A confirmação técnica permanece limitada pela ausência de uma evidência independente da identidade autenticada em cada consulta cruzada. AUT-04 tem também uma limitação de conservação: o anexo mencionado no histórico não possui cópia local disponível. A janela privativa descrita nesse anexo distingue contextos de navegação, mas não prova sozinha a identidade da conta.

## Prova de conceito

A demonstração disponível é o registro da primeira execução manual com duas contas e produtos diferentes. E-03 e E-04 documentam os controles próprios. E-05 registra a consulta ao carrinho 7 e o retorno bem-sucedido; E-06 complementa essa resposta com o produto Green Smoothie expandido. A manutenção da sessão A foi confirmada pelo executor, sem coleta de tokens, cookies ou arquivo bruto de tráfego.

O resultado inverso, B para A, está descrito no histórico e na matriz como AUT-04. A captura original `01.png` continua ausente. Ela não foi substituída por outra imagem e não pode receber verificação de integridade enquanto o arquivo não estiver disponível.

Após um reinício, os registros E-08 e E-09 documentam uma segunda execução com os controles próprios de A e B. Embora os identificadores coincidam, essa execução não possui repetição documentada das consultas cruzadas e não confirma novamente o resultado da primeira.

E-10 registra o encerramento do contêiner, com estado posterior `Exited (255)`. A causa desse código de saída não foi determinada. A evidência foi recebida em 30/10/2026; a captura não comprova a data e a hora exatas do encerramento.

## Remediação

Em uma aplicação protegida, o servidor deve identificar a conta autenticada e verificar sua permissão sobre o carrinho solicitado antes de devolver qualquer dado. O identificador recebido deve selecionar o objeto, sem conceder acesso por si só. Uma resposta negada deve preservar a confidencialidade do conteúdo, enquanto consultas ao próprio carrinho permanecem permitidas.

Ocultar um botão ou trocar identificadores numéricos por valores difíceis de adivinhar não substitui essa verificação. A decisão precisa ocorrer no servidor para cada consulta protegida.

Essas recomendações são conceituais. Nenhuma correção foi implementada no Juice Shop e não existe resultado de reteste de correção neste projeto. A edição intencionalmente vulnerável foi utilizada para aprendizado, dentro do escopo local documentado.

## Síntese

Os registros sustentam o exercício didático de leitura entre carrinhos de contas fictícias no Juice Shop 20.3.0, considerando a identidade das sessões informada pelo executor. O resultado A para B tem capturas de objeto e produto; o resultado B para A permanece com o anexo local ausente. A segunda execução contém somente controles próprios.

A conclusão se limita à leitura relatada e documentada, sem atribuição de causa em código, avaliação de outras versões ou alegação de correção. O ambiente foi encerrado conforme E-10. As referências internas para o detalhe das evidências são [ambiente observado](ambiente.md) e [matriz de testes](matriz-de-testes.md).
