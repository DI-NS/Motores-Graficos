# 04 - The Witcher 3: Motor Proprietário REDengine

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Resumo Executivo

| Aspecto | Informação |
|--------|-----------|
| **Título** | The Witcher 3: Wild Hunt |
| **Desenvolvedora** | CD Projekt Red (Polônia) |
| **Data de Lançamento** | 19 de maio de 2015 |
| **Plataformas** | PC, PS4, Xbox One, Nintendo Switch, PlayStation 5, Xbox Series X|S |
| **Engine** | REDengine 3 (proprietária) |
| **Tempo de Desenvolvimento** | ~4 anos (2011-2015) |
| **Vendas** | 40+ milhões de cópias |
| **Motor** | REDengine (desenvolvido internamente) |

---

## Por que Escolher um Motor Proprietário?

### Contexto: CD Projekt Red em 2011

**Situação:**
- The Witcher 2 (2011) usava REDengine 2
- Sucesso relativo, mas tecnicamente limitado
- Ambição: Fazer RPG open-world AAA
- Competição: Skyrim já era referência

### Por que NÃO usar Unreal Engine 3?

**Razões Estratégicas:**
```
Unreal Engine 3 (2011):
❌ Licença cara para estúdio polonês (royalties altos)
❌ Orientado a ShootersFirst-person (The Witcher é RPG)
❌ Menos controle sobre otimizações
❌ Comunidade menos amigável a modificações

REDengine Proprietária:
✓ Controle total de código
✓ Otimizações específicas para RPG/narrativa
✓ Sem pagamento de royalties
✓ Customização ilimitada
```

### Decisão: Desenvolver REDengine 3

**Risco:**
- ~100 pessoas dedicadas apenas ao engine
- Tempo de desenvolvimento ~1 ano
- Sem ROI garantido

**Payoff:**
- Engine totalmente otimizada para visão de CD Projekt
- Controle de visual e gameplay completo
- Tecnologia de longo prazo (usada em Cyberpunk 2077)

---

## Arquitetura da REDengine 3

### Focos de Design

#### 1. Narrativa-First

```
Paradigma:
Traditional RPG:  Gameplay > Narrativa
The Witcher 3:    Narrativa ≈ Gameplay

Suporte:
- Dialogue system robusto
- Branching quest logic
- NPC AI narrativo (não tático)
- Cinematic direction integrada
```

#### 2. Open-World Seamless

```
Mundo de 136 km²:
- Novigrad (22 km²)
- Skellige Islands (15 km²)
- Velen (74 km²)
- + áreas menores

Requisitos:
- Streaming contínuo
- Pop-in invisível
- No loading durante exploration
```

#### 3. Visuais Fotorrealistas

```
Foco em:
- Iluminação dinâmica realista
- Material-based rendering (PBR)
- Física de água complexa
- Weather systems dinâmicos
```

### Stack Técnico

```
┌─────────────────────────────────────┐
│    Storytelling Layer               │
│  (Quests, Dialogue, Branching)      │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│    Gameplay Layer                   │
│  (Combat, Crafting, Alchemy)        │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│    World Layer                      │
│  (Open-world, NPCs, Creatures)      │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│    Rendering Layer                  │
│  (Graphics, Lighting, Effects)      │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│    Core Engine                      │
│  (Memory, Physics, IO, Tools)       │
└─────────────────────────────────────┘
```

---

## Desafios Técnicos Únicos

### 1. Mundo Aberto Seamless

**Problema:** 136 km² de mundo explorador sem carregamento

**Solução REDengine:**
```
Streaming inteligente:
- Mapa dividida em células
- Preload de células próximas durante gameplay
- Asynchronous I/O non-blocking
- Pano de cortina invisible (câmera nunca entra em zona de transição)
```

### 2. Número Massivo de NPCs

**Problema:** 800+ NPCs no mundo, cada um com:
- Diálogo customizado
- Rotina de IA
- Aparência única
- Histórico de interações

**Solução:**
```
AI Scheduling:
- NPCs só "ativos" dentro de distância da câmera (~500m)
- Fora disso, em "hibernação" (estado serializado)
- Ativação/desativação dinâmica

Memória:
- Cada NPC ~ 1-2 MB
- 800 NPCs = 1-2 GB apenas para NPCs
- Compressão agressiva de estados não-usados
```

### 3. Quests Branching

**Problema:** 100+ quests com múltiplas paths

**Solução:**
```csharp
// Quest scripting system
public class QuestNode {
    public string questName;
    public List<DialogueOption> options;
    public List<QuestNode> consequents;
    public bool[] stateFlags;  // Tracking quest estado
    
    public void ExecuteQuest() {
        if (stateFlags[0]) {
            // Path A
        } else if (stateFlags[1]) {
            // Path B  
        } else {
            // Default
        }
    }
}
```

### 4. Rendering Fotorrealista

**Problema:** Rendering completo com sombras dinâmicas em open world

**Técnicas:**
```
1. Screen-space techniques:
   - SSAO (ambient occlusion)
   - SSR (reflections)
   - SSDGI (indirect lighting approximation)

2. Pre-baked + Dynamic hybrid:
   - Static geometry: Light probes
   - Characters/objects: Dynamic lights
   - Mundo: Mix de ambos

3. Post-processing:
   - Bloom (weather effects)
   - Color grading (ambiente)
   - Cinematic focus
```

