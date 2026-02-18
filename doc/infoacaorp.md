# Doc de Fluxo e Variáveis — AçãoRP

Este documento descreve o **fluxo completo** de uma ação (roleplay) e as **variáveis** que controlam o comportamento do sistema, com base no plugin atual.

## Índice
- [1. Visão geral do fluxo](#1-visão-geral-do-fluxo)
- [2. Fluxo detalhado (passo a passo)](#2-fluxo-detalhado-passo-a-passo)
- [3. Variáveis globais](#3-variáveis-globais-sistemaacoesglobalconfiguration)
- [4. Variáveis por ação](#4-variáveis-por-ação-sistemaacaoparametrosconfiguration)
- [5. Estrutura de ação](#5-estrutura-de-ação-sistemaacaoconfiguration)
- [6. Estados de ocorrência](#6-estados-de-ocorrência)
- [7. Observações operacionais](#7-observações-operacionais)

---

## 1) Visão geral do fluxo

1. O jogador inicia a ação com `/acao iniciar <acaoId>` (ou `/roubo iniciar`).
2. O plugin valida alvo, permissões, polícia online, cooldown, limite diário e itens.
3. Se tudo ok, cria uma sessão ativa (`RobberySession`) e abre uma ocorrência.
4. Um monitor acompanha em tempo real e **cancela** em situações proibidas.
5. Quando o tempo termina, a ocorrência é **concluída**.
6. Se houver **contestação ativa**, a recompensa pode ficar pendente até o fim da janela.
7. Ao final, a ocorrência vira: **Sucesso**, **Contestado**, **Empate** ou **Sem validação policial**.

---

## 2) Fluxo detalhado (passo a passo)

### 2.1 Início do comando
- Comandos: `/acao iniciar <acaoId>` ou `/roubo iniciar`
- Requisito: o player deve estar **olhando para um alvo válido**.

### 2.2 Validações iniciais
- Sistema ativo
- Jogador não é policial (se bloqueio estiver ligado)
- Ação existe e está ativa
- Permissão de roubo da ação (`PermissaoRoubo`)
- Alvo permitido + distância válida
- Local ativo (se configurado)
- Limite de ações simultâneas
- Cooldown global (se ativo)
- Mínimo de policiais online
- Limite diário do jogador
- Item necessário (consumo e possível reembolso se falhar)
- Crew válida (se habilitada)

### 2.3 Criação da sessão
- Cria `RobberySession` com: player, ação, alvo e parâmetros resolvidos
- Cria/atualiza `RobberyOccurrence` como ativa

### 2.4 Alertas policiais
- Se `AlertaPoliciaImediato = true`, envia alerta **exato** imediatamente

### 2.5 Monitoramento em tempo real
O monitor roda em loop e pode cancelar se ocorrer:
- dano
- morte
- desconexão
- sair do raio
- atirar
- ser algemado

### 2.6 Conclusão do roubo
- Calcula recompensa base + jackpot (se ativo)
- Marca a ocorrência como concluída

### 2.7 Contestação (se ativa)
Quando `ContestacaoAtiva` **e** `RecompensaSomenteAposContestacao`:
- a recompensa fica pendente
- a polícia precisa manter presença e movimento dentro do raio para validar

### 2.8 Resultado final
- **Sucesso:** recompensa entregue (ou pendente se inventário cheio)
- **Contestado:** bandido perde recompensa, polícia pode receber prêmio
- **Empate:** prêmios fixos opcionais
- **Sem validação policial:** sem recompensa se a validação não ocorreu

---

## 3) Variáveis globais (SistemaAcoesGlobalConfiguration)

### 3.1 Ativação e permissões
- `Ativo`
- `GrupoPolicialQAP`
- `PermissaoCargoPolicial`
- `PermissaoComandoAcao`
- `PermissaoComandoIracao`
- `AdminContaComoPolicial`
- `BloquearRouboCargoPolicial`
- `AdminIgnoraBloqueioRouboPolicial`

### 3.2 Limites e cooldowns
- `ModoCooldown`
- `CooldownGlobalSegundos`
- `MaxAcoesSimultaneas`
- `LimiteDiarioRoubosPorPlayer`
- `ResetDiarioFuso`

### 3.3 Retenção e histórico
- `RetencaoOcorrenciasSegundos`

### 3.4 Balanceamento de recompensa
- `AtivarDecaimentoRepeticaoJogador`
- `JanelaDecaimentoRepeticaoSegundos`
- `ReducaoPorRouboRepetidoPercent`
- `MultiplicadorMinimoDecaimentoJogador`
- `AtivarOrcamentoJogadorHora`
- `OrcamentoJogadorPorHora`
- `MultiplicadorAposOrcamentoJogador`
- `AtivarTetoGlobalHora`
- `TetoGlobalRecompensaPorHora`
- `MultiplicadorAposTetoGlobal`

### 3.5 Identidade e webhooks
- `HatsOcultamRostoIDs`
- `MascarasOcultamRostoIDs`
- `WebhookPoliciaUrl`
- `WebhookStaffUrl`
- `WebhookTimeoutSegundos`
- `WebhookTentativas`
- `WebhookRetryAtrasoMs`

---

## 4) Variáveis por ação (SistemaAcaoParametrosConfiguration)

### 4.1 Validação e distâncias
- `MinPoliciaisOnline`
- `DistanciaMaximaInteracao`
- `DistanciaMaximaRaycast`

### 4.2 Execução e cancelamentos
- `TempoRouboSegundos`
- `RaioMaximoDuranteRoubo`
- `CancelarAoReceberDano`
- `CancelarAoAtirar`
- `CancelarAoMorrer`
- `CancelarAoDesconectar`
- `CancelarAoSairDaArea`
- `CancelarAoSerAlgemado`
- `CooldownCapturaInicioRouboSegundos`
- `CooldownCapturaFinalRouboSegundos`

### 4.3 Itens e recompensa
- `ItemNecessarioParaIniciarID`
- `ItemNecessarioQuantidade`
- `DinheiroSujoItemID`
- `RecompensaBaseMin`
- `RecompensaBaseMax`
- `JackpotAtivo`
- `JackpotChancePercent`
- `JackpotBonusMin`
- `JackpotBonusMax`

### 4.4 Alertas policiais
- `AlertaPoliciaImediato`
- `AlertaPoliciaAproximadoRaio`
- `SegundosParaAlertaExato`

### 4.5 Contestação (básico)
- `ContestacaoAtiva`
- `JanelaContestacaoSegundos`
- `RaioContestacaoMetros`
- `TempoMinimoPresencaPolicialSegundos`
- `PoliciaisMinimosParaContestacao`
- `JanelaAmostragemMovimentoContestacaoSegundos`
- `DistanciaMinimaMovimentoContestacaoMetros`
- `VelocidadeMinimaContestacaoMps`
- `TempoMinimoMovimentoPolicialValidacaoSegundos`
- `TempoDominioPolicialSegundos`
- `TempoQuebraContestacaoSegundos`
- `ExigirMovimentoPolicialParaLiberarRecompensa`
- `RecompensaSomenteAposContestacao`

### 4.6 Contestação (avançado)
- `ModoContestacaoAvancadoAtivo`
- `ContestacaoExtraPolicialReservaQuantidade`
- `ModoFugaDesvantagemDiferencaPolicial`
- `ModoFugaSemPressaoSegundos`
- `ModoFugaTempoMaximoSegundos`
- `ModoFugaRaioPressaoPolicialMetros`

### 4.7 Crew (equipe)
- `CrewAtiva`
- `CrewModoValidacao`
- `MinBandidosParticipantes`
- `MaxBandidosParticipantes`
- `RaioParticipacaoBandidosMetros`
- `MaxPoliciaisParticipantes`

### 4.8 Recompensas policiais e empate
- `RecompensaPolicialXpAtiva`
- `RecompensaPolicialXpQuantidade`
- `RecompensaPolicialItemAtiva`
- `RecompensaPolicialItemID`
- `RecompensaPolicialItemQuantidade`
- `EmpateRecompensaItemAtiva`
- `EmpateRecompensaItemID`
- `EmpateRecompensaItemQuantidade`
- `EmpateRecompensaXPPMAtiva`
- `EmpateRecompensaXPPMQuantidade`
- `EmpateRecompensaItemPMAtiva`
- `EmpateRecompensaItemPMID`
- `EmpateRecompensaItemPMQuantidade`

---

## 5) Estrutura de ação (SistemaAcaoConfiguration)

### 5.1 Identificação
- `Id`
- `NomeExibicao`
- `Tipo`
- `SlugComando`

### 5.2 Disponibilidade e alvo
- `Ativa`
- `PermitirObjetosMapa`
- `PermitirColocaveis`
- `TokensPermitidos`
- `LocaisAtivos`

### 5.3 Permissões
- `PermissaoRoubo`
- `PermissaoIr`

### 5.4 Overrides
- `Overrides` (substitui o `PadraoAcao`)

---

## 6) Estados de ocorrência
- `Active`
- `PendingContestation`
- `Completed`
- `Contested`
- `SemValidacaoPolicial`
- `Empate`
- `Canceled`

---

## 7) Observações operacionais
1. O item de início é consumido antes da ação. Se o início falhar depois, o item é devolvido.
2. O monitor roda em loop e aplica cancelamentos rapidamente.
3. A recompensa pode ser entregue parcialmente e completar depois (inventário cheio).
4. A contestação só existe quando `ContestacaoAtiva` e `RecompensaSomenteAposContestacao`.
5. O modo avançado pode forçar “modo fuga” com pressão policial.
