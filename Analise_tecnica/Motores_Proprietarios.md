# Motores Proprietários: Quando Vale a Pena?

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## O que é um Motor Proprietário?

Um **motor proprietário** é um engine desenvolvido internamente por um estúdio, específico para suas necessidades.

---

## Exemplos Notáveis

### 1. REDengine (CD Projekt Red)

```
Usado: The Witcher 3, Cyberpunk 2077
Status: Desenvolvido internamente desde 2007
Longevidade: 15+ anos, ainda em evolução

Características:
✓ Narrativa-first
✓ Open-world otimizado
✓ Fotorrealismo
```

### 2. Source Engine (Valve)

```
Usado: Half-Life 2, Portal, Left 4 Dead
Status: Modificação de Quake engine, depois proprietária
Longevidade: 20+ anos (ainda suportado)

Características:
✓ Physics-based gameplay
✓ Modding-friendly
✓ Ótimo para narrativa
```

### 3. RAGE (Rockstar Games)

```
Usado: GTA IV, GTA V, Red Dead Redemption
Status: Desenvolvido internamente
Longevidade: 15+ anos

Características:
✓ Open-world massive
✓ Física complexa
✓ Otimizado para Rockstar's vision
```

### 4. Decima Engine (Guerrilla Games)

```
Usado: Killzone 2+, Horizon Zero Dawn, God of War (parcialmente)
Status: Desenvolvido internamente
Longevidade: 10+ anos

Características:
✓ Combat dynamics
✓ AI sofisticada
✓ Mundo expansivo
```

### 5. Unidyne (Naughty Dog)

```
Usado: The Last of Us, Uncharted, The Last of Us Part II
Status: Desenvolvido internamente
Longevidade: 10+ anos

Características:
✓ Cinematografia integrada
✓ AI comportamental
✓ Otimizado para PlayStation
```

---

## Decisão: Desenvolver Motor Proprietário

### Quando Vale a Pena?

#### ✅ Condições Ideais:

```
1. Budget: $10M-$100M+
   - Apenas AAA pode se dar ao luxo

2. Equipe experiente: 50-100+ engenheiros de engine
   - Expertise em arquitetura de software
   - Conhecimento profundo de hardware alvo

3. Escala de projeto: Múltiplos games, 10+ anos
   - REDengine: 3+ games (The Witcher 2, 3, Cyberpunk)
   - Source: 5+ games (HL2, Portal, L4D, etc)

4. Visão clara: Diferenciação específica
   - Gameplay único que engines comerciais não suportam
   - Exemplo: Narrativa em The Witcher, física em Source

5. Longevidade: Compromisso de 5-10 anos
   - Motor não é produto, é infraestrutura
   - ROI realizado ao longo de tempo
```

#### ❌ Quando Não Vale:

```
❌ Budget baixo (< $10M)
❌ Timeline curta (< 3 anos)
❌ Primeira vez fazendo engine (risco extremo)
❌ Escopo único (1 jogo)
❌ Skill não está em engine dev (vão se afogar)
```

---

## Análise de Custo vs Benefício

### Custo de Desenvolvimento

```
Cenário: Desenvolver motor AAA-quality em 3 anos

Pessoal:
- Engine architects (5): $2M/ano = $10M
- Graphics engineers (10): $1M/ano = $30M
- Physics engineers (5): $500K/ano = $7.5M
- Tools engineers (10): $800K/ano = $24M
- QA/Support (10): $600K/ano = $18M
- Management (5): $1M/ano = $15M

Subtotal pessoal: ~$104.5M

Infrastructure:
- Servidores/workstations: $5M
- Softwares/licenses: $2M
- Testes/CI-CD: $3M

Total: ~$115M para engine base
```

### Receita Esperada

```
Cenário: Game AAA usando motor customizado

Sales targets:
- 10M+ copies @ $60 avg = $600M
- Menos marketing (30%), distribuição (20%): $240M

Royalties economizados:
- Unreal: 5% = $30M
- Licensing alternativo: $10M+

Total benefício direto: ~$280M

Payoff: 2.4x ROI
```

**Conclusão: Matematicamente viável para AAA, mas arriscado**

---

## Vantagens de Motor Proprietário

### 1. Controle Absoluto

```
- Nenhuma limitação do engine
- Customização sem limites
- Otimizações específicas do jogo
- "Assinatura tecnológica" única
```

### 2. Sem Royalties

```
Exemplo financeiro:
- 10M copies @ $60 = $600M receita
- Unreal 5%: $30M em royalties
- REDengine: $0 em royalties

Economia: $30M que vira profit
```

### 3. Independência Estratégica

```
Unreal atualiza, break compat: problema
REDengine: Seu controle

Exemplo real:
- Godot 4 mudou Vulkan, projects quebraram
- REDengine atualiza no seu ritmo
```

