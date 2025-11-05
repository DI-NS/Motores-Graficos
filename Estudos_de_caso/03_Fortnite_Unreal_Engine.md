# 03 - Fortnite: Battle Royale com Unreal Engine 4

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Resumo Executivo

| Aspecto | Informação |
|--------|-----------|
| **Título** | Fortnite (BR) |
| **Desenvolvedora** | Epic Games |
| **Data de Lançamento** | 26 de julho de 2018 (modo BR) |
| **Plataformas** | PC, Mac, Mobile, PlayStation, Xbox, Switch, Cloud |
| **Engine** | Unreal Engine 4 (agora UE5) |
| **Receita Acumulada** | $9.1+ bilhões (lifetime) |
| **Pico de Jogadores** | 500M+ registrados |

---

## O Fenômeno Fortnite

### Contexto de 2018

**Cenário Pré-Fortnite:**
- Playerunknown's Battlegrounds (PUBG) dominava Battle Royale
- Mas PUBG tinha bugs, lag, performance ruins
- Mercado carecia de alternativa polida

**Lançamento de Fortnite BR:**
- Mais rápido, responsivo, sem bugs
- Gráficos divertidos (não militaristas)
- Cross-platform seamless
- Gratuito com monetização cosmética
- Atualizações frequentes (semanal)

**Resultado:** Desbancou PUBG em 6 meses

---

## Por que Unreal Engine 4?

### Requisitos do Battle Royale

```
100 jogadores     →  Performance crítica
Mundo de 10km²    →  Streaming massivo
Dinâmico (mapa muda)  →  Flexibilidade de engine
Múltiplas plataformas  →  Cross-platform portabilidade
Atualizações semanais  →  Ciclo de dev rápido
```

### Escolha: Unreal Engine 4

**Vantagens:**
```
✓ Blueprints permitem iteração rápida
✓ Mais poderoso que Unity para AAA
✓ Cross-platform nativo
✓ Suporte console de primeira classe
✓ Comunidade AAA-focused
✓ Performance superior para 100+ atores
```

**Desvantagem:**
```
❌ Mais complexo que Unity
❌ Maior requisito de conhecimento
❌ Builds maiores (~50GB de dados)
```

---

## Arquitetura de Fortnite BR

### Pilares Tecnológicos

#### 1. Replicação de Rede

```cpp
// Unreal Replication: Sincronizar estado entre jogadores
class ACharacter {
public:
    UPROPERTY(Replicated)
    FVector PlayerLocation;
    
    UPROPERTY(Replicated)
    FRotator PlayerRotation;
    
    UPROPERTY(Replicated)
    uint8 EquippedWeapon;
};

// Servidor envia atualizações (20-30 Hz)
// Clients predizem movimento localmente (60 Hz)
// Resultado: Suavidade + correto
```

#### 2. Streaming de Mundo

```
Mapa Fortnite: ~10 km²

Divisão em chunks:
┌─────┬─────┬─────┐
│     │     │     │
├─────┼─────┼─────┤
│  ╳  │  ✓  │     │ ✓ = Carregado
├─────┼─────┼─────┤ ✗ = Descarregado
│     │     │     │ ╳ = Jogador
└─────┴─────┴─────┘

Apenas chunks próximos carregados
Dynamic LOD baseado em distância
```

#### 3. Rendering Otimizado para 100 Players

```
Desafio: Renderizar 100 personagens animados
Solução Fortnite:

1. GPU Instancing
   - Mesmo modelo = 1 draw call para múltiplos
   - 100 players = 1-5 draw calls (vs 100)

2. Skeletal Mesh Optimization
   - Reduzir qualidade de animação em distância
   - Ultra próximo: Full skeletal
   - Longe: Pose estática

3. LOD Agressivo
   - Perto: Full model + detalhe
   - Meio: Modelo simplificado
   - Longe: Silhueta simples
   - Muito longe: Quad (sprite 2D)
```

#### 4. Netcode Customizado

```
Problema: Unreal's default netcode é bom, mas Fortnite exige o melhor

Solução: Custom networking
- Client-side prediction (local input)
- Server reconciliation (correção se necessário)
- Interest-based replication (não enviar tudo)
- Tickrate de 30Hz (balança entre precisão e bandwidth)

Resultado: Responsividade 60 FPS localmente
```

---

## Features que Exigem Engenharia Especial

### 1. Mapa Dinâmico

**Desafio:** Mapa muda a cada temporada

**Solução:**
```
- Temporada 1: Ilha original
- Temporada 2: Parcial destruição
- Temporada 3: Novo bioma aparece
- Temporada X: Meteorito cai (evento ao vivo!)

Implementação:
- Blueprint procedural para remover/adicionar geometria
- Streaming otimizado para mudanças
- Sem redownload (patches incrementais)
```

### 2. Building System

**Desafio:** Até 200 estruturas simultâneas sendo construídas

