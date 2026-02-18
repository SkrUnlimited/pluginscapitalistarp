# Doc de Fluxo e Variáveis — AçãoRP

Documentação técnica completa do sistema de Ações (Roleplay), incluindo fluxo interno, estados de ocorrência e todas as variáveis configuráveis.

## Índice

- [1) Visão Geral do Sistema](#1-visão-geral-do-sistema)
- [2) Fluxo Completo da Ação](#2-fluxo-completo-da-ação)
  - [2.1 Início](#21-início)
  - [2.2 Validações Executadas](#22-validações-executadas)
  - [2.3 Criação de Sessão](#23-criação-de-sessão)
  - [2.4 Monitoramento em Tempo Real](#24-monitoramento-em-tempo-real)
  - [2.5 Conclusão](#25-conclusão)
- [3) Variáveis Globais](#3-variáveis-globais)
  - [Ativação e Permissões](#ativação-e-permissões)
  - [Limites e Cooldowns](#limites-e-cooldowns)
  - [Retenção e Histórico](#retenção-e-histórico)
  - [Balanceamento de Recompensa](#balanceamento-de-recompensa)
  - [Webhooks e Identidade](#webhooks-e-identidade)
- [4) Variáveis por Ação](#4-variáveis-por-ação)
  - [Validação e Distância](#validação-e-distância)
  - [Execução e Cancelamento](#execução-e-cancelamento)
  - [Itens e Recompensa](#itens-e-recompensa)
- [5) Estados de Ocorrência](#5-estados-de-ocorrência)

---

# 1) Visão Geral do Sistema

O sistema de Ações (ex: roubos) funciona em etapas bem definidas:

1. O jogador inicia a ação via comando.
2. O sistema executa validações server-side.
3. Se aprovado, cria uma sessão ativa.
4. Um monitor acompanha a ação em tempo real.
5. A ação pode ser concluída, contestada ou cancelada.
6. A ocorrência recebe um estado final.

---

# 2) Fluxo Completo da Ação

## 2.1 Início

Comandos:
- `/acao iniciar <acaoId>`
- `/roubo iniciar`

Requisitos:
- O jogador deve estar mirando em um alvo válido.
- A ação precisa existir e estar ativa.
- O jogador deve possuir permissão.

---

## 2.2 Validações Executadas

O sistema valida:

- Sistema global ativo
- Permissão do comando
- Permissão específica da ação
- Bloqueio de policial (se configurado)
- Alvo permitido
- Distância válida
- Local ativo
- Limite de ações simultâneas
- Cooldown
- Mínimo de policiais online
- Limite diário
- Item necessário (consumido no início)
- Regras de crew (se habilitado)

Se qualquer verificação falhar → a ação não inicia.

---

## 2.3 Criação de Sessão

Se todas as validações forem aprovadas:

- Cria `RobberySession`
- Cria `RobberyOccurrence`
- Define estado inicial como `Active`

---

## 2.4 Monitoramento em Tempo Real

O sistema executa um loop constante que pode cancelar a ação caso:

- Jogador receba dano (se configurado)
- Jogador morra
- Jogador desconecte
- Jogador saia do raio
- Jogador atire (se bloqueado)
- Jogador seja algemado

Se cancelado → estado `Canceled`.

---

## 2.5 Conclusão

Quando o tempo termina:

- Calcula recompensa base
- Aplica jackpot (se ativo)
- Entra diretamente em `Completed`
- Ou vai para `PendingContestation` se contestação estiver ativa

---

# 3) Variáveis Globais  
`SistemaAcoesGlobalConfiguration`

## Ativação e Permissões

### `Ativo`
Define se o sistema de ações está habilitado globalmente.

### `GrupoPolicialQAP`
Grupo utilizado para identificar policiais no sistema.

### `PermissaoCargoPolicial`
Permissão que determina quais jogadores são considerados policiais.

### `PermissaoComandoAcao`
Permissão necessária para utilizar o comando `/acao`.

### `PermissaoComandoIracao`
Permissão necessária para utilizar o comando administrativo `/ir`.

### `AdminContaComoPolicial`
Se verdadeiro, administradores contam na quantidade mínima de policiais online.

### `BloquearRouboCargoPolicial`
Impede que jogadores com cargo policial iniciem ações.

### `AdminIgnoraBloqueioRouboPolicial`
Permite que administradores ignorem o bloqueio de roubo policial.

---

## Limites e Cooldowns

### `ModoCooldown`
Define como o cooldown funciona (global, individual ou desativado).

### `CooldownGlobalSegundos`
Tempo de espera (em segundos) antes de nova ação ser permitida.

### `MaxAcoesSimultaneas`
Número máximo de ações ativas ao mesmo tempo no servidor.

### `LimiteDiarioRoubosPorPlayer`
Quantidade máxima de ações que um jogador pode realizar por dia.

### `ResetDiarioFuso`
Fuso horário usado para resetar o limite diário.

---

## Retenção e Histórico

### `RetencaoOcorrenciasSegundos`
Tempo que ocorrências ficam armazenadas antes de serem removidas.

---

## Balanceamento de Recompensa

### `AtivarDecaimentoRepeticaoJogador`
Reduz recompensa se o jogador repetir ações em curto período.

### `JanelaDecaimentoRepeticaoSegundos`
Janela de tempo considerada para cálculo de repetição.

### `ReducaoPorRouboRepetidoPercent`
Percentual de redução aplicado por repetição.

### `MultiplicadorMinimoDecaimentoJogador`
Limite mínimo que a recompensa pode atingir após reduções.

### `AtivarOrcamentoJogadorHora`
Limita quanto um jogador pode ganhar por hora.

### `OrcamentoJogadorPorHora`
Valor máximo permitido por hora para cada jogador.

### `MultiplicadorAposOrcamentoJogador`
Multiplicador aplicado após ultrapassar orçamento.

### `AtivarTetoGlobalHora`
Ativa limite global de recompensa por hora no servidor.

### `TetoGlobalRecompensaPorHora`
Valor máximo global permitido por hora.

### `MultiplicadorAposTetoGlobal`
Multiplicador aplicado após atingir teto global.

---

## Webhooks e Identidade

### `HatsOcultamRostoIDs`
Lista de IDs de chapéus que ocultam identidade do jogador.

### `MascarasOcultamRostoIDs`
Lista de IDs de máscaras que ocultam identidade.

### `WebhookPoliciaUrl`
Webhook para logs policiais.

### `WebhookStaffUrl`
Webhook para logs administrativos.

### `WebhookTimeoutSegundos`
Tempo máximo de espera para resposta do webhook.

### `WebhookTentativas`
Número de tentativas em caso de falha no envio.

### `WebhookRetryAtrasoMs`
Tempo de espera entre tentativas.

---

# 4) Variáveis por Ação  
`SistemaAcaoParametrosConfiguration`

## Validação e Distância

### `MinPoliciaisOnline`
Número mínimo de policiais online exigido para iniciar a ação.

### `DistanciaMaximaInteracao`
Distância máxima permitida para iniciar interação com o alvo.

### `DistanciaMaximaRaycast`
Distância máxima do raycast para validar o alvo.

---

## Execução e Cancelamento

### `TempoRouboSegundos`
Tempo total da ação antes de ser concluída.

### `RaioMaximoDuranteRoubo`
Raio máximo permitido durante a execução da ação.

### `CancelarAoReceberDano`
Cancela ação se jogador receber dano.

### `CancelarAoAtirar`
Cancela ação se jogador disparar arma.

### `CancelarAoMorrer`
Cancela ação se jogador morrer.

### `CancelarAoDesconectar`
Cancela ação se jogador desconectar.

### `CancelarAoSairDaArea`
Cancela ação se sair do raio permitido.

### `CancelarAoSerAlgemado`
Cancela ação se jogador for algemado.

### `CooldownCapturaInicioRouboSegundos`
Tempo de imunidade após início do roubo.

### `CooldownCapturaFinalRouboSegundos`
Tempo de imunidade após finalização.

---

## Itens e Recompensa

### `ItemNecessarioParaIniciarID`
ID do item exigido para iniciar a ação.

### `ItemNecessarioQuantidade`
Quantidade do item exigida.

### `DinheiroSujoItemID`
ID do item usado como recompensa.

### `RecompensaBaseMin`
Valor mínimo da recompensa base.

### `RecompensaBaseMax`
Valor máximo da recompensa base.

### `JackpotAtivo`
Define se jackpot está habilitado.

### `JackpotChancePercent`
Chance percentual de ativação do jackpot.

### `JackpotBonusMin`
Valor mínimo do bônus de jackpot.

### `JackpotBonusMax`
Valor máximo do bônus de jackpot.

---

# 5) Estados de Ocorrência

### `Active`
A ação está em andamento e o monitor está ativo.

### `PendingContestation`
A ação terminou, mas aguarda validação da contestação policial.

### `Completed`
A ação foi concluída com sucesso.

### `Contested`
A polícia venceu a contestação.

### `Empate`
Nenhum lado atingiu domínio suficiente.

### `SemValidacaoPolicial`
A ação terminou sem validação policial mínima exigida.

### `Canceled`
A ação foi interrompida antes da conclusão.
