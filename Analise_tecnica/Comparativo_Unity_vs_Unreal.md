# Comparativo: Unity vs Unreal Engine

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Resumo Executivo

| Aspecto | Unity | Unreal Engine |
|--------|-------|---------------|
| **Curva de Aprendizado** | Fácil | Médio-Difícil |
| **Performance** | Boa | Excelente |
| **Visual Scripting** | Básico | Blueprints (excelente) |
| **Mobile Support** | Nativo | Experimental |
| **Preço** | Gratuito até $100k | Gratuito até $1M |
| **Comunidade** | Enorme | Grande |

---

## Comparação Técnica Detalhada

### 1. Linguagem de Programação

**Unity: C#**
```csharp
using UnityEngine;

public class Player : MonoBehaviour {
    public float speed = 5f;
    
    void Update() {
        float moveX = Input.GetAxis("Horizontal");
        transform.position += Vector3.right * moveX * speed * Time.deltaTime;
    }
}
```

Vantagens:
✓ Sintaxe familiar (Java-like)
✓ Type-safe
✓ Garbage collection automático

Desvantagens:
❌ GC stutters ocasionais
❌ Performance inferior a C++ em loops críticos

**Unreal: C++ + Blueprints**
```cpp
UCLASS()
class GAME_API APlayer : public ACharacter {
    GENERATED_BODY()
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float Speed = 5.0f;
    
    virtual void BeginPlay() override;
    virtual void Tick(float DeltaTime) override;
};

void APlayer::Tick(float DeltaTime) {
    float MoveX = GetInputAxisValue("Horizontal");
    AddMovementInput(GetActorRightVector(), MoveX);
}
```

Vantagens:
✓ Performance máxima (C++ nativo)
✓ Blueprints para designers
✓ Controle fino da memória

Desvantagens:
❌ Sintaxe C++ complexa
❌ Recompilação demorada
❌ Curva de aprendizado íngreme

---

### 2. Renderização: Forward vs Deferred

**Unity (Forward Rendering)**
```
Vantagem:
- Mais simples para game indie
- Melhor com poucas luzes
- Menos overdraw

Desvantagem:
- Muitas luzes = lag
- ~100 luzes = impraticável
```

**Unreal (Deferred Rendering)**
```
Vantagem:
- Suporta 1000+ luzes interativas
- Performance escalável
- Ideal para AAA

Desvantagem:
- Complexo em shader writing
- MSAA mais difícil
- Mais memória (G-buffer)
```

---

### 3. Visual Scripting

**Unity: Node-Based Visual Scripting**
```
State:
- Recente (Unity 2021+)
- Competente, mas não revolucionário
- Comunidade: "Use C# ao invés"
```

**Unreal: Blueprints**
```
State:
- Mature (UE3+)
- Profissional (Fortnite a usa massivamente)
- Community: "Use Blueprints para gameplay, C++ para systems"

Exemplo Blueprint:
[Event BeginPlay] → [Print String "Hello"] → [Draw Debug Box]
```

**Vencedor: Unreal Blueprints**
- Mais polida
- Comunidade forte
- Comprovado em produção AAA

---

### 4. Performance: Benchmarks Teóricos

```
Test: Renderizar 10,000 espheras animadas
Target: 60 FPS

Unity (em máquina media):
- Base C# code: 25 FPS
- Com optimization (Burst compiler): 40 FPS
- Com GPU instancing: 50 FPS

Unreal (mesma máquina):
- C++ base: 50 FPS
- C++ + optimization: 55 FPS
- + GPU instancing: 58 FPS

Vencedor: Unreal (máximo performance)
```

---

### 5. Mobile Development

**Unity**
```
✓ First-class mobile support
✓ iOS + Android nativo
✓ 500+ games em App Store TOP 100
✓ Tooling simples

Exemplo: Angry Birds, Pokémon GO, Crossy Road
```

**Unreal**
```
❌ Mobile suporte experimental
❌ Não é prioritário
❌ Builds muito grandes (~500MB+)
✓ Possível, mas não recomendado

Status: "Use Unity para mobile"
```

**Vencedor: Unity** (por 10 pontos)

---

### 6. Asset Store vs Marketplace

**Unity Asset Store**
```
Conteúdo: 50,000+ assets
Qualidade: Variável (bom + lixo)
Preço: $0-$100+
Comunidade: Enorme, muita escolha
```

**Unreal Marketplace**
```
Conteúdo: 5,000+ assets
Qualidade: Mais controlada (Epic veta)
Preço: $0-$100+
Comunidade: Seleta, menos opção
```

**Vencedor: Unity** (mais opções)

---

### 7. Scalabilidade Técnica

**Indie Small Game (1-3 pessoas)**
```
Recomendação: Unity
- Curva de aprendizado mais suave
- Menos overhead
- Community support melhor
```

**Medium Game (10-50 pessoas)**
```
Recomendação: Unity (mobile) ou Unreal (AAA vision)
- Ambos viáveis
- Choose based on: plataforma target, estilo art
```

**AAA Game (100+ pessoas)**
```
Recomendação: Unreal Engine
- Performance crítica
- Blueprints + C++ workflow
- Suporte profissional
- Comunidade AAA-focused
```

---

### 8. Workflow de Colaboração

**Unity**
```
Versionamento Git:
- YAML scene format (bom)
- Merge conflicts ainda frequentes
- Prefabs complicados em equipe

Recomendação: Usar PlasticSCM ou Perforce (pago)
```

**Unreal**
```
Versionamento:
- Binary-based (difícil com Git)
- Perforce integrado (profissional)
- Replicação automática

Recomendação: Use Perforce (pago) ou Plastic SCM
```

---

## Análise de Trade-offs

### Por que escolher Unity?

✅ **Prototipação rápida** - Iteração em horas
✅ **Mobile nativo** - iPhone/Android first-class
✅ **Indie-friendly** - Gratuito, comunidade larga
✅ **2D excelente** - Unity 2D é muito bom
✅ **Portátil** - Mesmo código → múltiplas plataformas

### Por que escolher Unreal?

✅ **Performance máxima** - C++ nativo
✅ **AAA-grade** - Infraestrutura para grandes equipes
✅ **Blueprints poderosos** - Visual scripting profissional
✅ **Gráficos avançados** - Nanite, Lumen, ray tracing
✅ **Suporte console** - PlayStation, Xbox nativo
✅ **Rede multiplayer** - Replication system robusto

---

## Tendências (2024-2025)

### Unity

```
Mudanças recentes:
- DOTS (Data-Oriented Tech Stack) = performance extrema
- Sentient AI (generative content)
- Shader Graph melhorado

Futuro: Concorrer com Unreal em performance
```

### Unreal

```
Mudanças recentes:
- Unreal Engine 5 = Nanite + Lumen
- Blueprint melhorado
- Python scripting adicionado

Futuro: Democratizar com melhor documentação
```

---

## Recomendação Final

| Cenário | Engine | Razão |
|---------|--------|-------|
| Indie 2D | Unity | 2D superior |
| Indie 3D | Godot | Open-source, fácil |
| Mobile | Unity | Suporte nativo |
| AAA PC/Console | Unreal | Performance, suporte |
| Multiplayer Online | Unreal | Replication maturo |
| Educational | Godot | Free, open-source |
| Produção Virtual (cinema) | Unreal | Real-time rendering |

---

**Última atualização:** Outubro de 2025

