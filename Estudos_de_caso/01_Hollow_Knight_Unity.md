# 01 - Hollow Knight: Indie AAA com Unity

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Sumário
1. [Visão Geral do Projeto](#visão-geral-do-projeto)
2. [Contexto de Desenvolvimento](#contexto-de-desenvolvimento)
3. [Escolha Tecnológica: Por que Unity?](#escolha-tecnológica-por-que-unity)
4. [Arquitetura Técnica](#arquitetura-técnica)
5. [Desafios Técnicos](#desafios-técnicos)
6. [Lições Aprendidas](#lições-aprendidas)
7. [Referências](#referências)

---

## Visão Geral do Projeto

### Dados Básicos

| Aspecto | Informação |
|--------|-----------|
| **Título** | Hollow Knight |
| **Desenvolvedora** | Team Cherry (Austrália) |
| **Tamanho do Estúdio** | ~3 pessoas |
| **Tempo de Desenvolvimento** | ~4-5 anos (2011-2017) |
| **Plataformas** | Windows, Mac, Linux, Nintendo Switch, PlayStation, Xbox |
| **Engine** | Unity 2D |
| **Linguagem** | C# |
| **Gênero** | Metroidvania / Ação-Aventura |

### Sucesso Comercial e Crítico

- **Vendas:** Mais de 3 milhões de cópias (2023)
- **Crítica:** Pontuação 86-90 em Metacritic
- **Comunidade:** Fanbase extremamente leal e ativa
- **Impacto:** Prova de que indie pode fazer qualidade AAA

---

## Contexto de Desenvolvimento

### Situação Pré-Desenvolvimento (2010)

**Cenário:**
- Team Cherry era uma pequena desenvolvedora
- Indie games ainda tinham dificuldade de monetização
- Console indie era praticamente inexistente
- Acesso a engines AAA (Unreal, proprietary) era caro/restrito

### Por que Hollow Knight?

**Visão do Time:**
- Criar um jogo clássico Metroidvania (referência: Super Metroid, Castlevania)
- Qualidade AAA com equipe indie
- Mundo vasto e exploração aberta
- Arte 2D em um mundo 3D (pseudo-3D)

### Obstáculos Iniciais

```
❌ Orçamento limitado
❌ Equipe pequena (3 pessoas)
❌ Sem suporte publisher grande
❌ Tecnologia proprietária restrita
✅ Acesso a Unity (gratuita para indie)
```

---

## Escolha Tecnológica: Por que Unity?

### Alternativas Consideradas (2011)

| Motor | Motivo de Rejeição |
|------|-------------------|
| Unreal Engine 3 | Muito caro, orientado a 3D |
| Proprietary | Desenvolvimto longo, custoso |
| Godot | Ainda em beta, comunidade pequena |
| GameMaker | Limitation em sistemas de física |
| **Unity ✓** | Gratuita, 2D suportado, fácil |

### Razões para Escolher Unity

#### 1. **Custo Zero**
```
Começar com Unity = Gratuito
Sem royalties para jogos indie
Decisão de negócio viável para pequeno estúdio
```

#### 2. **Multi-plataforma Nativa**
```
Codificar uma vez, publicar em:
- PC (Windows, Mac, Linux)
- Consoles (Switch, PS4, Xbox)
- Mobile (iOS, Android)

Hollow Knight usou esse benefício para atingir múltiplos mercados
```

#### 3. **2D Maduro (Unity 2D Tools)**
```
✓ Sprite rendering otimizado
✓ Animation tools
✓ Physics 2D (Box2D)
✓ Tilemap system
✓ Particle effects

Tudo que um jogo 2D indie precisa
```

#### 4. **Asset Store**
```
Team Cherry pôde usar/comprar:
- Particle effects prontos
- Shaders especiais
- Ferramentas de level design
- Plugins de otimização

Economizou meses de desenvolvimento
```

---

## Arquitetura Técnica

### Estrutura do Projeto

```
HollowKnight/
├── Assets/
│   ├── Scripts/           (C# gameplay)
│   ├── Sprites/           (Arte pixel 2D)
│   ├── Scenes/            (Cenas/níveis)
│   ├── Prefabs/           (Templates de objetos)
│   ├── Audio/             (Música, SFX)
│   ├── Animations/        (Animações 2D)
│   └── Resources/         (Dados configuráveis)
├── ProjectSettings/       (Configurações Unity)
└── Library/               (Cache compilado)
```

### Componentes Principais

#### 1. Sistema de Movimento
```csharp
public class PlayerController : MonoBehaviour {
    public float moveSpeed = 10f;
    public float jumpForce = 15f;
    
    private Rigidbody2D rb;
    private Animator animator;
    
    void Update() {
        float input = Input.GetAxis("Horizontal");
        rb.velocity = new Vector2(input * moveSpeed, rb.velocity.y);
        
        if (Input.GetKeyDown(KeyCode.Space)) {
            rb.velocity += Vector2.up * jumpForce;
        }
    }
}
```

#### 2. Sistema de Combate
```csharp
public class WeaponController : MonoBehaviour {
    public float attackRange = 2f;
    public float attackDamage = 5f;
    public float attackCooldown = 0.5f;
    
    private float lastAttackTime;
    
    void Update() {
        if (Input.GetMouseButtonDown(0) && Time.time > lastAttackTime + attackCooldown) {
            PerformAttack();
            lastAttackTime = Time.time;
        }
    }
    
    void PerformAttack() {
        // Detectar inimigos em range
        Collider2D[] hits = Physics2D.OverlapCircleAll(transform.position, attackRange);
        foreach (Collider2D hit in hits) {
            if (hit.CompareTag("Enemy")) {
                hit.GetComponent<Health>().TakeDamage(attackDamage);
            }
        }
    }
}
```

#### 3. Sistema de Saúde
```csharp
public class Health : MonoBehaviour {
    public int maxHealth = 100;
    private int currentHealth;
    
    void Start() {
        currentHealth = maxHealth;
    }
    
    public void TakeDamage(int damage) {
        currentHealth -= damage;
        
        if (currentHealth <= 0) {
            Die();
        }
    }
    
    void Die() {
        if (CompareTag("Player")) {
            // Respawnar no checkpoint
            GameManager.Instance.RespawnPlayer();
        } else {
            // Destroy inimigo
            Destroy(gameObject);
        }
    }
}
```

#### 4. Sistema de Checkpoint/Save
```csharp
public class CheckpointManager : MonoBehaviour {
    private Vector3 lastCheckpoint;
    
    void OnTriggerEnter2D(Collider2D collision) {
        if (collision.CompareTag("Player")) {
            lastCheckpoint = transform.position;
            SaveGame();
            UIManager.Instance.ShowCheckpointMessage();
        }
    }
    
    void SaveGame() {
        PlayerPrefs.SetFloat("PlayerX", lastCheckpoint.x);
        PlayerPrefs.SetFloat("PlayerY", lastCheckpoint.y);
        PlayerPrefs.Save();
    }
}
```

### Fluxo de Renderização 2D

```
┌──────────────────────────────────┐
│    Sprites e Animações           │
│  (pixel art 2D, 16-bit style)    │
└────────────┬─────────────────────┘
             │
             ▼
┌──────────────────────────────────┐
│    Camera 2D com Parallax        │
│  (fundo se move mais lentamente) │
└────────────┬─────────────────────┘
             │
             ▼
┌──────────────────────────────────┐
│    Compositing (UI sobreposto)   │
│  (health bar, inventário, etc)   │
└────────────┬─────────────────────┘
             │
             ▼
┌──────────────────────────────────┐
│    Post-processing (filtros)     │
│  (se necessário, mínimo)         │
└────────────┬─────────────────────┘
             │
             ▼
┌──────────────────────────────────┐
│    Render na Tela                │
└──────────────────────────────────┘
```

---

## Desafios Técnicos

### 1. Performance em Consoles

**Problema:** Nintendo Switch é menos poderoso que PC/PS4

**Solução:**
- Redução de sprite resolution (1.5x em vez de 2x)
- LOD (Level of Detail) para inimigos longe
- Culling agressivo de objetos fora de tela
- Otimização de shaders para GPU móvel

**Resultado:** 60 FPS estável no Switch

### 2. Portabilidade Multiplataforma

**Problema:** 
```
Different input (controller vs mouse/keyboard)
Different resolutions (720p-4K)
Different APIs gráficas
```

**Solução:**
```csharp
// Abstração de input
public class InputManager : MonoBehaviour {
    public Vector2 GetMovementInput() {
        return new Vector2(
            Input.GetAxis("Horizontal"),
            Input.GetAxis("Vertical")
        );
    }
    
    public bool GetAttackInput() {
        return Input.GetButtonDown("Fire1");
    }
}

// Funciona com:
// - Teclado (WASD + espaço)
// - Joystick (analógico + botão)
// - Touch (no mobile, se portar)
```

### 3. Tamanho do Arquivo

**Problema:** Asset Store + spites HD = arquivo grande (4GB+)

**Solução:**
- Compressão agressiva de texturas
- Asset bundles (carregar sob demanda)
- Remoção de assets não usados (versão final: ~2GB)

### 4. Sincronização de Física 2D

**Problema:** Frame rate variável = física instável

**Solução:**
```csharp
void FixedUpdate() {  // Timestep fixo
    // Física executada em 60 FPS sempre
    // Independente da taxa de render
    rb.velocity = new Vector2(moveX * speed, rb.velocity.y);
}

void Update() {  // Frame variável
    // Input e lógica visual
    float input = Input.GetAxis("Horizontal");
    moveX = input;
}
```

---

## Lições Aprendidas

### O que Funcionou

✅ **Unity foi a escolha certa**
- Desenvolvimento rápido
- Multi-plataforma sem refactor massivo
- Asset Store acelerou desenvolvimento
- Comunidade ativa para troubleshoot

✅ **Arte pixel nostálgica**
- Otimizada para performance
- Charming e memorável
- Menos Assets que 3D de alta-fi

✅ **Design focado**
- Escopo bem definido (1 mapa conectado, 30h gameplay)
- Sem features desnecessárias
- Profundo em vez de amplo

### Desafios Principais

❌ **Suporte a consoles**
- Exigiu otimização extrema
- Diferentes arquivos de build
- Certificação por console é complexa

❌ **Early Access (Kickstarter)**
- Feedback durante desenvolvimento
- Necessidade de iteração contínua
- QA manual foi intenso

### Recomendações para Indie Studios

1. **Escolha engine alinhada ao escopo**
   - 2D simples → GameMaker / Godot
   - 3D indie → Godot / Unreal
   - Complexo → Unreal / Unity DOTS

2. **Defina escopo realista**
   - Equipe pequena = jogo focado
   - Melhor fazer 1 coisa perfeita

3. **Use Asset Store/Community**
   - Não reinventar a roda
   - Ferramentas prontas = tempo do core gameplay

4. **Teste multiplataforma cedo**
   - Performance varia dramaticamente
   - Não deixar para o final

---

## Referências

TEAM CHERRY. **Hollow Knight Official Website**. Disponível em: <https://www.hollowknight.com>. Acesso em: 2025.

UNITY TECHNOLOGIES. **Unity 2D Documentation**. Disponível em: <https://docs.unity.com/2d>. Acesso em: 2025.

GREGORY, J. **Game Engine Architecture**. 3. ed. CRC Press, 2018.

---

**Última atualização:** Outubro de 2025  
**Próximo:** 02_Genshin_Impact_Unity.md

