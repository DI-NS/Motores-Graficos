# 03 - Entity-Component-System (ECS)

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Sumário
1. [Introdução ao ECS](#introdução-ao-ecs)
2. [Paradigma OOP vs ECS](#paradigma-oop-vs-ecs)
3. [Componentes](#componentes)
4. [Entidades](#entidades)
5. [Sistemas](#sistemas)
6. [Vantagens do ECS](#vantagens-do-ecs)
7. [Implementação Prática](#implementação-prática)
8. [Referências](#referências)

---

## Introdução ao ECS

### O que é Entity-Component-System?

**ECS** é um **padrão arquitetural de software** que separa a lógica de jogo em três conceitos fundamentais:

1. **Entidades:** Objetos no mundo (jogador, inimigo, parede)
2. **Componentes:** Dados/propriedades (posição, velocidade, saúde)
3. **Sistemas:** Lógica que atua sobre componentes (movimento, colisão, IA)

### Por que ECS?

**Problema com OOP tradicional:**
```
Classe Jogador
├── Posição
├── Velocidade
├── Saúde
├── Arma
├── AI
└── Renderer (MUITA HIERARQUIA!)

Problema: Mudanças em uma hierarquia afetam toda a árvore
```

**Solução ECS:**
```
Entidade (Jogador)
├── Componente Posição (x, y, z)
├── Componente Velocidade (vx, vy, vz)
├── Componente Saúde (100 HP)
├── Componente Arma (tipo, munição)
└── Componente Renderer (modelo, textura)

Vantagem: Composição flexível sem hierarquia rígida
```

---

## Paradigma OOP vs ECS

### Programação Orientada a Objetos (OOP)

```cpp
class GameObject {
    Vector3 position;
    Vector3 velocity;
    
    virtual void Update() = 0;
    virtual void Render() = 0;
};

class Jogador : public GameObject {
    int health;
    Weapon weapon;
    
    void Update() override { /* Lógica jogador */ }
    void Render() override { /* Desenhar jogador */ }
};

class Inimigo : public GameObject {
    int health;
    AIController ai;
    
    void Update() override { /* Lógica inimigo */ }
    void Render() override { /* Desenhar inimigo */ }
};

// PROBLEMA: Hierarquia profunda, difícil de modificar
// Um inimigo que é também um projetil? Múltipla herança = complexidade
```

### Entity-Component-System (ECS)

```cpp
// Componentes = Estruturas de dados puras
struct Position {
    float x, y, z;
};

struct Velocity {
    float vx, vy, vz;
};

struct Health {
    int hp;
    int maxHp;
};

struct Weapon {
    int ammo;
    float firerate;
};

// Entidades = Apenas IDs + coleção de componentes
Entity player = world.CreateEntity();
player.AddComponent<Position>({0, 0, 0});
player.AddComponent<Velocity>({0, 0, 0});
player.AddComponent<Health>({100, 100});
player.AddComponent<Weapon>({30, 0.1f});

// Sistemas = Lógica que itera sobre componentes
class MovementSystem : System {
    void Update(float dt) {
        for (auto entity : Entities with <Position, Velocity>) {
            auto& pos = entity.Get<Position>();
            auto& vel = entity.Get<Velocity>();
            pos.x += vel.vx * dt;
            pos.y += vel.vy * dt;
            pos.z += vel.vz * dt;
        }
    }
};

// VANTAGEM: Composição flexível, reutilizável, cache-friendly
```

---

## Componentes

### O que é um Componente?

Um **componente** é uma estrutura de dados que armazena uma **parte específica do estado** de uma entidade.

### Exemplos de Componentes

#### Componentes de Transformação
```cpp
struct Transform {
    Vector3 position;
    Quaternion rotation;
    Vector3 scale;
};

struct Velocity {
    Vector3 linear;
    Vector3 angular;
};
```

#### Componentes de Gameplay
```cpp
struct Health {
    int hp;
    int maxHp;
    int armor;
};

struct Weapon {
    WeaponType type;
    int ammo;
    float cooldown;
};

struct AIController {
    AIState currentState;
    Vector3 targetPosition;
    float detectionRadius;
};
```

#### Componentes de Renderização
```cpp
struct Renderer {
    MeshHandle mesh;
    MaterialHandle material;
    Vector4 tintColor;
};

struct AnimationController {
    AnimationClipHandle currentClip;
    float playbackTime;
    bool isLooping;
};
```

### Características Ideais de Componentes

✅ **Dados puros** - Sem lógica complexa  
✅ **Independentes** - Funcionam sem outros componentes  
✅ **Pequenos** - Poucos membros (cache-friendly)  
✅ **Reutilizáveis** - Podem ser usados em muitas entidades  
❌ **Sem dependências circulares** - Um componente não deveria "possuir" outro

---

## Entidades

### O que é uma Entidade?

Uma **entidade** é um **container de componentes** - basicamente um ID único associado a um conjunto dinâmico de componentes.

### Representação Interna

```cpp
struct Entity {
    uint32_t id;  // Identificador único
    uint32_t generation;  // Para detectar entidades deletadas
};

// Internamente, o engine mantém:
struct EntityComponentData {
    uint32_t entityId;
    std::vector<ComponentHandle> components;  // Quais componentes esta entidade tem
    std::bitset<256> componentMask;  // Bitmask: qual componente está presente
};
```

### Operações Comuns com Entidades

```cpp
// Criar entidade
Entity enemy = world.CreateEntity();

// Adicionar componentes
enemy.AddComponent<Transform>({x, y, z});
enemy.AddComponent<Health>({50, 50});
enemy.AddComponent<AIController>({...});

// Acessar componentes
Transform& transform = enemy.GetComponent<Transform>();
Health& health = enemy.GetComponent<Health>();

// Remover componente
enemy.RemoveComponent<Weapon>();

// Destruir entidade
world.DestroyEntity(enemy);
```

### Arquétipo (Archetypal ECS)

Engines modernos usam **arquétipos** para agrupar entidades com o **mesmo conjunto de componentes**:

```
Arquétipo A: <Transform, Health, Renderer>
├── Jogador
├── Inimigo_Goblin
└── Inimigo_Orc

Arquétipo B: <Transform, Projectile, Velocity>
├── Flecha
├── Bala
└── Missile

Vantagem: Entidades do mesmo tipo estão próximas na memória = CPU cache hit!
```

---

## Sistemas

### O que é um Sistema?

Um **sistema** é a **lógica de jogo** que itera sobre grupos de entidades que possuem um **conjunto específico de componentes**.

### Exemplo: MovementSystem

```cpp
class MovementSystem : System {
public:
    void Update(float deltaTime) {
        // Processa todas as entidades que têm AMBOS Position e Velocity
        auto view = world.GetView<Position, Velocity>();
        
        for (auto entity : view) {
            Position& pos = entity.Get<Position>();
            Velocity& vel = entity.Get<Velocity>();
            
            // Atualizar posição
            pos.x += vel.vx * deltaTime;
            pos.y += vel.vy * deltaTime;
            pos.z += vel.vz * deltaTime;
        }
    }
};
```

### Exemplo: HealthSystem

```cpp
class HealthSystem : System {
public:
    void Update(float deltaTime) {
        auto view = world.GetView<Health, Renderer>();
        
        for (auto entity : view) {
            Health& health = entity.Get<Health>();
            Renderer& renderer = entity.Get<Renderer>();
            
            // Renderizar barra de saúde com cor baseada em HP
            if (health.hp <= 0) {
                world.DestroyEntity(entity);
            } else {
                float healthPercent = (float)health.hp / health.maxHp;
                renderer.tintColor = Vector4(1 - healthPercent, healthPercent, 0, 1);
            }
        }
    }
};
```

### Queries (Filtering Avançado)

```cpp
// Entidades com Transform E Renderer, MAS SEM Invisible
auto view = world.GetView<Transform, Renderer>().Exclude<InvisibleTag>();

// Entidades com Health OU Armor
auto view = world.GetView().With<Health>().Or<Armor>();

// Entidades sem nenhum componente (empty entities)
auto view = world.GetView<>().All();
```

---

## Vantagens do ECS

### 1. Cache Locality (Performance)

**OOP Tradicional:**
```
Memória: [Jogador obj] [Inimigo1 obj] [Inimigo2 obj] ...
         (posição + health + arma + renderer)
         
Cache miss! Dados espalhados em memória
```

**ECS:**
```
Memória: [Pos1] [Pos2] [Pos3] ... [Vel1] [Vel2] [Vel3] ...
         
Cache hit! Dados do mesmo tipo contíguos
Um único loop acessa dados sequenciais na memória = velocidade 10-100x maior
```

### 2. Composição Flexível

```cpp
// OOP: Criar classe para cada combinação
class Jogador_Com_Arma_E_AI { ... };
class Jogador_Sem_Arma_Com_AI { ... };

// ECS: Apenas adicione/remova componentes
entity.AddComponent<Weapon>();
entity.RemoveComponent<AIController>();
```

### 3. Paralelização Automática

```cpp
// Todos os MovementSystem.Update() podem rodar em paralelo
// porque não compartilham estado
MovementSystem.Update();  // CPU core 1
HealthSystem.Update();    // CPU core 2
RenderSystem.Update();    // CPU core 3
```

### 4. Código Reutilizável

```cpp
// Mesma lógica de movimento para:
// - Jogador
// - Inimigos
// - Objetos voadores
// - Projéteis

Basta ter componentes <Position, Velocity>!
```

### 5. Fácil de Debugar

```cpp
// Inspeccionar componentes de uma entidade
Entity entity = GetEntityByID(42);
auto components = entity.GetAllComponents();
// Output: Transform, Health, Weapon, Renderer, ...

// Entender por que um objeto se move:
// Procurar sistemas que processam <Position, Velocity>
// Encontrar MovementSystem
// Debugar apenas esse sistema
```

---

## Implementação Prática

### ECS em Engines Populares

#### Unreal Engine (Entity Component System)

Unreal Engine 5 introduz Archetypes e queries avançadas:

```cpp
// FArchetype define um tipo de entidade
FArchetype PlayerArchetype = Entity->GetArchetype();

// Queries eficientes
for (FEntityHandle Entity : QueryDesc.GetView(World)) {
    FTransformComponent& Transform = Entity.Get<FTransformComponent>();
    FHealthComponent& Health = Entity.Get<FHealthComponent>();
    // ...
}
```

#### Unity DOTS (Data-Oriented Tech Stack)

```csharp
// C# com sistemas que processam dados em massa
public partial class MovementSystem : ISystem {
    public void OnUpdate() {
        foreach (var (transform, velocity) in Query<Transform, Velocity>()) {
            transform.position += velocity.value * SystemAPI.Time.DeltaTime;
        }
    }
}
```

#### Godot 4

```gdscript
# Godot usa nodes (OOP-ish) mas permite patterns similares a ECS
class_name PlayerController
extends Node3D

var health_component: HealthComponent
var movement_component: MovementComponent

func _ready():
    health_component = HealthComponent.new()
    movement_component = MovementComponent.new()
    add_child(health_component)
    add_child(movement_component)
```

#### Engine Customizada em C++

```cpp
// Pseudo-código de um ECS básico

class World {
private:
    std::unordered_map<EntityID, Entity> entities;
    std::vector<System*> systems;
    
public:
    void Update(float dt) {
        for (auto system : systems) {
            system->Update(dt);  // Cada sistema processa seus componentes
        }
    }
};
```

---

## Referências

GREGORY, J. **Game Engine Architecture**. 3. ed. CRC Press, 2018.

JÄRVI, V.; JÄRVI, A. **Architecting Game Engines: On the Performance of Entity-Component-System Paradigm**. Journal of Game Development and Technology, v. 12, n. 3, p. 45-62, 2020.

UNITY TECHNOLOGIES. **Unity DOTS Documentation**. Disponível em: <https://docs.unity.com/dots>. Acesso em: 2025.

EPIC GAMES. **Unreal Engine 5 Entity System Documentation**. Disponível em: <https://docs.unrealengine.com>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Próximo arquivo:** 04_Simulacao_Fisica.md

