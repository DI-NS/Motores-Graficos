# 04 - Simulação de Física em Game Engines

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Sumário
1. [Introdução à Física em Games](#introdução-à-física-em-games)
2. [Motores de Física Integrados](#motores-de-física-integrados)
3. [Corpos Rígidos (Rigid Bodies)](#corpos-rígidos-rigid-bodies)
4. [Detecção de Colisão](#detecção-de-colisão)
5. [Resposta de Colisão](#resposta-de-colisão)
6. [Simulações Avançadas](#simulações-avançadas)
7. [Otimizações](#otimizações)
8. [Referências](#referências)

---

## Introdução à Física em Games

### Por que Física em Games?

A **física** cria **imersão e realismo**. Objetos que caem, colidem, ricocheteiam e reagem de forma previsível tornam o mundo do jogo mais crível.

### Objetivo do Motor de Física

Simular **leis da física clássica** em tempo real:
- Movimento (cinemática)
- Forças (dinâmica)
- Colisões (impulso, fricção)
- Corpos deformáveis (tecido, água)

### Trade-off: Precisão vs Performance

**Simulação Precisa:**
- Muitos pontos de teste
- Pequeno timestep (ex: 1/120 segundos)
- Resultado: Lento

**Simulação Rápida:**
- Poucos pontos de teste
- Grande timestep (ex: 1/30 segundos)
- Resultado: Pode "passar através" de objetos

---

## Motores de Física Integrados

### NVIDIA PhysX

**Características:**
- ✅ Amplamente usada em games AAA
- ✅ Suporte CUDA (aceleração GPU)
- ✅ Cloth simulation, particles, fluids
- ❌ Code proprietário (agora aberto em alguns casos)

**Usado em:**
- Unreal Engine (padrão)
- Nvidia's PhysX SDK (standalone)
- Muitos games indie/AAA

### Havok Physics

**Características:**
- ✅ Muito performática
- ✅ Ótima detecção de colisão
- ✅ Licensiamento profissional
- ❌ Cara

**Usado em:**
- Xbox One
- Alguns games AAA
- Aplicações industriais

### Bullet Physics

**Características:**
- ✅ Open-source (Zlib license)
- ✅ Multi-plataforma
- ✅ Bem documentada
- ❌ Menos otimizada que PhysX/Havok

**Usado em:**
- Godot Engine (padrão)
- Blender
- Muitos engines indie

### Rapier (Rust)

**Características:**
- ✅ Moderna, escrita em Rust
- ✅ Excelente para desenvolvimento Web (WASM)
- ✅ Open-source
- ✅ Crescendo em popularidade

---

## Corpos Rígidos (Rigid Bodies)

### O que é um Corpo Rígido?

Um **corpo rígido** é um objeto que **não deforma** e segue as leis da física clássica.

### Propriedades de um Corpo Rígido

```cpp
struct RigidBody {
    // Transformação
    Vector3 position;
    Quaternion rotation;
    
    // Movimento Linear
    Vector3 velocity;
    Vector3 acceleration;
    Vector3 force;
    
    // Movimento Rotacional
    Vector3 angularVelocity;
    Vector3 angularAcceleration;
    Vector3 torque;
    
    // Propriedades de Material
    float mass;
    float inertia;  // Resistência à rotação
    float drag;     // Resistência do ar
    float angularDrag;
    
    // Propriedades de Superfície
    float friction;  // Atrito
    float restitution;  // Elasticidade (0=absorve, 1=rebote perfeito)
    
    // Flags
    bool isKinematic;  // Movimento controlado manualmente
    bool useGravity;
};
```

### Estados de um Corpo Rígido

```
Dinâmico:    Afetado por forças, simula física
Cinemático:  Controlado por código, não afeta outros corpos
Estático:    Imóvel, não usa recursos (parede, chão)
```

### Equações de Movimento (Física de Verlet)

```
Integração de velocidade (Euler integrator):
v_novo = v_atual + (F/m) * dt
pos_novo = pos_atual + v_novo * dt

Onde:
F = força total aplicada
m = massa
dt = delta time
```

---

## Detecção de Colisão

### Algoritmo de Detecção de Colisão

A detecção de colisão típica ocorre em **duas fases**:

#### Fase 1: Broad Phase (Rápida)

Usa estruturas espaciais para descartar pares óbvios de não-colisão:

**Techniques:**
- **AABB (Axis-Aligned Bounding Box):** Caixas simples
- **Octree/Quadtree:** Divisão espacial hierárquica
- **Sweep & Prune:** Ordenação espacial

```
Exemplo:
Mundo dividido em 8 octantes
- Apenas testar colisões dentro do mesmo octante
- Reduz testes de N² para N/8 (muito mais rápido!)
```

#### Fase 2: Narrow Phase (Precisa)

Testa precisamente colisão entre pares identificados:

**Técnicas:**
- **GJK (Gilbert-Johnson-Keerthi):** Polígonos convexos
- **SAT (Separating Axis Theorem):** Verificar overlaps em cada eixo
- **Raycasting:** Teste de raio para objetos que se movem rápido

### Formas de Colisão

```
Esfera:     Rápida, mas imprecisa
Cápsula:    Cilindro com hemisférios (humanoides)
Caixa:      AABB ou OBB (rotacionada)
Malha:      Lenta, mas precisa (terreno complexo)
Composta:   Múltiplas formas simples (personagem = cápsula + braços)
```

---

## Resposta de Colisão

### O que Acontece Quando Dois Corpos Colidem?

1. **Detectar colisão** → Sim, objetos se sobrepõem
2. **Calcular impulso** → Com que força se separam?
3. **Aplicar impulso** → Transferir momentum

### Cálculo de Impulso (Colisão Elástica)

```
Impulso (J) = -(1 + e) * (v_relativa · normal) / (1/m1 + 1/m2)

Onde:
e = coeficiente de restituição (0=macio, 1=elástico)
v_relativa = velocidade relativa entre corpos
normal = direção de colisão
m1, m2 = massas dos corpos
```

### Aplicação de Impulso

```cpp
// Após calcular J (impulso)
body1.velocity += (J / body1.mass) * normal;
body2.velocity -= (J / body2.mass) * normal;

// Resultado: Corpos se separaram e se movem realista
```

### Fricção

```
Força de fricção = coeficiente_fricção * força_normal

Estática: Objeto não se move se força < fricção_estática
Cinética: Objeto desliza com fricção_cinética (< estática)
```

### Restituição (Elasticidade)

```
Coef. Restituição | Comportamento
0.0               | Bola de borracha pesada (absorve impacto)
0.5               | Bola de borracha normal
1.0               | Bola de pingue-pongue (rebote perfeito)
1.5+              | Fisicamente impossível (ganha energia)
```

---

## Simulações Avançadas

### 1. Simulação de Tecido

Simula roupas, bandeiras, cortinas que se movem realisticamente.

**Abordagem:**
- Modelo de partículas conectadas com constraints
- Aplicar forças (vento, gravidade)
- Solucionar constraints a cada frame

**Exemplo:** Uma capa com 1000 vértices
- Cada vértice é uma partícula
- Restrições mantêm distância entre vértices próximos
- Simulação suave e realista

### 2. Simulação de Fluidos

Simula água, fumaça, fogo em tempo real.

**Técnicas:**
- **SPH (Smoothed Particle Hydrodynamics):** Muitas partículas pequenas
- **Grid-based:** Divide espaço em células (Euler grid)
- **Hybrid:** Combina ambas

**Uso prático:**
- Água em rios/lagos (The Witcher 3)
- Fumaça/fogo dinâmico (Unreal Engine 5)
- Sangue, chuva, neve

### 3. Rope e Cable Simulation

Simula cordas, cabos, correntes flexíveis.

**Implementação:**
- Série de corpos rígidos conectados
- Constraints para distância máxima entre elos
- Iteração para solucionar constraints

### 4. Ragdoll Physics

Simula corpo humano sendo derrubado (desativação de animação).

**Como funciona:**
1. Personagem morre ou é derrubado
2. Anima normal para → ragdoll (physics)
3. Múltiplos corpos rígidos (torso, braço, perna, etc.)
4. Articulações (joints) conectam os corpos
5. Física toma conta (o corpo "cai" realisticamente)

---

## Otimizações

### 1. Sleep/Wake States

Corpos estáticos não precisam ser simulados:

```cpp
if (body.velocity < threshold && body.angularVelocity < threshold) {
    body.Sleep();  // Parar de simular
}

if (collision_detected) {
    body.WakeUp();  // Reativar se colidir
}
```

**Impacto:** 50% redução de cálculos em cenas estáticas

### 2. Timestep Fixo (Fixed Timestep)

```cpp
const float PHYSICS_DT = 1/60.0f;  // 60 FPS fixo para física
float accumulator = 0;

while (true) {
    accumulator += deltaTime;
    
    while (accumulator >= PHYSICS_DT) {
        PhysicsStep(PHYSICS_DT);
        accumulator -= PHYSICS_DT;
    }
}
```

**Benefício:** Determinístico e estável

### 3. Discrete vs Continuous Collision Detection

**Discrete:** Teste colisão a cada frame (rápido, pode perder colisões)
**Continuous:** Teste ao longo do movimento (preciso, lento)

```cpp
// Para objetos rápidos (balas, etc)
body.SetContinuousCollisionDetection(true);

// Para objetos lentos
body.SetContinuousCollisionDetection(false);
```

### 4. Simplificação de Geometria

- Use cápsulas em vez de meshes completos para humanoides
- Decomponha meshes complexas em formas simples
- Use terreno simplificado para physics (vs render mesh)

---

## Referências

SMEDSTAD, F. **Real-Time Collision Detection and Physics Simulation in Game Engines**. GDC Vault, 2019.

NVIDIA. **PhysX: Open Source Physics Engine Documentation**. Disponível em: <https://developer.nvidia.com/physx-sdk>. Acesso em: 2025.

GREGORY, J. **Game Engine Architecture**. 3. ed. CRC Press, 2018.

MÖLLER, T.; HAINES, E.; HOFFMAN, N. **Real-Time Rendering**. 4. ed. CRC Press, 2018.

---

**Última atualização:** Outubro de 2025  
**Próximo arquivo:** 05_Visual_Scripting_Blueprints.md

