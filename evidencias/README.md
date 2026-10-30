# Evidências e proveniência

O pacote reúne **nove capturas reais** enviadas pelo executor. Os arquivos originais permanecem na pasta de trabalho local. As cópias em `sanitizadas/` foram revisadas visualmente e mantidas sem alteração dos bytes: nas imagens disponíveis não foi identificado valor de senha, cookie ou token que exigisse ocultação.

O termo `sanitizadas/` indica o conjunto selecionado para distribuição. Não significa que houve edição de imagem ou anonimização completa: os e-mails fictícios, o usuário local `lynfort`, nomes de produtos, identificadores e horários permanecem visíveis para preservar o contexto.

## Índice das capturas

| ID | Arquivo revisado | O que mostra | Limite |
| --- | --- | --- | --- |
| E-01 | [Ambiente Docker](sanitizadas/E-01-ambiente-docker.png) | Contêiner, imagem, Image ID, RepoDigest e porta loopback | Metadados locais; sem verificação em registro remoto |
| E-02 | [Versão e data](sanitizadas/E-02-versao-data.png) | 20.3.0 e `2026-10-29T13:48:43-03:00` | Não informa nome do fuso nem data de todos os testes |
| E-03 | [Carrinho da conta A](sanitizadas/E-03-carrinho-conta-A.png) | E-mail fictício A, Apple Juice e quantidade 1 | Não mostra ID do carrinho ou a requisição |
| E-04 | [Carrinho da conta B](sanitizadas/E-04-carrinho-conta-B.png) | E-mail fictício B, Green Smoothie e janela privativa | Não mostra ID do carrinho ou a requisição |
| E-05 | [Retorno da consulta atribuída a A → B](sanitizadas/E-05-A-acessa-carrinho-B.png) | GET com final 7, HTTP 200, carrinho 7 e UserId 26 | Identidade da credencial informada pelo executor; Products recolhido |
| E-06 | [Produto no retorno de B](sanitizadas/E-06-A-recebe-produto-B.png) | Carrinho 7, UserId 26 e Green Smoothie | Complementa E-05, sem mostrar autenticação |
| E-07 | Sem arquivo | Anexo histórico do sentido B → A | Original indisponível; não reconstruído, sem hash e fora da contagem |
| E-08 | [Controle A após reinício](sanitizadas/execucao-02/E-08-controle-conta-A.png) | GET final 6, HTTP 200, UserId 25 e Apple Juice | Segunda execução; não comprova acesso cruzado |
| E-09 | [Controle B após reinício](sanitizadas/execucao-02/E-09-controle-conta-B.png) | Carrinho 7, UserId 26 e Green Smoothie | Segunda execução; sem status HTTP no recorte, que foi visto em captura anterior do histórico |
| E-10 | [Encerramento Docker](sanitizadas/E-10-encerramento-docker.png) | Stop retornando o nome e estado `Exited (255)` | Causa da saída e horário absoluto não determinados |

## Registros textuais derivados

[Campos dos controles da primeira execução](transcricoes/execucao-01-controles.json) é uma seleção de campos dos JSONs que o executor copiou para a conversa. Foi organizada posteriormente e **não é exportação HTTP bruta, nova resposta do servidor ou arquivo recebido diretamente da VM**. Campos omitidos não são substituídos por valores estimados. A origem e o tipo do registro estão no próprio JSON.

A URL completa de cada consulta na primeira execução foi fornecida separadamente pelo executor. Os prints de Network preservados mostram o segmento final, e não todos os cabeçalhos.

## Arquivo ausente

O anexo denominado `01.png` foi observado no histórico com janela privativa, consulta final 6, HTTP 200 e Apple Juice no carrinho 6, UserId 25. A atribuição à sessão B deriva do procedimento relatado. O executor informou que não dispõe mais do arquivo e autorizou concluir o projeto com essa limitação.

E-07 é uma referência reservada no índice, sem imagem ou miniatura. Não foi fabricada, simulada ou substituída uma captura de execução. Essa ausência reduz a verificabilidade do sentido B → A no pacote distribuível.

## Integridade

O [manifesto SHA-256](manifesto-sha256.txt) cobre os nove PNGs revisados e o JSON derivado, com caminhos relativos a esta pasta. Uma alteração nos arquivos muda seus hashes. Isso permite comparar o conteúdo à versão do manifesto, mas não comprova origem, completude da execução, credencial enviada ou veracidade do relato.

O arquivo local `manifesto-originais-sha256.txt` mantém a referência dos originais; ele e `originais/` são excluídos de uma futura publicação pelo `.gitignore`. O índice descreve o conjunto recebido, sem alegar cadeia de custódia forense.

## Separação dos registros

- Primeira execução: E-01 a E-06 e observação histórica B → A com arquivo ausente.
- Segunda execução após reinício: E-08 e E-09, somente controles de carrinho próprio.
- Encerramento recebido em 30/10/2026: E-10. A data de recebimento não é o horário de execução comprovado na imagem.
- Visuais de `images/`: capa e diagrama explicativos, sem valor de captura do teste.
