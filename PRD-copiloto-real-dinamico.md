# PRD — Implementação de Copiloto Real com Automações
## Sistema de Inteligência, Memória Curada e Otimizações para OpenClaw

**Versão:** 2.0  
**Data:** Abril de 2026  
**Escopo:** Replicação completa e dinâmica em qualquer servidor OpenClaw  
**Público:** Qualquer gateway OpenClaw (novo deployment ou upgrade)

---

## 1. OVERVIEW EXECUTIVO

Este PRD documenta todas as melhorias de inteligência, automação e operação de um copiloto pessoal avançado. O sistema é totalmente replicável em qualquer ecossistema OpenClaw, independente do contexto ou usuário.

### Objetivos
- ✅ Transformar assistente reativo em copiloto real
- ✅ Implementar automações de baixo ruído com alert inteligente
- ✅ Consolidar memória curada como fonte de verdade
- ✅ Otimizar custo de tokens sem perder inteligência
- ✅ Criar comportamento 100% previsível e autorrecuperável

### Métricas de Sucesso
- Score de automação: de 2.0/10 → 7.0+/10
- Economia de tokens: 40-60% por sessão via cache + context reduction
- Taxa de acerto em lições: 100% (feedback vira regra)
- Tempo de resposta: 2-3x mais rápido via memory_search vs full context

---

## 2. ARQUITETURA DO SEGUNDO CÉREBRO

### 2.1 Estrutura de Memória Curada

```
memory/
├── index.md                          # Índice principal (leitura obrigatória)
├── YYYY-MM-DD.md                     # Nota diária (scratch, consolidada semanalmente)
├── PRIORITY FILES (leitura sob demanda):
│   ├── priorities.md                 # Foco + filtro de decisão
│   ├── decisions.md                  # Decisões permanentes do usuário
│   ├── lessons.md                    # Feedback consolidado em regra
│   ├── errors-to-avoid.md            # Anti-patterns derivados de lessons
│   ├── people.md                     # Contexto de pessoas importantes
│   ├── pending.md                    # Tarefas aguardando input/aprovação
│   ├── automation-candidates.md      # Repetições prontas pra automação
│   ├── automation-rules.md           # Limites e autonomia
│   ├── optimization-rules.md         # Cache, context reduction, cost
│   ├── projects.md                   # Projetos ativos e status
│   └── language.md                   # Tom, estilo, essência de comunicação
└── MOC & REFERENCE:
    ├── mocs/library/                 # Base autoral (leia uma vez)
    ├── mocs/method/                  # Metodologia do usuário
    └── concepts/                     # Tópicos diversos
```

### 2.2 Regra de Carregamento

**Carregar UMA VEZ por sessão (cache):**
- SOUL.md
- USER.md
- IDENTITY.md
- AGENTS.md

**Consultar sob demanda via memory_search():**
- memory/priorities.md
- memory/pending.md
- memory/lessons.md
- memory/errors-to-avoid.md
- Tudo mais por busca semântica

**NUNCA recarregar automaticamente:**
- Arquivos-base (a menos que usuário diga "recarrega")
- Histórico completo de sessões antigas (>7 dias)
- Contexto não citado há +7 dias

---

## 3. OS 8 PILARES DO COPILOTO REAL

### 3.1 Lembrar
**O que:** Consultar memória crítica antes de agir  
**Como:** memory_search(query) + memory_get(path, lines)  
**Quando:** Sempre que a pergunta toca um tópico já consolidado

### 3.2 Organizar
**O que:** Separar memória útil de ruído  
**Como:** Consolidar notas diárias em topic files semanalmente  
**Quando:** Toda segunda-feira ou ao atingir 5+ itens não consolidados

### 3.3 Priorizar
**O que:** Usar memory/priorities.md como filtro de decisão  
**Como:** Consultar antes de sugerir ações; validar alinhamento  
**Quando:** Em cada resposta relevante

### 3.4 Sugerir
**O que:** Propor com base em histórico, prioridades e pendências  
**Como:** Juntar contexto de memória curada + momento + roadmap  
**Quando:** Só quando a complexidade da pergunta justificar

### 3.5 Automatizar
**O que:** Registrar repetição em memory/automation-candidates.md  
**Como:** Detectar padrão → propor automação → implementar se dentro de autonomia  
**Quando:** Ao identificar 3+ repetições da mesma ação

### 3.6 Monitorar
**O que:** Heartbeat + pendências + crons operacional  
**Como:** Verificar saúde a cada 2 horas via cron; pull de memory/pending.md  
**Quando:** Automático via heartbeat.md checklist

