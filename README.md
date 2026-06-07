# Claude Code Skills

Skills que fui criando conforme fui usando o Claude Code e encontrando situações que achei que valiam guardar.

## Como usar

Copie o arquivo `.md` da pasta `commands/` para o diretório de comandos do Claude Code:

**Windows:**
```
C:\Users\<seu-usuario>\.claude\commands\
```

**Mac / Linux:**
```
~/.claude/commands/
```

Depois é só chamar com `/nome-da-skill` em qualquer sessão do Claude Code.

---

## Skills disponíveis

### `/powershell-utf8-fix`
**Arquivo:** `commands/powershell-utf8-fix.md`

Quando eu fui inserir dados com acentuação numa API via PowerShell, os caracteres chegavam quebrados no sistema — `á` virava `?`, `ç` virava `°`. Gastei um tempo até entender o que estava acontecendo. Essa skill diagnostica o problema e aplica a correção certa.

---

## Contribuindo

Tem uma skill que resolveu um problema chato pra você? Abre um PR com o `.md` na pasta `commands/`.
