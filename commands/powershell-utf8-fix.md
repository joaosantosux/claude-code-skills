# Correção de Encoding UTF-8 no PowerShell para Requisições de API

## Sobre esta skill

**O que faz:** Diagnostica e corrige a corrupção de encoding UTF-8 em scripts PowerShell 5.1 que enviam JSON para APIs REST — o problema onde caracteres acentuados (á, é, ã, ú, ç) chegam como `?` ou `°` no sistema de destino.

**Para quem é:** Qualquer pessoa no Windows usando PowerShell 5.1 para integrar com APIs REST, CRMs, webhooks ou qualquer endpoint HTTP que espera JSON em UTF-8.

**Por que foi criada:** Descoberta durante a construção de uma integração com o CRM GoHighLevel — nomes de campos personalizados e dados de contato com caracteres acentuados chegavam corrompidos. Horas de investigação apontaram para uma única causa raiz: o encoding padrão do PowerShell 5.1 é Windows-1252, não UTF-8. A correção é uma mudança de duas linhas, mas o problema é invisível e nada óbvio. Esta skill existe para que ninguém precise redescobri-lo do zero.

**Benefício:** Em vez de gastar horas tentando entender por que os dados parecem corretos localmente mas chegam quebrados na API, execute `/powershell-utf8-fix`, obtenha um diagnóstico imediato e aplique a correção certa em minutos.

---

## Quando usar esta skill

Invoque com `/powershell-utf8-fix` quando:
- Caracteres como á, é, ã, ú, ç aparecem como `?`, `°` ou outros símbolos estranhos no sistema de destino (CRM, API, banco de dados, webhook)
- Você está rodando **PowerShell 5.1** (padrão no Windows) e fazendo requisições POST ou PUT com corpo JSON
- Qualquer chamada `Invoke-RestMethod` ou `Invoke-WebRequest` que envia um `-Body` com caracteres acentuados

## Diagnóstico

Primeiro, verifique a versão do PowerShell:

```powershell
$PSVersionTable.PSVersion
```

- **Major = 5** → você tem o problema. Aplique a correção abaixo.
- **Major = 7+** → UTF-8 já é o padrão. Nenhuma correção necessária.

## Causa raiz

O PowerShell 5.1 usa o encoding Windows-1252 por padrão ao construir corpos de requisições HTTP. APIs modernas esperam UTF-8. A incompatibilidade de bytes faz com que os caracteres acentuados cheguem corrompidos no sistema receptor.

## A correção

**Antes (quebrado):**
```powershell
$json = @{ name = "Campo com Ação" } | ConvertTo-Json
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $json
```

**Depois (correto):**
```powershell
$json  = @{ name = "Campo com Ação" } | ConvertTo-Json
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $bytes
```

A única mudança: converter `$json` para um array de bytes UTF-8 antes de passá-lo como `-Body`. Aplique isso em **toda** requisição POST e PUT que inclua texto com caracteres acentuados.

## Como aplicar em scripts existentes

Procure no script por todo padrão `-Body $json` ou `-Body $payload` e substitua pela versão de duas linhas:

```powershell
# Substitua este padrão onde ele aparecer:
-Body $json

# Por isto:
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
# ...depois use -Body $bytes
```

## Solução permanente: atualizar para o PowerShell 7

O PowerShell 7+ usa UTF-8 por padrão e é gratuito. Instale via:

```powershell
winget install --id Microsoft.PowerShell --source winget
```

Ele instala **lado a lado** com o 5.1 — não o substitui. Após instalar, use `pwsh` em vez de `powershell` para iniciá-lo. Nenhum workaround necessário na versão 7+.

## Esta correção se aplica a qualquer sistema

Não é específico para GoHighLevel ou qualquer API em particular. A mesma correção se aplica sempre que o PowerShell 5.1 envia JSON para:
- Qualquer API REST (HubSpot, ClickUp, ActiveCampaign, Asana, etc.)
- Webhooks
- Bancos de dados via HTTP
- Criação de arquivos via API

O problema está no PowerShell, não no sistema receptor.
