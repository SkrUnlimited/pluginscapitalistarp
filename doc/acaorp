# 🚔 AcaoRP — Resumo Rápido de Comandos e Permissões

Documento curto para operação do dia a dia:  
o que cada comando faz, qual permissão precisa e como funciona na prática.

---

## 1️⃣ Comandos Principais

| Comando | Permissão | Como funciona |
|----------|------------|---------------|
| `/roubo iniciar [acaoId]` | `sistemaacoes.acao` | Inicia uma ação olhando para um alvo válido. Se informar `acaoId`, força aquela ação. |
| `/acao iniciar [acaoId]` | `sistemaacoes.acao` | Alias do comando acima. |
| `/iracao [ID]` | `sistemaacoes.iracao` | Marca no mapa a ocorrência ativa/recente. Uso focado em policial. |
| `/historicoacao [1-10]` | `sistemaacoes.iracao` | Lista ocorrências recentes e ajuda a encontrar o ID para usar no `/iracao`. |

### Aliases de histórico

- `acaohistorico`
- `histiricoacao`

---

## 2️⃣ Comandos Administrativos (Staff)

| Comando | Permissão | Como funciona |
|----------|------------|---------------|
| `/acao criar <nome>` | `sistemaacoes.acao` + `sistemaacoes.admin` | Cria nova ação usando o alvo que você está olhando. |
| `/acao ativar <acao> [raio]` | `sistemaacoes.acao` + `sistemaacoes.admin` | Ativa/atualiza um local ativo da ação no alvo olhado. |
| `/acao desativar <acao>` | `sistemaacoes.acao` + `sistemaacoes.admin` | Remove o local ativo da ação no alvo olhado. |
| `/acao pontos <acao>` | `sistemaacoes.acao` + `sistemaacoes.admin` | Mostra os pontos (locais ativos) da ação. |
| `/acaocriar <nome>` | `sistemaacoes.admin` | Comando legado para criar ação. |
| `/acaoeditar <acao> ...` | `sistemaacoes.admin` | Edita GUID/tokens, nome, status ativo e parâmetros da ação. |
| `/acaoinfo <acao>` | `sistemaacoes.admin` | Exibe dados da ação (ID, slug, permissões, tokens etc.). |
| `/acaolistar` | `sistemaacoes.admin` | Lista todas as ações cadastradas. |
| `/acaoexcluir <acao>` | `sistemaacoes.admin` | Exclui uma ação da configuração. |

---

## 3️⃣ Permissões por Ação (além do comando)

Cada ação possui permissões próprias:

### 🔐 PermissaoRoubo
- Necessária para iniciar a ação.
- Exemplo padrão:
  `sistemaacoes.roubar.<slug_da_acao>`

### 📡 PermissaoIr
- Necessária para rastrear aquela ação via `/iracao`.
- Exemplo padrão:
  `sistemaacoes.ir.<slug_da_acao>`

**Importante:**  
Ter permissão do comando não substitui a permissão específica da ação.

---

## 4️⃣ Regras de Funcionamento Importantes

- O jogador precisa estar olhando para um alvo válido (token/GUID liberado para a ação).
- O local precisa estar ativo em `LocaisAtivos` (raio, posição e escopo compatíveis).
- Se `CrewAtiva = true`, o plugin valida participação de equipe conforme `CrewModoValidacao`.
- Policial para contestação/recompensa segue as regras de participação/atividade definidas na config.
- Webhooks usam emojis; chat do servidor permanece sem emoji.

---

## 5️⃣ Permissões Globais Mais Usadas

| Permissão | Função |
|------------|--------|
| `sistemaacoes.acao` | Acesso ao comando principal (`/roubo` e alias `/acao`). |
| `sistemaacoes.iracao` | Acesso a `/iracao` e `/historicoacao`. |
| `sistemaacoes.admin` | Acesso aos comandos administrativos. |
| `sistemaacoes.policial` | Permissão de cargo policial (padrão, pode ser alterada na config). |
| `sistemaacoes.crew` | Permissão opcional usada na elegibilidade de equipe. |

---

## 6️⃣ Observação Técnica

Os campos de config:

- `Global.PermissaoComandoAcao`
- `Global.PermissaoComandoIracao`

Existem na configuração, porém os comandos atualmente registrados utilizam as permissões fixas declaradas diretamente nas classes de comando.
