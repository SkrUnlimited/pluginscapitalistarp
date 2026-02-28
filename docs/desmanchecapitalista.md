# 🚗 DesmancheRP

Plugin para **RocketMod (Rocket.Unturned)** que permite o desmanche controlado de veículos dentro de zonas configuradas, com sistema opcional de **recuperação interna para o proprietário** via XP.

---

## 📦 Requisitos

- Unturned  
- RocketMod (Rocket.Unturned)  
- Permissões configuradas no Rocket  

---

## 📥 Instalação

1. Compile o projeto em **Release**.
2. Copie os arquivos:
   ```
   DesmancheRP.dll
   DesmancheRP.XmlSerializers.dll
   ```
   de:
   ```
   DesmancheRP/bin/Release/
   ```
   para:
   ```
   Rocket/Plugins/DesmancheRP/
   ```
3. Inicie o servidor para gerar automaticamente:
   ```
   DesmancheRP.configuration.xml
   ```
   ou copie:
   ```
   DesmancheRP.config.example.xml
   ```
   e renomeie para:
   ```
   DesmancheRP.configuration.xml
   ```
4. Configure zonas, permissões e parâmetros conforme necessário.

---

## 🎮 Comandos

### 🔧 Jogador

| Comando | Descrição |
|----------|------------|
| `/desmanche iniciar` | Inicia o desmanche do veículo que o jogador está olhando |
| `/desmanche cancelar` | Cancela o desmanche ativo |
| `/roubados` | Lista veículos disponíveis para recuperação |
| `/resgatar <id>` | Recupera um veículo da lista pagando XP |

---

### 🛠️ Administração

| Comando | Descrição |
|----------|------------|
| `/desmanche criar <nome> <raio>` | Cria ou atualiza uma zona na posição atual |
| `/desmanche setpreco <vehicleAssetId> <xp>` | Define custo de resgate específico por veículo |
| `/desmanche removepreco <vehicleAssetId>` | Remove override e volta ao preço padrão |

---

## 🔐 Permissões

| Permissão | Padrão | Acesso |
|------------|----------|----------|
| `PermissaoUsar` | `desmanche.usar` | Iniciar e cancelar desmanche |
| `PermissaoCriarZona` | `desmanche.staff` | Criar zonas |
| `PermissaoGerenciarPrecoResgate` | `desmanche.staff` | Gerenciar preços de resgate |

---

## ⚙️ Funcionamento

1. O jogador usa `/desmanche iniciar` olhando para um veículo válido.
2. O veículo deve:
   - Estar dentro de uma zona configurada  
   - Estar parado  
   - Não possuir passageiros  
3. O jogador deve permanecer dentro da zona durante todo o processo.
4. Após o tempo configurado:
   - O veículo é removido  
   - O jogador recebe recompensa configurada  

---

## ❌ Cancelamento Automático

O processo é interrompido automaticamente se:

- O jogador sair da zona  
- O veículo sair da zona  
- O veículo se mover mais de ~1.5m  
- O veículo entrar em movimento  
- Algum passageiro entrar  
- O jogador receber dano  
- O jogador atirar durante o processo  

---

## 🔄 Sistema de Recuperação Interna

Quando ativado:

- Se o veículo possuir **owner lock**, é criado um registro em:
  ```
  DesmancheRP.Recuperacoes.xml
  ```
- O proprietário pode:
  - Usar `/roubados` para visualizar seus veículos disponíveis
  - Usar `/resgatar <id>` para recuperar pagando XP

### 💰 Sistema de Custo

O valor de resgate:

- Usa `PrecoXPResgatePadrao`
- Pode ser sobrescrito por veículo através de:
  - `PrecosResgateOverrides`
  - `/desmanche setpreco`

---

## ⚙️ Configuração (Principais Campos)

| Campo | Descrição |
|--------|------------|
| `DistanciaMaximaRaycast` | Distância máxima para localizar o veículo via raycast |
| `DistanciaMaximaInteracao` | Distância máxima entre jogador e veículo |
| `ZonaDesmanche` | Zona única (retrocompatibilidade) |
| `ZonasDesmanche` | Lista de zonas (Nome, Position, Raio) |
| `TempoDesmancheSegundos` | Tempo necessário para concluir |
| `RewardItemId` | ID do item recompensa |
| `RewardMin` / `RewardMax` | Quantidade mínima e máxima |
| `Overrides` | Override de recompensa por `VehicleAssetId` |
| `RecuperacaoInternaAtiva` | Ativa sistema de recuperação |
| `ArquivoRecuperacaoInterna` | Nome do arquivo de registros |
| `PrecoXPResgatePadrao` | XP padrão para resgate |
| `PrecosResgateOverrides` | Overrides de custo por veículo |
| `Debug` | Ativa logs detalhados no console |

---

## 🧩 Observações

- Totalmente compatível com múltiplas zonas.
- Sistema seguro contra abuso (movimento, dano, passageiros).
- Recuperação persistente baseada em arquivo XML.
- Ideal para servidores RP com economia baseada em XP.