### 3.7 Aprender
**O que:** Transformar feedback em regra durável  
**Como:** Feedback explícito → memory/lessons.md → consolida em memory/errors-to-avoid.md  
**Quando:** Sempre que o usuário corrigir ou indicar preferência

### 3.8 Acompanhar como Copiloto Real
**O que:** Ser proativo, antecipar, organizar, sugerir, automatizar  
**Como:** Todos os 7 pilares acima trabalhando em sinergia  
**Quando:** Sempre, em toda sessão

---

## 4. AUTOMAÇÕES IMPLEMENTADAS

### 4.1 Automação #1: Skill Padrão para Tarefa Recorrente

**Status:** ✅ IMPLEMENTADO  
**Descrição:** Uma skill (ex: geração de imagens, web search, etc) é configurada como rota padrão  
**Trigger:** Usuário pede a ação recorrente  
**Comportamento:**
- Usar a skill configurada
- Enviar resultado por padrão
- Sem overhead, a menos que pedido explícito

**Como implementar:**
1. Escolher a skill desejada
2. Documentar em SKILL.md o comportamento padrão
3. Configurar chave de API em ~/.openclaw/openclaw.json se necessário

---

### 4.2 Automação #2: Follow-ups e Pendências com Revisão Silenciosa

**Status:** ✅ PLAYBOOK PRONTO  
**Descrição:** Rotina que revisa memory/pending.md a cada 2 horas e emite alerta só quando há atenção real  
**Trigger:** Cron: 0 */2 * * * (a cada 2 horas)  
**Comportamento:**
- Ler memory/pending.md
- Classificar por urgência (🔴 bloqueante, 🟠 importante, 🟡 normal, 🟢 backlog)
- Se houver 🔴 ou 🟠 → alerta resumido
- Se tudo ok → responder PENDING_OK
- Atualizar timestamps

**Arquivo:**
- playbooks/copilot-pending-review.md

**Implementação:**
```
openclaw cron create \
  --name pending-review-2h \
  --cron "0 */2 * * *" \
  --message "check pending and alert if needed"
```

---

### 4.3 Automação #3: Consolidação Automática de Feedbacks em Erros Evitáveis

**Status:** ✅ PLAYBOOK PRONTO  
**Descrição:** Rotina que consolida memory/lessons.md em memory/errors-to-avoid.md semanalmente  
**Trigger:** Cron: 0 6 * * 1 (segunda-feira 6h UTC)  
**Comportamento:**
- Ler memory/lessons.md
- Identificar padrões repetidos (2+ vezes)
- Consolidar em memory/errors-to-avoid.md com origem + ação
- Marcar itens consolidados com ✅ em lessons
- Log em logs/copilot-learnings-consolidation.log

**Arquivos:**
- playbooks/copilot-consolidate-learnings.md
- memory/errors-to-avoid.md (consolidação destino)

**Implementação:**
```
openclaw cron create \
  --name learnings-consolidation-weekly \
  --cron "0 6 * * 1" \
  --message "consolidate lessons into errors-to-avoid"
```

---

## 5. OTIMIZAÇÕES DE CUSTO E VELOCIDADE

### 5.1 Cache de Prompts Persistente

**O que:** Reutilizar SOUL.md, USER.md, IDENTITY.md, AGENTS.md entre sessões  
**Por quê:** Esses arquivos não mudam frequentemente; economiza ~50 KB de tokens  
**Implementação:**
1. Carregar uma só vez na primeira mensagem da sessão
2. Usar cache local do sistema
3. Nunca recarregar automaticamente

**Economia:** ~50% economia de tokens no contexto base

---

### 5.2 Redução de Contexto Histórico Automático

**O que:** Em vez de carregar histórico completo, usar memória curada via memory_search()  
**Regra:**
- Histórico da sessão: apenas últimas 10-20 mensagens
- Memória curada: busca relevante via memory_search() sob demanda
- Contexto histórico distante: só se usuário mencionar explicitamente
- Sessões com +7 dias: ignorar; puxar só se pedido

**Economia:** 40-60% economia de tokens por sessão, velocidade 2-3x maior

---

### 5.3 Regras de Economia Operacional

**Pergunta simples → resposta simples:**
- Não reabrir memória fixa sem necessidade real
- Puxar contexto do momento só quando muda a resposta

**Para assuntos recorrentes:**
- Reutilizar padrões de resposta

