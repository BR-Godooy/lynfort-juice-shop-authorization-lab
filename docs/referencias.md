# Referências e atribuições

Referências documentais consultadas na preparação do pacote em **30/10/2026**. Elas fundamentam conceitos e método; não substituem as capturas nem comprovam a revisão da versão instalada.

| Fonte | Uso neste projeto |
| --- | --- |
| [OWASP Juice Shop — projeto original](https://github.com/juice-shop/juice-shop) | Identificação da aplicação educacional; o código completo não é redistribuído neste pacote |
| [Guia oficial — Broken Access Control](https://help.owasp-juice.shop/part2/broken-access-control.html) | Contexto do desafio View Basket e do acesso horizontal entre contas |
| [WSTG v4.2 — WSTG-ATHZ-04](https://wstg.owasp.org/v4.2/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References/) | Método de comparar objetos conhecidos de contas distintas, evitando depender de adivinhação de IDs |
| [MITRE CWE-639](https://cwe.mitre.org/data/definitions/639.html) | Correspondência conceitual com acesso a registros de outro titular por chave controlada pelo cliente |

## Classificação e limites

O comportamento relatado é compatível com autorização horizontal indevida por referência de objeto, frequentemente descrita como IDOR. CWE-639 é usada como correspondência conceitual do comportamento observado, sem afirmar uma causa em código revisado.

Não foram atribuídos CVE, vetor CVSS, faixa de versões afetadas ou técnica MITRE ATT&CK. O laboratório local não demonstra exposição pública, exploração de uma aplicação de produção ou campanha adversária.

## Autoria dos materiais

- Execução e capturas do laboratório: Victor Godoy.
- Identidade visual Lynfort: reutilização do logo fornecido nos projetos do autor; capa e fluxo produzidos para este portfólio.
- Apoio de IA: orientação, edição e revisão da documentação. A IA não executou requisições dentro da VM.
- Interfaces, nomes e imagens dos produtos presentes nas capturas pertencem ao Juice Shop e a seus respectivos titulares; aparecem como registro do ambiente educacional. Não há alegação de vínculo ou endosso da OWASP à Lynfort.

Nenhuma licença adicional é presumida sobre marcas ou conteúdo de terceiros. O código da aplicação permanece no projeto original, com seus termos próprios. A versão 20.3.0 é identificada pela saída local de E-02, não deduzida da edição atual do guia.
