# Correção de Encoding UTF-8 no PowerShell para Requisições de API

Eu estava criando uma integração com um CRM via API usando PowerShell e, toda vez que eu enviava algum texto com acentuação — como nome de campo com "ação" ou "configuração" — o caractere chegava quebrado no sistema. Um `á` virava `?`, um `ç` virava `°`. Parecia problema na API, no CRM, em tudo. Mas o problema estava no próprio PowerShell.

Criei essa skill para não ter que passar por isso de novo — e para quem cair no mesmo problema conseguir resolver rápido.

---

## Quando usar

Use `/powershell-utf8-fix` quando:
- Caracteres acentuados (á, é, ã, ú, ç) aparecem como `?`, `°` ou algum símbolo estranho no sistema que está recebendo os dados
- Você está no Windows com PowerShell 5.1 fazendo POST ou PUT para alguma API
- Qualquer `Invoke-RestMethod` ou `Invoke-WebRequest` com `-Body` contendo texto acentuado

## Por que isso acontece

O PowerShell 5.1 — que é o padrão no Windows — usa o encoding Windows-1252 ao montar o corpo da requisição. A maioria das APIs espera UTF-8. Quando os bytes chegam com encoding errado, os caracteres especiais ficam corrompidos. O problema está no PowerShell, não na API.

Para confirmar a versão que você está usando:

```powershell
$PSVersionTable.PSVersion
```

- **Major = 5** → é o 5.1, tem o problema. Aplique a correção abaixo.
- **Major = 7+** → UTF-8 já é padrão. Não precisa fazer nada.

## A correção

**Do jeito que estava (quebrado):**
```powershell
$json = @{ name = "Campo com Ação" } | ConvertTo-Json
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $json
```

**Do jeito correto:**
```powershell
$json  = @{ name = "Campo com Ação" } | ConvertTo-Json
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $bytes
```

Só isso. Em vez de passar `$json` direto no `-Body`, converte para bytes UTF-8 primeiro. Aplica em todo POST e PUT que envia texto com acentuação.

## Se você já tem scripts prontos

Procura no script por `-Body $json` ou `-Body $payload` e substitui pela versão de duas linhas:

```powershell
# Onde tiver isso:
-Body $json

# Troca por:
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
# ...e usa -Body $bytes
```

## Solução definitiva: atualizar para o PowerShell 7

Se quiser resolver de vez, o PowerShell 7 já usa UTF-8 por padrão e é gratuito:

```powershell
winget install --id Microsoft.PowerShell --source winget
```

Ele instala junto com o 5.1, não substitui. Depois é só usar `pwsh` no lugar de `powershell`. Sem workaround, sem problema.

## Isso vale para qualquer API

Não é específico do CRM que eu estava usando. O mesmo problema acontece com qualquer sistema que receba JSON via PowerShell 5.1 — HubSpot, ClickUp, ActiveCampaign, webhooks, banco de dados via HTTP. Se o texto tem acento e o destino recebe lixo, provavelmente é isso.