**Rate limits:**
- Mínimo 5s entre chamadas de API consecutivas
- Mínimo 10s entre web searches
- Máximo 5 buscas por lote, depois pausa de 2min
- Se 429: PARAR, esperar 5min, tentar de novo

---

## 6. DIRETRIZ OPERACIONAL DO COPILOTO

### 6.1 Loop Operacional

1. **Antes de entregar** (relevante):
   - Consultar memory/decisions.md, memory/lessons.md, memory/language.md, memory/pending.md, memory/priorities.md
   
2. **Durante a ação:**
   - Lembrar → Organizar → Priorizar → Sugerir → Automatizar → Monitorar → Aprender
   
3. **Ao receber feedback:**
   - Transformar em regra durável em memory/errors-to-avoid.md ou memory/lessons.md
   
4. **Ao detectar repetição:**
   - Registrar em memory/automation-candidates.md
   - Se dentro de autonomia + reversível, implementar
   - Avisar usuário com resumo claro

### 6.2 Autonomia Permitida para Automações

O copiloto pode automatizar **sozinho** quando:
- ✅ Interna ao workspace
- ✅ Interna à memória
- ✅ Interna à organização operacional
- ✅ Cron silenciosa (sem impacto externo)
- ✅ Monitoramento de baixo risco
- ✅ Ajuste que não afete negativamente sistema, canais, dados ou operação crítica

O copiloto **sempre pergunta** quando:
- ❌ Altera config do sistema
- ❌ Acessa APIs externas de forma nova
- ❌ Remove ou modifica dados sensíveis
- ❌ Afeta canais de comunicação
- ❌ Ação é destrutiva ou de alto risco

### 6.3 Tons & Estilo de Comunicação

**Em SOUL.md / IDENTITY.md, documentar:**
- Tom desejado (ex: descontraído, forte, elegante, humano, estratégico)
- Anti-patterns (o que NUNCA fazer)
- Força de resposta (brevidade vs profundidade conforme contexto)
- Pessoa(s) a quem falar e contexto específico

---

## 7. PLAYBOOKS CRIADOS

### 7.1 Playbook: Pending Review (Silenciosa)
**Arquivo:** playbooks/copilot-pending-review.md  
**Objetivo:** Revisar memory/pending.md automaticamente e alertar só quando necessário  
**Frequência:** A cada 2 horas (cron 0 */2 * * *)

### 7.2 Playbook: Consolidate Learnings (Silenciosa)
**Arquivo:** playbooks/copilot-consolidate-learnings.md  
**Objetivo:** Consolidar memory/lessons.md em memory/errors-to-avoid.md semanalmente  
**Frequência:** Segunda-feira 6h UTC (cron 0 6 * * 1)

---

## 8. ARQUIVOS & ESTRUTURA

### 8.1 Arquivos a Criar (Estrutura Mínima)

```
memory/
├── index.md                          # Índice curado
├── priorities.md                     # Prioridades + diretriz operacional
├── decisions.md                      # Decisões permanentes
├── lessons.md                        # Feedback consolidado
├── errors-to-avoid.md                # Anti-patterns derivados
├── people.md                         # Contexto de pessoas
├── pending.md                        # Tarefas aguardando
├── automation-candidates.md          # Candidatos a automação
├── automation-rules.md               # Limites de autonomia
├── optimization-rules.md             # Cache + context reduction
├── projects.md                       # Projetos ativos
└── language.md                       # Tom + estilo

playbooks/
├── copilot-pending-review.md         # Pending review silenciosa
└── copilot-consolidate-learnings.md  # Consolidação de learnings

logs/
├── copilot-pending-review.log        # Log de pending reviews
└── copilot-learnings-consolidation.log # Log de consolidações
```

### 8.2 Arquivos Obrigatórios (Raiz do Workspace)

```
SOUL.md          # Quem o copiloto é, seu tom, seus valores
USER.md          # Quem o usuário é, preferências, contexto
IDENTITY.md      # Nome, emoji, avatar, background
AGENTS.md        # Diretriz de agentes, arquitetura, segurança
MEMORY.md        # Índice principal (nunca editar, só referenciar)
HEARTBEAT.md     # Checklist automático de saúde
```

---

## 9. CHECKLIST DE IMPLEMENTAÇÃO

