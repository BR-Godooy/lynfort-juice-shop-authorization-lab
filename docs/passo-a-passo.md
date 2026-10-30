# Procedimento e reprodução orientada

Este documento registra a execução manual e oferece uma sequência para futura reprodução no próprio laboratório. Não é um script automatizado nem um registro bruto de rede. Nenhum comando deste documento foi executado pela IA dentro da VM.

## 1. Identificar o contêiner existente

No Kali, foram consultados o estado, a imagem, o identificador de conteúdo e o vínculo da porta. As consultas abaixo leem metadados locais, sem baixar imagem nova:

```bash
sudo docker ps -a --filter name=juice-shop
sudo docker inspect juice-shop --format 'Imagem={{.Config.Image}} | ID={{.Image}} | Portas={{json .HostConfig.PortBindings}}'
sudo docker image inspect bkimminich/juice-shop --format '{{json .RepoDigests}}'
```

A captura E-01 registra `127.0.0.1:3000`. Uma tag pode mudar de conteúdo; o digest observado deve acompanhar uma reprodução. Este roteiro usa o contêiner existente, sem instruir recriação ou atualização.

## 2. Iniciar e registrar a versão

O início do contêiner foi seguido da abertura da loja no Firefox dentro do Kali:

```bash
sudo docker start juice-shop
```

Endereço local: `http://127.0.0.1:3000`.

A primeira tentativa de executar `node` pelo nome falhou por ausência no PATH. O caminho completo abaixo funcionou no contêiner observado e mostrou a versão 20.3.0:

```bash
sudo docker exec juice-shop /nodejs/bin/node -p "require('./package.json').version"
date --iso-8601=seconds
```

E-02 registra versão e horário com deslocamento UTC. Caminhos internos podem variar entre imagens; não inferir a versão de uma imagem pela documentação de outra.

## 3. Preparar duas contas e seus controles

Na janela normal, foi cadastrada a conta fictícia A e adicionado um Apple Juice. Na janela privativa, foi cadastrada B com um Green Smoothie. As senhas fictícias não integram a documentação.

As telas E-03 e E-04 relacionam cada e-mail fictício ao produto escolhido. Na aba Network do Firefox, a consulta real foi identificada após abrir o carrinho. As URLs fornecidas pelo executor na primeira execução foram:

- A: `http://127.0.0.1:3000/rest/basket/6`.
- B: `http://127.0.0.1:3000/rest/basket/7`.

Esses números são observações daquela instância. Em outra execução é necessário identificá-los pelos próprios controles; não percorrer intervalos de IDs.

## 4. Comparar a leitura entre contas

Na janela A, a consulta legítima do carrinho foi aberta em Edit and Resend. O procedimento relatado manteve GET e a autenticação existente, alterando apenas o identificador para o carrinho conhecido de B. O retorno documentado em E-05/E-06 contém carrinho 7, UserId 26 e Green Smoothie.

Na janela privativa B, foi acompanhado o procedimento equivalente para o carrinho conhecido de A. O anexo histórico mostrou carrinho 6, UserId 25 e Apple Juice. Seu arquivo não está disponível no pacote e esse sentido tem suporte documental menor.

Para uma reprodução futura, manter explícita a identidade da sessão de origem e os objetos de cada conta. Registrar o status e o corpo da resposta sem exportar segredos. Uma captura pode omitir a autenticação por privacidade; isso precisa ser descrito como limite de verificação, não convertido em prova visual da credencial.

## 5. Interpretar o resultado

Comparar o acesso cruzado ao controle legítimo do titular. O conteúdo do catálogo é público, mas a associação retornada de produto, carrinho e UserId permite avaliar a leitura de dados do carrinho. O significado do retorno depende também da sessão usada; HTTP 200 não resolve essa identidade.

O comportamento esperado de uma aplicação corrigida seria negar o acesso cruzado sem revelar o objeto de outro titular. Esse resultado é critério futuro, não saída observada neste laboratório.

## 6. Retomada após reinício

A loja informou restauração de progresso, porém as credenciais anteriores falharam. O executor cadastrou novamente as contas. E-08/E-09 registram os controles dessa nova execução, com datas de criação diferentes e mesmos números de carrinho. Nenhuma consulta cruzada foi repetida nessa execução. A causa da perda de estado não foi investigada.

## 7. Encerrar

Os comandos de encerramento param o aplicativo e consultam seu estado, preservando a configuração do contêiner:

```bash
sudo docker stop juice-shop
sudo docker ps -a --filter name=juice-shop
```

E-10 mostra `Exited (255)`. Isso confirma o estado parado no momento da captura; não estabelece a causa do código nem garante persistência dos dados após outro início.
