# PRD — Copiloto Real para OpenClaw

Sistema completo, dinâmico e replicável para implementar um copiloto pessoal avançado em qualquer OpenClaw.

---

## 📋 O que está incluído

| Seção | Conteúdo |
|---|---|
| 1 | Overview e métricas de sucesso |
| 2 | Arquitetura do segundo cérebro (estrutura memory/) |
| 3 | Os 8 pilares do copiloto real |
| 4 | 3 automações prontas para implementar |
| 5 | Otimizações de custo e velocidade (40-60% economia) |
| 6 | Diretriz operacional e autonomia |
| 7 | Playbooks criados |
| 8 | Arquivos e estrutura mínima |
| 9 | Checklist de implementação em 4 fases |
| 10 | Exemplos práticos de uso |
| 11 | Configuração do runtime (openclaw.json) |
| 12 | HEARTBEAT — checklist de saúde automático |
| 13 | Git Sync automático (backup diário) |
| 14 | Instalação de skills externas via Git |
| 15 | Score de automação (como medir e aumentar) |
| 16 | Próximos passos após implementação |

---

## 🚀 Como usar

### 1. Personalizar os arquivos base
```
SOUL.md       → Quem o copiloto é, tom, valores
USER.md       → Quem o usuário é, preferências, contexto
IDENTITY.md   → Nome, emoji, avatar
AGENTS.md     → Diretivas operacionais, arquitetura, segurança
MEMORY.md     → Índice principal da memória
HEARTBEAT.md  → Checklist de saúde automático
```

### 2. Criar estrutura de memória
```
memory/priorities.md
memory/decisions.md
memory/lessons.md
memory/errors-to-avoid.md
memory/pending.md
memory/automation-candidates.md
memory/automation-rules.md
memory/optimization-rules.md
memory/projects.md
memory/language.md
```

### 3. Configurar runtime (openclaw.json)
```json
{
  "env": { "NOME_DA_CHAVE": "valor" },
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-6",
        "fallbacks": ["google/gemini-3.1-pro-preview", "anthropic/claude-haiku-4-5"]
      }
    }
  }
}
```

### 4. Criar as 3 automações principais
```bash
# Pending review a cada 2h
openclaw cron create --name pending-review-2h --cron "0 */2 * * *" --message "check pending and alert if needed"

# Consolidação semanal de lessons
openclaw cron create --name learnings-weekly --cron "0 6 * * 1" --message "consolidate lessons into errors-to-avoid"

# Git backup diário
openclaw cron create --name git-backup-daily --cron "0 4 * * *" --message "run git backup script"
```

### 5. Testar e validar
- Rodar 5-10 sessões de teste
- Registrar feedback em `memory/YYYY-MM-DD.md`
- Consolidar em `memory/lessons.md`
- Meta: score de automação 7.0+/10

---

## 🎯 Resultado esperado

- ✅ Copiloto que lembra, organiza, prioriza, sugere, automatiza, monitora e aprende
- ✅ Score de automação 7.0+/10
- ✅ Economia de 40-60% em tokens por sessão
- ✅ Sistema autorrecuperável (fallback chain)
- ✅ Backup automático diário

---

## 📄 Documentação completa

[PRD-copiloto-real-dinamico.md](./PRD-copiloto-real-dinamico.md)

---

## 🔗 Recursos

- Docs OpenClaw: https://docs.openclaw.ai
- Comunidade: https://discord.com/invite/clawd