### Fase 1: Estrutura & Memória
- [ ] Criar estrutura de memory/ com 12 arquivos
- [ ] Criar playbooks/ com 2 playbooks
- [ ] Criar logs/ com permissões corretas
- [ ] Criar SOUL.md, USER.md, IDENTITY.md (PERSONALIZADOS)
- [ ] Criar AGENTS.md com diretivas do copiloto
- [ ] Criar MEMORY.md com índice

### Fase 2: Automações
- [ ] Escolher skill desejada (opcional)
- [ ] Configurar chave de API em ~/.openclaw/openclaw.json se necessário
- [ ] Criar cron pending-review-2h via openclaw cron create
- [ ] Criar cron learnings-consolidation-weekly via openclaw cron create
- [ ] Testar crons (verificar logs/ após 2h e 1 semana)

### Fase 3: Otimizações
- [ ] Documentar em memory/optimization-rules.md o comportamento de cache
- [ ] Testar context reduction (rodar sessão, verificar economia de tokens)
- [ ] Validar memory_search() vs full context (performance)

### Fase 4: Validação
- [ ] Fazer 5-10 sessões de teste
- [ ] Registrar feedback em memory/YYYY-MM-DD.md
- [ ] Consolidar lessons em memory/lessons.md
- [ ] Rodar pending review manual (PENDING_OK esperado)
- [ ] Verificar Git commits (se using Git sync)

---

## 10. EXEMPLOS DE USO

### Exemplo 1: Loop de Lembrar + Priorizar + Sugerir

```
Usuário: "O que fazer com a frente X?"

1. LEMBRAR:
   memory_search("frente X, prioridades, projetos")
   memory_get("memory/priorities.md", 1-10)

2. PRIORIZAR:
   Frente X está em memory/priorities? É bloqueante?

3. SUGERIR:
   "A frente X está como [prioridade]. Com base em [contexto], 
   recomendo [opção 1], [opção 2], [opção 3]."
```

### Exemplo 2: Loop de Aprender + Automatizar

```
Usuário: "Daqui em diante, sempre faça X de forma Y"

1. APRENDER:
   - Registra em memory/lessons.md: "Preferência de Y para X"
   - Consolida em memory/errors-to-avoid.md (se padrão)
   - Registra em memory/automation-candidates.md (se repetido)

2. AUTOMATIZAR (se autonomia permitir):
   - Cria cron/playbook para X com formato Y
   - Implementa
   - Avisa: "Pronto. A partir de agora, toda vez que [trigger], 
     eu [ação] em [formato]."
```


---

## 11. CONFIGURAÇÃO DO RUNTIME (openclaw.json)

### 11.1 Variáveis de Ambiente e API Keys

Adicionar chaves de API diretamente no runtime do OpenClaw:

```json
{
  "env": {
    "GEMINI_API_KEY": "sua_chave_aqui",
    "DEEPGRAM_API_KEY": "sua_chave_aqui",
    "APIFY_TOKEN": "seu_token_aqui"
  }
}
```

**Como aplicar:**
```python
import json
from pathlib import Path
p = Path.home() / '.openclaw/openclaw.json'
obj = json.loads(p.read_text())
obj.setdefault('env', {})['NOME_DA_CHAVE'] = 'valor'
p.write_text(json.dumps(obj, indent=2))
```

### 11.2 Configuração de Modelo Principal e Fallback Chain

Garantir que o sistema nunca caia por rate limit:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-6",
        "fallbacks": [
          "google/gemini-3.1-pro-preview",
          "anthropic/claude-opus-4-6",
          "anthropic/claude-haiku-4-5",
          "ollama/qwen2.5:7b"
        ]
      }
    }
  }
}
```

**Lógica:** Se o modelo principal atingir rate limit → tenta próximo da lista automaticamente.

**Recomendação:**
- Principal: Claude Sonnet ou Gemini Pro
- Fallbacks: Opus (poderoso), Haiku (rápido/barato), Ollama local (último recurso)
- Nunca confiar em só um modelo

---

## 12. HEARTBEAT — CHECKLIST DE SAÚDE AUTOMÁTICO

### 12.1 Estrutura do HEARTBEAT.md

```markdown
# HEARTBEAT.md

## Checklist (a cada heartbeat)
- [ ] Compromissos próximos (24-48h)
- [ ] Tarefas pendentes em memory/pending.md
- [ ] Ler logs/copilot-alerts quando existir
- [ ] Prioridades continuam corretas em memory/priorities.md
- [ ] Há algo repetido para memory/automation-candidates.md
- [ ] Crons saudáveis

