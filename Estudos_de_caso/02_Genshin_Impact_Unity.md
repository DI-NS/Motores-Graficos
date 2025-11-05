# 02 - Genshin Impact: RPG Mobile AAA com Unity

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Resumo Executivo

| Aspecto | Informação |
|--------|-----------|
| **Título** | Genshin Impact |
| **Desenvolvedora** | MiHoYo (China) |
| **Tamanho do Estúdio** | ~600+ pessoas |
| **Data de Lançamento** | 28 de setembro de 2020 |
| **Plataformas** | PC, Mobile (iOS/Android), PlayStation, Nintendo Switch |
| **Engine** | Unity 3D customizada |
| **Receita** | $3.7 bilhões (2023) |
| **Usuários** | 50+ milhões de downloads |

---

## O Fenômeno Genshin Impact

### Contexto de Lançamento (2020)

**Cenário:**
- Mobile gaming em explosão
- Fortnite dominava consoles
- Ninguém havia feito RPG mobile com gráficos de console

**Genshin Impact:**
- Gráficos anime estilo Zelda: Breath of the Wild
- 60 FPS em Mobile (extremamente raro em 2020)
- Open world massivo (exploração livre)
- Monetização gacha responsável

**Impacto:** Mudou paradigma do que era possível em mobile

---

## Decisão Arquitetural: Por que Unity?

### Requisitos Técnicos Únicos

```
DESAFIO:           REQUISITO:
4K Console    →    Render 4K @ 60 FPS
1080p Mobile  →    Render 1080p @ 60 FPS
Mundo aberto  →    70 km² exploráveis
100+ NPCs      →    Gerenciar simultaneously
Multiplayer    →    Drop-in/drop-out coop
```

### Por que Unity e não Unreal?

**Decisão de Negócio:**
```
Unity:
✓ Melhor suporte mobile
✓ Menor tamanho de build (~2GB vs 5GB)
✓ Mais fácil portar para múltiplas plataformas
✓ Comunidade mobile maior

Unreal:
✗ Focado em console/PC
✗ Builds enormes (> 5GB)
✗ Mobile suporte experimental em 2020
```

**Resultado:** Unity foi escolhida, porém **customizada drasticamente**

---

## Arquitetura Técnica Customizada

### Modificações do Engine

MiHoYo não usou Unity "vanilla". Fazem customizações profundas:

#### 1. Renderizador Customizado

```
Padrão Unity:      Deferred rendering (múltiplas luzes = lag)
Genshin Custom:    Forward rendering + otimizações próprias
Resultado:         Suporta 100+ luzes interativas sem lag
```

#### 2. LOD (Level of Detail) Agressivo

```csharp
// Detecção de distância customizada
public class DynamicLOD : MonoBehaviour {
    public float[] lod_distances = {0, 50, 100, 200};
    
    void Update() {
        float distance = Vector3.Distance(transform.position, Camera.main.transform.position);
        
        if (distance < 50) {
            SetQuality(100);  // Ultra: 100% polígonos
        } else if (distance < 100) {
            SetQuality(50);   // High: 50% polígonos
        } else if (distance < 200) {
            SetQuality(25);   // Medium: 25% polígonos
        } else {
            SetQuality(10);   // Low: 10% polígonos
        }
    }
}
```

#### 3. Async Loading

```csharp
// Carregar mundo sem congelar
void LoadRegionAsync(string regionName) {
    StartCoroutine(LoadAsyncCoroutine(regionName));
}

IEnumerator LoadAsyncCoroutine(string regionName) {
    AsyncOperation operation = SceneManager.LoadSceneAsync(regionName);
    
    while (!operation.isDone) {
        progressBar.value = operation.progress;
        yield return null;  // Não bloqueia frame
    }
    
    OnRegionLoaded();
}
```

### Otimizações de Performance

#### Mobile-First Optimization

```
Target: iPhone XS (2018) = baseline

Graphics settings tiered:
- Ultra (PS5): 4K, ray tracing (teórico), todas luzes
- High (iPhone 12+): 1440p, sombras dinâmicas
- Medium (iPhone X): 1080p, sombras simplifadas  
- Low (iPhone 8): 720p, sem sombras reais
```

#### Physics Culling

