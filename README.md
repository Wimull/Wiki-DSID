# Wiki-DSID

# Uno Distribuído

## Visão Geral

Uno Distribuído é um protótipo de jogo de Uno que explora uma arquitetura peer-to-peer com controle de estado por contrato em uma aplicação desktop moderna. O objetivo é permitir que vários jogadores participem de uma partida distribuída usando Electron, React e TypeScript, com troca de mensagens em tempo real via WebSocket.

---

## Objetivo do Projeto

- Construir um sistema de Uno em que cada jogador mantenha uma cópia local do estado do jogo.
- Implementar validação de ações por consenso entre os pares antes de aplicar qualquer mudança de estado.
- Estudar e aplicar um modelo de comunicação distribuída baseado em contrato.
- Garantir execução em duas ou mais máquinas para simular um ambiente real.

---

## Limitações Conhecidas

- Cada jogador pode visualizar a mão de outros jogadores e o baralho.
- O sistema ainda não resolve problemas de confidencialidade.
- O foco está na arquitetura distribuída e consenso, não em segurança.

---

## Modelo Estudado e Escolhido

- Modelo estudado: Peer-to-Peer
- Modelo adotado: Peer-to-Peer com controle lógico centralizado por sala

Cada participante é um nó ativo que:
- Mantém uma cópia do estado
- Participa da validação das ações
- Contribui para o consenso distribuído

---

## Arquitetura de Software

- Plataforma: Electron
- Interface: React + TypeScript
- Comunicação: WebSockets persistentes

### Arquitetura de Mensagens por Contrato

- Toda ação é enviada para todos os pares
- Cada nó valida localmente a mensagem
- A ação só é aplicada após consenso coletivo
- Middleware garante integridade antes de alterar o estado

---

## Nomeação e Processos

### Nomeação

**Recursos identificados**
- `PlayerID` → jogador
- `roomID` → sala
- `messageID` → mensagem

**Esquema de nomeação**
- Plano

**Resolução de nomes**
- Tabela de mapeamento (lookup direto)

---

### Processos

**Uso de threads**
- Sim, para:
  - Recepção de mensagens
  - Processamento concorrente
  - Atualização de estado

**Modelo de servidor**
- Stateless  
- Estado mantido localmente e sincronizado via consenso

**Virtualização**
- Uso de containers (Docker)
- Facilita testes distribuídos e replicação de ambiente

---

### Sincronização e Controle

**Desincronização**
- Não necessária  
- Uso de relógio lógico para ordenação de eventos

**Exclusão mútua distribuída**
- Não necessária  
- Consenso já garante consistência

**Algoritmo de eleição**
- Sim  
- Algoritmo em corrente  
- Define o controlador da sala

---

### Comunicação Avançada

**Uso de Pub/Sub**
- Não utilizado  
- Comunicação direta via WebSocket (broadcast controlado)

---

## Papéis e Funções

- Um jogador assume o papel de **controlador da sala**
- Responsável por:
  - Gerenciar IPs dos jogadores
  - Ajudar na descoberta inicial de pares
- O controlador **não decide sozinho**
- Toda decisão depende do consenso dos nós

---

## Comunicação

### Tipo

- Síncrona
- Conexões persistentes

### Protocolo

- WebSocket
- Comunicação orientada a eventos
- Formato JSON
- Uso de keepAlive para manter conexões

---

## Formato de Mensagens

```json
{
  "type": "string",
  "seq": 1,
  "data": {}
}
```

---

## Tipos de Mensagens

- `action` → ação de jogo  
- `agree` → validação positiva  
- `disagree` → rejeição  
- `keepAlive` → manter conexão  
- `ACK` → confirmação de recebimento  
- `ERR` → erro  

---

## Validação e Middleware

- Todas as mensagens passam por validação antes de alterar o estado  

### Verificações incluem:

- Estrutura (JSON válido)  
- Sequência (`seq`)  
- Regras do jogo  

### Fluxo

1. Recebe mensagem  
2. Valida contrato  
3. Responde com `agree` ou `disagree`  
4. Só aplica mudança se houver consenso  

---

## Testes

- Testes manuais  
- Execução em múltiplas máquinas  

### Cenários

- Conexão entre jogadores  
- Envio de ações e consenso  
- Desconexão de pares  
- Reconexão e sincronização  
- Verificação de `keepAlive`  

---

## Diagrama de Sequência (Lógico)

1. Jogador A envia `action`  
2. Todos os pares recebem  
3. Cada nó valida  
4. Respondem com `agree` ou `disagree`  
5. Se todos concordarem:
   - Estado é atualizado  
6. Caso contrário:
   - A ação é abortada  
7. `keepAlive` mantém conexões  

---

## Caminho Futuro e Melhorias

- Proteger informações (mãos e baralho)  
- Implementar consenso tolerante a falhas  
- Criar testes automatizados  
- Melhorar descoberta de peers (sem IP fixo)  

---

## Conclusão

Este projeto demonstra como construir um sistema distribuído baseado em consenso usando um jogo simples como estudo de caso. A arquitetura peer-to-peer com validação por contrato garante consistência do estado mesmo sem um servidor central tradicional, enquanto WebSockets permitem comunicação eficiente em tempo real.
