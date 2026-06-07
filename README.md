# Claude Code Skills

Uma coleção de skills (slash commands) para o [Claude Code](https://claude.ai/code) criadas a partir de problemas reais.

## Como usar uma skill

Copie o arquivo `.md` da pasta `commands/` para o diretório global de comandos do Claude Code:

**Windows:**
```
C:\Users\<seu-usuario>\.claude\commands\
```

**Mac / Linux:**
```
~/.claude/commands/
```

Depois é só invocar em qualquer sessão do Claude Code com `/nome-da-skill` (o nome do arquivo sem o `.md`).

---

## Skills disponíveis

### `/powershell-utf8-fix`
**Arquivo:** `commands/powershell-utf8-fix.md`

Diagnostica e corrige o problema de corrupção de encoding UTF-8 em scripts PowerShell 5.1 que enviam JSON para APIs REST.

Use quando caracteres acentuados (á, é, ã, ú, ç) chegam como `?` ou `°` no sistema de destino (CRM, API, webhook, banco de dados). Cobre o diagnóstico, a correção em duas linhas, como atualizar scripts existentes e a solução permanente (atualizar para o PowerShell 7).

---

## Contribuindo

Tem uma skill que vale compartilhar? Abra um PR com seu arquivo `.md` na pasta `commands/`.