---

## Porabilidade e Otimização

### Ports para Múltiplas Plataformas

#### PC (Original)

```
Ultra (GeForce RTX):
- 4K @ 60 FPS
- Ray tracing on
- Tudo maxado

High (GTX 970):
- 1440p @ 60 FPS
- Sombras dinâmicas
- Qualidade média

Low (i5-6600):
- 1080p @ 30 FPS
- Sombras simplificadas
- Alguns efeitos desligados
```

#### PlayStation 5 / Xbox Series X|S

```
Estratégia: "Remasted"
- Gráficos next-gen
- Carregamento 60% mais rápido (SSD)
- Ray tracing implementado
- Loading nulo (vs 1 min no PS4)

Mudanças da engine:
- Aproveitou SSD nativo
- Vulkan → Custom PS5 API
- Memory optimization para 10GB disponível
```

#### Nintendo Switch (Surpresa)

```
EXTREMO Challenge:
- GPU 10x mais fraca
- RAM 4GB (vs 8GB PS4)
- Portátil (termicamente limitado)

Solução:
- Resolução: 540p handheld, 720p docked
- Draw calls: 90% reduzidos
- Texturas: 1/4 resolução
- Sombras: Baked apenas
- Performance: 30 FPS estável

Resultado: Jogável, não bonito, mas impressionante para Switch
```

---

## Storytelling Técnico

### Integração de Narrativa no Engine

```
Tradicional:
Texto → Quest UI → Jogador lê

Witcher 3:
Texto → Voice acting → Cinematic camera
     ↓
     Animation scripting (mocap de atores)
     ↓
     Real-time rendering com cutscene quality
     ↓
     NPC react dinamicamente (IA behavior)
```

### Sistema de Diálogo Avançado

```cpp
// Dialogue system com consequências
public class DialogueSystem {
    public enum Consequence {
        QuestProgression,
        RelationshipChange,
        ItemGain,
        ItemLoss,
        MortalityEvent  // Morte consequência
    }
    
    public class DialogueChoice {
        public string text;
        public List<Consequence> consequences;
        public bool isAvailable;  // Based on flags
    }
    
    public void SelectOption(DialogueChoice choice) {
        foreach (var consequence in choice.consequences) {
            ApplyConsequence(consequence);
        }
    }
}
```

---

## Desenvolvimento de Motor Proprietário: Worth It?

### Vantagens de REDengine

✅ **Otimizações específicas do jogo**
- Cada aspecto é otimizado para RPG
- Sem features inúteis

✅ **Longo prazo: Reutilização**
- REDengine usada em Cyberpunk 2077
- Investimento payoff multiplicado

✅ **Identidade única**
- Assinatura visual distintiva
- Difícil de copiar

### Desvantagens e Custos

❌ **100+ pessoas apenas em engine**
- Custo massivo
- Risco alto (sem fallback)

❌ **Sem comunidade externa**
- Menos feedback
- Menos inovação vinda de fora

❌ **Time divido**
- Pessoal em engine = não está em gameplay
- Possível ralentamento de conteúdo

### Modelo de Negócio

```
Investimento total (4 anos):
- Engine: 100 pessoas-ano = $5-8M
- Gameplay: 300 pessoas-ano = $15-25M
- Total: $20-33M

Receita (40M cópias @ $40 avg):
- $1.6 bilhões bruto
- Após marketing/distribution: $800M+

ROI: 25-40x

Conclusão: Worth it (assumindo sucesso comercial)
```

---

## Legado e Futuro

### Cyberpunk 2077

CD Projekt Red reutilizou REDengine para Cyberpunk 2077 (2020):

```
Melhorias:
✓ Ray tracing nativo
✓ Better streaming (next-gen consoles)
✓ Enhanced AI (NPCs more believable)
✗ Launch issues (optimization needed for consoles)
```

### Próximos Projetos

```
Unconfirmed:
- The Witcher 4 possível em REDengine 4
- Outro projeto sci-fi em desenvolvimento
- Possível suporte a Unreal Engine no futuro (licensing deal com Epic)
```

---

## Lições Sobre Motores Proprietários

### 1. Viável APENAS com Budget Grande

REDengine = sucesso para CD Projekt (40M+ vendas), mas seria desastre para indie (não recupera investimento).

### 2. Tempo de Payoff Longo

4 anos para ROI positivo. Muitos estúdios não sobrevivem.

### 3. Expertise Necessário

Requer A-team de engenheiros. Compromisso inexcusável.

### 4. Modern Alternative: Unreal Engine 4/5

Em 2024, mesmo AAA studios usam Unreal por:
- Tecnologia similarmente poderosa
- Menos overhead de desenvolvimento
- Melhor ROI

---

## Referências

CD PROJEKT RED. **The Witcher 3: Wild Hunt Official Website**. Disponível em: <https://thewitcher.com>. Acesso em: 2025.

GREGORY, J. **Game Engine Architecture**. 3. ed. CRC Press, 2018.

EPIC GAMES. **Unreal Engine Case Study**. Disponível em: <https://www.unrealengine.com>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Próximo:** Comparativo_Geral.md