### 4. Intellectual Property

```
Motor é propriedade do estúdio
Pode licenciar, vender, ou transferir
Valor duradouro (licensing: $$$)
```

---

## Desvantagens e Riscos

### 1. Desenvolvimento Longo

```
Unreal Engine 3 → Engine profissional: ~2-3 anos
REDengine 3 → The Witcher 3: ~4 anos
Cyberpunk 2077 → REDengine melhorado: ~8 anos

Risco: Atraso = prejuízo massivo
```

### 2. Talent Hiring Difícil

```
Procurar: "Engine programmer experiente em Graphics"
Mercado: Muitos aplicam
Procurar: "Engine programmer experiente em REDengine"
Mercado: Ninguém (especifico demais)

Resultado: Treinar internamente = demorado
```

### 3. Bugs e Crashes

```
Motor comercial: Epic tem 300+ engenheiros testando
Motor proprietário: Seu time testando

Real example:
Cyberpunk 2077 lançou com bugs graves = responsabilidade do estúdio
Unreal tinha sido used para 1000+ games antes de UE5 lançar

Risco: Bugs críticos descobertos tarde custam caro
```

### 4. Oportunidade Perdida

```
Cenário pessimista:
- Gastou $100M em engine
- Game não vende bem (10K copies, não 10M)
- Prejuízo de $100M+ (irrecuperável)

Cenário alternativo:
- Usar Unreal $0 + contrate 50 gamedevs
- Mesmo game, mas com mais tempo em gameplay
- Resultado similar com menos risco
```

---

## Trade-offs: Engine Proprietária vs Comercial

| Fator | Proprietária | Comercial |
|-------|-------------|-----------|
| **Performance** | Máxima (se bem feito) | 90-95% máxima |
| **Customização** | Ilimitada | Limitada |
| **Tempo Development** | +12-24 meses | -12-24 meses |
| **Risk** | Extremo | Moderado |
| **Royalties** | $0 | 3-5% |
| **Suporte** | Interno | Externo (Epic, UE team) |
| **Talent** | Fácil (genérico) | Difícil (especializado) |
| **Iteração** | Lenta (recompilação) | Rápida (Blueprints) |

---

## Tendência Atual (2024-2025)

### Menos Motores Proprietários

```
Razão 1: Unreal Engine 5 tão poderosa que deixa menos valor agregado

Razão 2: Assets e plugins comerciais cobrem 90% das necessidades

Razão 3: Custo de desenvolvimento extremamente alto

Resultado: Apenas mega-estúdios (Rockstar, Naughty Dog) justificam investimento
```

### Exceção: AI-Integrated Engines

```
Novo nicho emergente:
- Engines otimizadas para procedural content (IA)
- Potencial ROI em novo tipo de game

Exemplo:
- Decentraland engine (metaverse)
- Roblox engine (user-generated content)
```

---

## Quando Motoristas Proprietários Fizeram Diferença

### The Witcher 3 (REDengine)

```
✓ Narrativa integrada perfeitamente
✓ Open-world com 800+ NPCs
✓ Customização de hardware-specific otimizações

Would have been:
- Possível com Unreal, mas mais lento
- Menor controle visual
- Menos otimização de storage
```

### Red Dead Redemption 2 (RAGE)

```
✓ Mundo 100km² seamless
✓ Física realista (cabelo, roupa, dinâmica do animal)
✓ Otimização para console perfeita

Diferença:
- Unreal teria struggle com mundo seamless
- Performance não seria garantida em PS4
```

### Half-Life 2 (Source)

```
✓ Gameplay inovativo (Gravity Gun, mechanics únicos)
✓ Física totalmente integrada na gameplay
✓ Flexibilidade de modding

Diferença:
- Unreal (na época) não suportava level design com physics-based gameplay
- Source foi perfeit fit para visão de Valve
```

---

## Recomendação Final

### Desenvolver Motor Proprietário Se:

```
✓ Budget: $100M+
✓ Timeline: 10+ anos (múltiplos games)
✓ Visão: Gameplay/tech completamente única
✓ Team: 50+ engine specialists
✓ Company: Já tem track record de sucesso
```

### NÃO Desenvolver Se:

```
❌ Budget: < $20M
❌ Primeiro jogo
❌ Gameplay é variation de gênero conhecido
❌ Timeline curta
❌ Team não tem expertise em engine dev
```

### Alternativa: Hybrid

```
- Forcar em Unreal Engine
- Customize Blueprints / C++ extensions
- Resultado: 95% customização com 50% menos risco
- Exemplo: MiHoYo (Genshin Impact customiza Unity massivamente)
```

---

**Última atualização:** Outubro de 2025

