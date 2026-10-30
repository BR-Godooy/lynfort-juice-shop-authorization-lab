# Ambiente e linha do tempo

Revisão documental: 30/10/2026. Os dados abaixo derivam das capturas fornecidas por Victor Godoy e dos seus relatos de execução. O acesso à VM não foi realizado pela IA.

## Identificação observada

| Item | Valor | Base |
| --- | --- | --- |
| Aplicação | OWASP Juice Shop 20.3.0 | E-02 |
| Contêiner | `juice-shop`, ID curto `f43d7b8ea14e` | E-01 |
| Imagem configurada | `bkimminich/juice-shop` | E-01 |
| Image ID local | `sha256:2d370ec11ab40bc24b3516c042f7317a053c80f668e572538469d1fd7e91d7f8` | Transcrição visual de E-01 |
| RepoDigest local | `bkimminich/juice-shop@sha256:42843723a7bf4246aa5d63c324978b17ef296ac78434aeddd34c427bfb5853f4` | Transcrição visual de E-01 |
| Vínculo da porta | `127.0.0.1:3000 → 3000/tcp` | E-01 e E-10 |
| URL dentro do Kali | `http://127.0.0.1:3000` | Navegador do laboratório |
| Data/hora no Kali | `2026-10-29T13:48:43-03:00` | E-02 |
| Contextos | Firefox normal para A; privativo para B | Procedimento relatado e telas |
| Plataforma | Kali em VirtualBox no Windows, Docker | Capturas e relato do executor |

O Image ID identifica conteúdo local da imagem; RepoDigest identifica o manifesto registrado localmente. São objetos diferentes, portanto seus hashes não precisam coincidir. Ambos foram transcritos da captura, sem consulta ao registro remoto. `latest` não foi usado como número de versão.

E-02 mostra o deslocamento UTC `-03:00`, não o nome de um fuso. O planejamento e a revisão usam a data informada na sessão de trabalho. Capturas com somente horário não comprovam, sozinhas, a data de todos os eventos. Não foram registrados recursos da VM, versões de Docker/Firefox/VirtualBox, arquitetura da imagem, volumes ou configuração de persistência.

## Primeira execução — 29/10/2026 no acompanhamento

O contêiner aparecia inicialmente como `Exited (0)`. O executor iniciou o contêiner existente e abriu a loja no Kali. E-03/E-04 mostram duas contas fictícias com produtos diferentes. Os JSONs dos controles foram fornecidos como texto no histórico, sem captura bruta de rede.

| Conta | Janela | Carrinho | UserId | Produto | Criação do carrinho no JSON fornecido |
| --- | --- | --- | --- | --- | --- |
| A — `conta.a@lynfort.invalid` | Normal | 6 | 25 | Apple Juice (1000ml), id 1 | `2026-10-29T16:53:11.450Z` |
| B — `conta.b@lynfort.invalid` | Privativa | 7 | 26 | Green Smoothie, id 22 | `2026-10-29T16:55:27.063Z` |

O sufixo `Z` nesses campos indica UTC; o deslocamento exibido no terminal é outro formato de representação de horário. Os valores não foram ajustados nem usados para inferir a hora exata de cada consulta.

## AUT-03 — consulta cruzada A para B

O usuário informou que manteve a sessão A na janela normal e alterou apenas a consulta do carrinho 6 para 7. Informou retorno do produto id 22, Green Smoothie. A captura E-05 mostra GET com final 7, HTTP 200 e resposta success com data.id 7 e UserId 26. Products está recolhido; a captura não mostra o nome do produto nem identifica por si só a conta autenticada. Não foram recebidos ou armazenados tokens. Resultado sustentado pela captura e pelo relato, com essas limitações de proveniência.

E-06 complementa E-05: resposta expandida mostra data.id 7, UserId 26 e Products com id 22, name Green Smoothie. A identidade da sessão A continua baseada na confirmação do executor; a captura isolada não mostra autenticação.

## Execução 02 — após reinício
Usuário relatou carrinho vazio, falha de login com credenciais anteriores e novo cadastro/login de A. E-08 mostra GET final 6, HTTP 200, data.id 6, UserId 25 e Apple Juice. IDs coincidem com execução anterior, mas registros são de execução distinta. URL completa ainda não capturada nesta execução.

E-09: resposta do controle B na execução 02 mostra status success, data.id 7, UserId 26, produto id 22 Green Smoothie. Captura anterior da janela privativa mostrou GET final 7 com HTTP 200. Associação à conta B conforme execução relatada pelo usuário.

E-08 mostra criação do carrinho A em `2026-10-29T18:56:57.706Z`; E-09 mostra criação do carrinho B em `2026-10-29T19:00:54.050Z`. O servidor informou restauração do progresso dos desafios no navegador. Isso não garantiu preservação das credenciais; não foi investigado o mecanismo interno de persistência ou reinicialização. Os acessos cruzados da primeira execução não foram repetidos depois desse reinício.

## Resultado inverso na primeira execução

O anexo `01.png`, observado no histórico, mostrou janela privativa, GET com final 6, HTTP 200 e resposta com carrinho 6, UserId 25 e Apple Juice. A sessão B corresponde ao procedimento relatado; não há rastreio independente da credencial enviada. Em 30/10/2026 o executor confirmou não ter mais esse arquivo. Ele não foi reconstruído, não integra o pacote e não recebe hash. A referência reservada E-07 identifica a lacuna, não um arquivo existente.

## Encerramento informado em 30/10/2026
E-10 mostra sudo docker stop juice-shop retornando juice-shop e consulta posterior com Exited (255), binding 127.0.0.1:3000. Contêiner parado; causa do código de saída não determinada. Data de recebimento da evidência, não data/hora de execução comprovada na captura.