```csharp
// Simular física apenas de objetos próximos
void PhysicsUpdate() {
    Vector3 playerPos = player.transform.position;
    
    foreach (Rigidbody rb in allRigidbodies) {
        float distance = Vector3.Distance(rb.transform.position, playerPos);
        
        if (distance < 100) {
            rb.isKinematic = false;  // Simular
        } else {
            rb.isKinematic = true;   // Hibernar
        }
    }
}
```

### Sistema de Rede Customizado

```
Problema: Unity's networking é simples, Genshin precisa de:
- 4 players simultâneos
- Sincronização de posição e estado
- Latência ~100ms é aceitável
- Servidores MiHoYo

Solução: Protocolo customizado
- TCP para ações críticas (combat)
- UDP para movimento (tolerante a perda)
- Client-side prediction para suavidade
```

---

## Desafios Técnicos Resolvidos

### 1. Gacha System Performance

**Problema:** Milhões de puxadas de gacha = servidores sobrecarregados

**Solução:**
```csharp
// Client-side RNG determinístico
// Seed = ID do usuário + timestamp
// Servidor valida apenas resultado final
// Elimina overhead de N chamadas de API
```

### 2. World Event Synchronization

**Problema:** Eventos globais (festa anual) afetam 50M players

**Solução:**
```
Arquitetura distribuída:
- Sharding por servidor regional (CN, Global, etc)
- Cache distribuído para estado do mundo
- Replicação eventual consistency
- Tolera pequenas diferenças entre regiões
```

### 3. Exploração Sem Limite

**Problema:** Mundo open-world 70km² = LOD impossível?

**Solução:**
```
Streaming de mundo:
- Mapa dividido em 100m x 100m chunks
- Carrega chunks próximos assincronamente  
- Descarrega chunks distantes
- Transição perfeita sem cutscenes
```

---

## Monetização e Engine

### Modelo Gacha

Genshin Impact usa **"gacha"** (loot box aleatória):
- Jogar grátis, personagens por gacha
- ~$10 por 10x puxada
- Receita: $3.7 bilhões em 2023

### Por que Funciona com Unity?

```
1. Fácil de atualizar (conteúdo novo a cada 6 semanas)
2. Mobile = acessível globalmente
3. Performance estável = confiança do jogador
4. Cross-platform = mais mercado
```

---

## Impacto na Indústria

### Antes de Genshin Impact

```
❌ "Mobile games não podem ter gráficos AAA"
❌ "Open world mobile = impossível"
❌ "60 FPS mobile = só para flagships"
```

### Depois de Genshin Impact

```
✅ AAA-quality mobile é esperado
✅ Open world é common practice
✅ 60 FPS é baseline em 2024
```

### Jogos Inspirados

- Honkai: Star Rail (mesmo dev, diferente gameplay)
- Zenless Zone Zero (novo jogo MiHoYo)
- Tower of Fantasy (cópia direta do conceito)
- Project Lazarus (suposto "Genshin Killer")

---

## Lições Técnicas

### 1. Engine Choice Não é Absoluto

"Unity é para indie" = FALSO

MiHoYo provou que com customizações, Unity pode fazer AAA mobile.

### 2. Otimização é Iterativa

- Dia 1: Features
- Iteração 1-10: Performance
- Cada console/device exige tweaks
- Não é "set and forget"

### 3. Server-side é Crítico

```
Client-side:  Gráficos, gameplay feel, responsividade
Server-side:  Autenticação, progression, economia, anti-cheat

Genshin deve manter servidor mesmo com erro de cliente
```

### 4. Community Management

```
Genshin é jogo contínuo:
- Evento novo a cada 6 semanas
- Personagem novo cada patch (6 semanas)
- Comunicação constante com comunidade
- Feedback → Mudanças rápidas
```

---

## Referências

MIHOYO. **Genshin Impact Official Website**. Disponível em: <https://genshin.mihoyo.com>. Acesso em: 2025.

LORENZEN, C. **The Story of the Genshin Impact Engine: Unity's Path to AAA Gaming**. GDC 2022 Talk, 2022.

UNITY TECHNOLOGIES. **Case Study: Genshin Impact**. Disponível em: <https://unity.com/case-studies/mihoyo>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Próximo:** 03_Fortnite_Unreal_Engine.md