## Semanal (segunda-feira)
- [ ] Revisar projetos ativos
- [ ] Consolidar notas diárias em topic files
- [ ] Atualizar MEMORY.md
- [ ] Revisar memory/automation-candidates.md
- [ ] Revisar memory/errors-to-avoid.md

## Regras
- Se tudo ok → responder HEARTBEAT_OK
- Se houver alerta → responder só com o alerta útil
- Nunca alertar durante criação, gravação ou reuniões estratégicas
```

### 12.2 Configurar Cron de Heartbeat

```bash
openclaw cron create \
  --name heartbeat-2h \
  --every 2h \
  --message "heartbeat check" \
  --light-context
```

---

## 13. GIT SYNC AUTOMÁTICO

### 13.1 Configurar Repositório de Backup

```bash
cd /caminho/do/workspace
git init
git remote add origin https://github.com/SEU_USUARIO/SEU_REPO.git
git add .
git commit -m "Initial backup"
git push -u origin main
```

### 13.2 Script de Sync Diário

Criar em `scripts/git-sync.sh`:

```bash
#!/bin/bash
REPO="/caminho/do/workspace"
cd "$REPO"
git add -A
git commit -m "backup: $(date -u '+%Y-%m-%d %H:%M:%S UTC')" --allow-empty
git push origin main
```

### 13.3 Cron de Backup Diário

```bash
openclaw cron create \
  --name git-backup-daily \
  --cron "0 4 * * *" \
  --message "run git backup script"
```

---

## 14. INSTALAÇÃO DE SKILLS EXTERNAS

### 14.1 Instalar Skill via Git

```bash
# 1. Clonar repositório da skill
git clone https://github.com/USUARIO/nome-da-skill.git /tmp/skill-install

# 2. Copiar SKILL.md para o workspace
mkdir -p /caminho/workspace/skills/nome-da-skill
cp /tmp/skill-install/skill/nome-da-skill/SKILL.md /caminho/workspace/skills/nome-da-skill/

# 3. Copiar scripts (se existirem)
cp /tmp/skill-install/skill/nome-da-skill/scripts/*.py /caminho/workspace/scripts/

# 4. Configurar API key no openclaw.json (seção 11.1)
```

### 14.2 Estrutura de Skill Mínima

```yaml
---
name: nome-da-skill
description: "Quando usar esta skill. Triggers: [palavra-chave]"
metadata:
  openclaw:
    emoji: "🔧"
    requires:
      config:
        - env.NOME_DA_API_KEY
---

# Nome da Skill

## Workflow
1. Identificar pedido do usuário
2. Executar via script
3. Retornar resultado no chat por padrão

## Comandos
python3 scripts/nome-da-skill.py --prompt "..."
```

---

## 15. SCORE DE AUTOMAÇÃO

### 15.1 Como Aumentar o Score

| Ação | Impacto estimado |
|---|---|
| Cron de heartbeat | +1.0 |
| Cron de pending review | +1.0 |
| Cron de git backup | +0.5 |
| Learnings consolidation | +1.0 |
| Playbooks com trigger claro | +0.5/each |
| Registrar automation-candidates | +0.5 |
| Fallback chain configurado | +0.5 |

**Meta recomendada:** 7.0+/10

### 15.2 Verificar Score

```bash
openclaw status
```

---

## 16. PRÓXIMOS PASSOS APÓS IMPLEMENTAÇÃO

1. **Semana 1:** Consolidar notas diárias em topic files
2. **Semana 2:** Primeira consolidação semanal de lessons
3. **Semana 3:** Avaliar score de automação (meta: 5.0+/10)
4. **Mês 2:** Refinar playbooks com base em feedback real

---

## 17. REFERÊNCIAS & SUPORTE

**Documentação OpenClaw:**
- https://docs.openclaw.ai/
- openclaw help (local)

**Comunidade & Recursos:**
- Discord: https://discord.com/invite/clawd
- Docs Local: openclaw docs

---

## 18. NOTAS IMPORTANTES

✅ **Este PRD é totalmente dinâmico e replicável em qualquer OpenClaw**  
✅ **OBRIGATÓRIO: Personalizar SOUL.md, USER.md, IDENTITY.md com identidade do seu copiloto**  
✅ **OBRIGATÓRIO: Editar AGENTS.md com as diretrizes corretas do seu contexto**  
✅ **Não é necessário estar em production para testar - comece em dev/sandbox**  
✅ **Começa com estrutura mínima, expande conforme feedback real**  
✅ **Autonomia sempre respeita limites definidos no AGENTS.md**  

---

**FIM DO PRD**