**Solução:**
```cpp
// Phantom collision (validação local)
// Construir localmente > Enviar servidor
// Servidor valida (anti-cheat)
// Todos os clientes atualizam

Resultado: Construção suave, mas segura
```

### 3. Eventos Globais Ao Vivo

**Desafio:** 50M+ jogadores vendo mesmo evento

**Solução:**
```
- Evento em cinemática (não é gameplay real)
- Renderizado localmente (não precisa de sync perfeito)
- Todos veem ao mesmo tempo (coordenação via servidor)
- Exemplo: Meteoro caindo (Temporada 10)
```

### 4. Cosmética Customizável

**Desafio:** Cada jogador pode ter diferentes skins

**Solução:**
```
Modular system:
- Head, Torso, Arms, Legs como assets separados
- Combinações ilimitadas
- Material dynamic (cor, padrão)
- Replicated para todos jogadores
- Cache agressiva (memorizar skins já vistas)
```

---

## Performance e Otimizações

### Benchmarks (UE4)

```
Plataforma | FPS | Resolução | Configuração
-----------|-----|-----------|-------------
PS5        | 60  | 1440p     | Gráficos altos
Xbox SX    | 60  | 1080p     | Gráficos altos
PC High    | 240+| 1080p-4K  | Customizável
PC Low     | 60  | 720p      | Eficiência
Mobile     | 30-60| 1080p    | Dinâmico
Switch     | 30  | 1080p     | Reduzido
```

### Técnicas de Otimização

#### 1. Culling Inteligente

```
Viewfrustum culling:
- Não renderizar fora de tela

Occlusion culling:
- Não renderizar atrás de paredes

Distance LOD:
- Objetos distantes = menos polígonos

Result: 70% redução de drawcalls
```

#### 2. Shaders Otimizados

```
Fortnite style (cartoon):
- Menos complexidade que fotorrealisma
- Mais tolerante a simplificações
- Mantém estilo mesmo com redução

Técnica: Toon shading com simplificação
```

#### 3. Audio Otimizado

```
100 jogadores = 100 sons potenciais
Solução:
- Apenas ~10-15 sons "importantes" reproduzem
- Outros são culled
- Dinâmico baseado em camera position
```

---

## Monetização e Engine

### Modelo Free-to-Play + Battle Pass

```
Receita Fortnite:
- Battle Pass ($10/temporada, ~9 temporadas/ano)
- Cosmética (skins $20, emotes $5, etc)
- Turbo Pass (acelerar battle pass)
```

### Por que Unreal Engine facilita isso?

```
Blueprints:
- Designers implementam novo battle pass em dias
- Não precisa recompilar engine
- Hot-reload permite testes rápidos

Resultado: Novas features a cada semana
```

---

## Impacto na Indústria

### Antes de Fortnite BR (2017)

```
❌ Battle Royale era visto como nicho
❌ PUBG tinha qualidade ruim (lag, crashes)
❌ Free-to-play cosmética-only era suspeito
❌ Atualizações semanais = unsustainable
```

### Depois de Fortnite BR (2018+)

```
✅ Battle Royale é mainstream (Warzone, Valorant, Apex)
✅ Qualidade polida é esperado
✅ Free-to-play cosmética é modelo padrão
✅ Atualizações frequentes são expectativa dos jogadores
```

### Competidores que Surgiram

- Call of Duty: Warzone (2020)
- Apex Legends (2019)
- Valorant (2020)
- Todos tentam copiar modelo de sucesso de Fortnite

---

## Lições Técnicas Principais

### 1. Engine Poderosa ≠ Desenvolvimento Lento

Fortnite prova que UE4 (complexo) pode iterar tão rápido quanto Unity (simples), se bem feito com:
- Blueprints para gameplay
- C++ para systems críticos
- Asset pipeline otimizado

### 2. Performance = Design Constraint

Fortnite não tenta renderizar tudo. Em vez disso:
- Estilo cartoon = permite simplificações
- Design consciente de performance
- Tradeoff entre visual e gameplay

### 3. Network é Gameplay

Battle Royale com 100 players é primariamente um desafio de rede:
- Latência = morte (quem vê primeiro ganha)
- Sincronização = integridade
- Anti-cheat = justiça

### 4. Atualizações Contínuas = Retenção

Fortnite mudou a expectativa da indústria:
- Conteúdo novo SEMPRE
- Eventos inesperados
- Cosmética exclusiva (FOMO)
- Resultado: Jogadores voltam semanalmente

---

## Referências

EPIC GAMES. **Fortnite Official Website**. Disponível em: <https://www.fortnite.com>. Acesso em: 2025.

GREGORY, J. **Game Engine Architecture**. 3. ed. CRC Press, 2018.

EPIC GAMES. **Unreal Engine Networking and Replication Documentation**. Disponível em: <https://docs.unrealengine.com>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Próximo:** 04_The_Witcher_3_REDengine.md

