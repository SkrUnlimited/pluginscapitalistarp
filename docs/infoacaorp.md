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
- [6) Sistema de Contestação](#6-sistema-de-contestação)
  - [6.1 Quando a Contestação é Ativada](#61-quando-a-contestação-é-ativada)
  - [6.2 Janela de Contestação](#62-janela-de-contestação)
  - [6.3 Raio de Contestação](#63-raio-de-contestação)
  - [6.4 Presença Policial Mínima](#64-presença-policial-mínima)
  - [6.5 Validação de Movimento Policial](#65-validação-de-movimento-policial)
  - [6.6 Domínio Policial](#66-domínio-policial)
  - [6.7 Quebra de Contestação](#67-quebra-de-contestação)
  - [6.8 Resultado da Contestação](#68-resultado-da-contestação)
  - [6.9 Modo Contestação Avançado](#69-modo-contestação-avançado)
  - [6.10 Fluxo Simplificado da Contestação](#610-fluxo-simplificado-da-contestação)
  - [6.11 Observações Importantes](#611-observações-importantes)

---

# 1) Visão Geral do Sistema

O sistema de Ações (ex: Caixa Eletrônico[ATM]) funciona em etapas bem definidas:

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

# 6) Sistema de Contestação

A contestação é o mecanismo que permite à polícia disputar o controle da ação após o término do tempo principal do roubo.

Ela adiciona uma segunda camada de validação antes da entrega definitiva da recompensa.

---

## 6.1 Quando a Contestação é Ativada

A contestação só ocorre se:

- `ContestacaoAtiva = true`
- E `RecompensaSomenteAposContestacao = true`

Se essas duas condições forem verdadeiras:

1. Ao terminar `TempoRouboSegundos`
2. A ocorrência NÃO vai direto para `Completed`
3. O estado muda para `PendingContestation`
4. A recompensa fica bloqueada temporariamente

Se qualquer uma dessas variáveis estiver desativada:
- O roubo finaliza normalmente em `Completed`

---

## 6.2 Janela de Contestação

Controlada por:

- `JanelaContestacaoSegundos`

Durante essa janela:

- A polícia pode disputar a ação
- O sistema mede presença policial
- O sistema mede movimento policial
- O sistema calcula domínio

Se o tempo acabar:
- O sistema resolve o resultado final

---

## 6.3 Raio de Contestação

Controlado por:

- `RaioContestacaoMetros`

Define a área onde policiais precisam estar para:

- Validar presença
- Gerar domínio
- Evitar quebra de contestação

Se o policial sair do raio:
- Ele deixa de contribuir para validação

---

## 6.4 Presença Policial Mínima

Controlado por:

- `PoliciaisMinimosParaContestacao`
- `TempoMinimoPresencaPolicialSegundos`

Regras:

- É necessário um número mínimo de policiais dentro do raio.
- Eles precisam permanecer pelo tempo mínimo configurado.
- Se não atingir esse requisito → contestação falha.

---

## 6.5 Validação de Movimento Policial

Para evitar contestação AFK, o sistema pode exigir movimento real.

Controlado por:

- `JanelaAmostragemMovimentoContestacaoSegundos`
- `DistanciaMinimaMovimentoContestacaoMetros`
- `VelocidadeMinimaContestacaoMps`
- `TempoMinimoMovimentoPolicialValidacaoSegundos`
- `ExigirMovimentoPolicialParaLiberarRecompensa`

Funcionamento:

- O sistema mede deslocamento dentro da janela.
- Mede velocidade mínima.
- Mede tempo mínimo em movimento válido.
- Se não cumprir → presença não é considerada válida.

---

## 6.6 Domínio Policial

Controlado por:

- `TempoDominioPolicialSegundos`

Se a polícia mantiver presença válida e contínua por esse tempo:

- O domínio policial é estabelecido.
- A ocorrência pode ir para `Contested`.

---

## 6.7 Quebra de Contestação

Controlado por:

- `TempoQuebraContestacaoSegundos`

Se a polícia perder presença contínua por esse tempo:

- A contestação pode ser quebrada.
- O domínio policial é reiniciado ou invalidado.

---

## 6.8 Resultado da Contestação

Ao final da `JanelaContestacaoSegundos`, o sistema decide:

### Caso 1 — Polícia venceu
Condições:
- Domínio atingido
- Presença válida
- Movimento validado

Resultado:
- Estado → `Contested`
- Bandido perde recompensa
- Polícia pode receber recompensa configurada

---

### Caso 2 — Bandido manteve domínio
Condições:
- Polícia não atingiu domínio mínimo

Resultado:
- Estado → `Completed`
- Recompensa é entregue ao bandido

---

### Caso 3 — Empate
Se habilitado e nenhum lado atingir domínio suficiente:

Resultado:
- Estado → `Empate`
- Pode haver recompensa parcial para ambos

---

## 6.9 Modo Contestação Avançado

Ativado por:

- `ModoContestacaoAvancadoAtivo`

Recursos adicionais:

### `ContestacaoExtraPolicialReservaQuantidade`
Permite exigir policiais extras como reserva.

### `ModoFugaDesvantagemDiferencaPolicial`
Ativa modo de fuga se houver grande diferença numérica.

### `ModoFugaSemPressaoSegundos`
Tempo necessário sem presença policial para permitir fuga.

### `ModoFugaTempoMaximoSegundos`
Tempo máximo permitido para fuga.

### `ModoFugaRaioPressaoPolicialMetros`
Define raio onde a presença policial gera "pressão".

---

## 6.10 Fluxo Simplificado da Contestação

Tempo do roubo termina
↓
ContestacaoAtiva?
↓
Sim → PendingContestation
↓
JanelaContestacaoSegundos ativa
↓
Polícia mantém domínio?
↓
Sim → Contested
Não → Completed


---

## 6.11 Observações Importantes

- Contestação não ocorre se `ContestacaoAtiva = false`
- Recompensa pode ficar bloqueada até o fim da janela
- Movimento policial pode ser obrigatório
- Sistema é totalmente server-side
- Evita contestação passiva/AFK

---

<p align="right">
⬆️ <a href="#índice">Voltar ao Índice</a>
</p>
