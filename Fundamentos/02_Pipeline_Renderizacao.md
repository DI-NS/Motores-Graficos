# 02 - Pipeline de Renderização em Tempo Real

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Sumário
1. [Introdução ao Pipeline](#introdução-ao-pipeline)
2. [Estágios Principais do Pipeline](#estágios-principais-do-pipeline)
3. [Vertex Shading](#vertex-shading)
4. [Rasterização](#rasterização)
5. [Pixel/Fragment Shading](#pixelfragment-shading)
6. [Otimizações Modernas](#otimizações-modernas)
7. [Tecnologias Emergentes](#tecnologias-emergentes)
8. [Referências](#referências)

---

## Introdução ao Pipeline

### O que é o Pipeline de Renderização?

O **pipeline de renderização em tempo real** é a sequência de operações que transforma **dados 3D** (vértices, geometria, texturas) em uma **imagem 2D** a ser exibida na tela, **a cada quadro** (30-60+ FPS).

**Objetivo Principal:** Otimizar a transformação de dados 3D → imagem 2D com máxima eficiência, respeitando restrições de tempo real (poucos milissegundos por frame).

### Por que é Crítico?

1. **Performance:** Determina o FPS e a fluidez do jogo
2. **Qualidade Visual:** Define os limites de fidelidade gráfica
3. **Escalabilidade:** Como o motor adapta-se a diferentes hardwares
4. **Inovação:** Novas técnicas de renderização definem gerações de games

---

## Estágios Principais do Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│            DADOS 3D (GEOMETRIA, TEXTURAS, LUZ)             │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
        ╔════════════════════════════════════════╗
        ║      VERTEX PROCESSING (GPU)           ║
        ║  - Transformação de vértices           ║
        ║  - Cálculos de iluminação (per-vertex) ║
        ║  - Deformação (skeletal animation)     ║
        ╚════════════════════┬═════════════════╝
                         │
                         ▼
        ╔════════════════════════════════════════╗
        ║      RASTERIZAÇÃO                      ║
        ║  - Conversão: triângulos 3D → pixels 2D│
        ║  - Cálculo de profundidade (Z-buffer)  ║
        ║  - Culling de backface                 ║
        ╚════════════════════┬═════════════════╝
                         │
                         ▼
        ╔════════════════════════════════════════╗
        ║      FRAGMENT PROCESSING (GPU)         ║
        ║  - Cálculo de cor por pixel            ║
        ║  - Aplicação de texturas               ║
        ║  - Iluminação por pixel (normal maps)  ║
        ║  - Efeitos especiais (parallax, etc)   ║
        ╚════════════════════┬═════════════════╝
                         │
                         ▼
        ╔════════════════════════════════════════╗
        ║      RENDER TARGET (COLOR + DEPTH)     ║
        ║  - Framebuffer (textura final)         ║
        ║  - Post-processing                     ║
        ╚════════════════════┬═════════════════╝
                         │
                         ▼
        ╔════════════════════════════════════════╗
        ║      OUTPUT NA TELA                    ║
        ║  - V-Sync, Teardown prevention         ║
        ║  - Display                             ║
        ╚════════════════════════════════════════╝
```

---

## Vertex Shading

### O que é Vertex Shading?

O **vertex shader** é um programa de GPU que processa **cada vértice da geometria** de forma independente e paralela.

**Responsabilidades:**
1. **Transformação de Coordenadas:** De espaço local → mundo → câmera → projeção
2. **Iluminação Per-Vertex (Gouraud Shading):** Cálculo de luz em cada vértice
3. **Deformação:** Skeletal animation, vertex morphing, wind simulation
4. **Texcoord Manipulation:** Parallax mapping, scrolling textures

### Exemplo Prático: Transformação de Vértice

```glsl
// Pseudo-código GLSL
varying vec3 vNormal;
varying vec3 vFragPos;

void main() {
    // Transformar posição local para espaço de tela
    gl_Position = uProjectionMatrix * uViewMatrix * uModelMatrix * vec4(aPosition, 1.0);
    
    // Transformar normal para espaço mundial
    vNormal = normalize(uNormalMatrix * aNormal);
    
    // Posição no espaço mundial (para iluminação per-pixel)
    vFragPos = vec3(uModelMatrix * vec4(aPosition, 1.0));
}
```

### Performance do Vertex Shader

- **Executado:** Uma vez por vértice da geometria
- **Paralelização:** Milhões de vértices processados simultaneamente na GPU
- **Impacto:** Altamente otimizado; complexidade relativa é baixa

---

## Rasterização

### O que é Rasterização?

A **rasterização** é o processo de converter **geometria vetorial contínua** (triângulos 3D) em **amostras discretas de pixel** em uma grade 2D.

### Estágios da Rasterização

#### 1. Culling de Backface
```
Triângulos que não enfrentam a câmera são descartados:
- Reduz 50% do trabalho de rasterização
- Simples verificação de direção de normal vs câmera
```

#### 2. Clipping
```
Triângulos fora do frustum de visualização são cortados
- Evita processar pixels fora da tela
```

#### 3. Conversão de Tela
```
Coordenadas 3D → coordenadas de pixel 2D
- Operação matemática de perspectiva
```

#### 4. Determinação de Coverage
```
Qual pixel está "dentro" do triângulo?
- Teste de ponto-em-triângulo para cada pixel
- Subamostragem para anti-aliasing (MSAA, FXAA)
```

#### 5. Z-Buffer (Depth Buffer)
```
Determina qual objeto está na frente
- Comparação de profundidade para cada pixel
- Pixel mais próximo ganha
```

### Rasterização vs Ray Tracing

| Aspecto | Rasterização | Ray Tracing |
|--------|-------------|-----------|
| Abordagem | Triângulo → Pixel | Pixel → Cena |
| Velocidade | Muito rápida (real-time) | Lenta (offline/filme) |
| Reflexões | Limitadas | Perfeitas |
| Sombras | Aproximadas | Fisicamente corretas |
| Uso | Todos os games (2015+) | Cinema, render offline |

---

## Pixel/Fragment Shading

### O que é Fragment Shader?

O **fragment shader** (ou pixel shader) calcula a **cor final de cada pixel** processado.

### Responsabilidades Principais

#### 1. Aplicação de Texturas
```glsl
vec4 texColor = texture(uTextureSampler, vTexCoord);
```

#### 2. Iluminação Por-Pixel (Phong/PBR)
```glsl
// Iluminação Phong
vec3 normal = normalize(vNormal);
vec3 lightDir = normalize(uLightPos - vFragPos);
float diff = max(dot(normal, lightDir), 0.0);
vec3 diffuse = diff * texColor.rgb * uLightColor;
```

#### 3. Normal Mapping
```
Simula detalhes de superfície sem geometria adicional
- Armazena perturbações de normal em textura
- Cálculo de iluminação usa normal perturbado
```

#### 4. Parallax Mapping / Height Mapping
```
Cria ilusão de profundidade sem geometria
- Desloca texcoords baseado em altura/altura
- Efeito 3D convincente com performance
```

#### 5. Ambient Occlusion (AO)
```
Escurece áreas onde luz teria dificuldade de chegar
- Falhas geométricas, cantos, grietas
- Melhora realismo dramaticamente
```

### Exemplo: Fragment Shader Completo (PBR Simplificado)

```glsl
varying vec3 vNormal;
varying vec3 vFragPos;
varying vec2 vTexCoord;

void main() {
    // Amostra de texturas
    vec4 albedo = texture(uAlbedoMap, vTexCoord);
    vec3 normal = normalize(texture(uNormalMap, vTexCoord).rgb * 2.0 - 1.0);
    float metallic = texture(uMetallicMap, vTexCoord).r;
    float roughness = texture(uRoughnessMap, vTexCoord).r;
    
    // Iluminação PBR (simplificada)
    vec3 viewDir = normalize(uCameraPos - vFragPos);
    vec3 lightDir = normalize(uLightPos - vFragPos);
    
    // Cook-Torrance BRDF (distribuição microfacetas)
    // ... cálculos complexos ...
    
    gl_FragColor = vec4(finalColor, 1.0);
}
```

---

## Otimizações Modernas

### 1. Deferred Rendering

**Problema:** Muitas luzes = muitas computações de iluminação

**Solução:** G-Buffer (Geometry Buffer)
```
Primeira passagem: Renderizar geometria em texturas
- Posição, Normal, Albedo, etc. em texturas

Segunda passagem: Aplicar iluminação usando G-Buffer
- Uma vez por pixel, independente de objetos
```

**Benefício:** Escalabilidade com múltiplas luzes (100+)

### 2. Forward+ Rendering

Híbrido entre forward e deferred:
- Forward: Renderiza objetos individualmente
- Plus: Culling de luz por tile/cluster

### 3. Occlusion Culling

**Problema:** Renderizar objetos atrás de paredes

**Solução:** Pre-computed visibility sets
- Divide mundo em células
- Calcula quais células são visíveis de cada célula
- Descarta geometria não-visível

**Impacto:** 30-50% redução de draw calls

### 4. LOD (Level of Detail)

```
Objetos distantes = geometria simples
Objetos próximos = geometria detalhada

Exemplo: Um edifício a 100m
- Distância: 100 triângulos
- Próximo: 100.000 triângulos
```

### 5. Instancing

```
Renderizar mesmo mesh múltiplas vezes em um draw call
- 1000 árvores: 1 draw call (em vez de 1000)
- Redução massiva de overhead de CPU
```

---

## Tecnologias Emergentes

### 1. Ray Tracing em Tempo Real

**Antes (Rasterização):** Reflexões/sombras aproximadas  
**Agora (Ray Tracing):** Traçar raios de luz para precisão

**Exemplo NVIDIA RTX:**
- Núcleos especializados para traçar raios
- 1-2 bounces em tempo real em 4K

**Limitações:** Ainda requer rasterização + ray tracing combinados

### 2. Nanite (Unreal Engine 5)

**Problema:** Modelos com milhões de polígonos = lag  
**Solução:** Renderização virtual de polígonos

```
Nanite vs Tradicional:
- Tradicional: 100M triângulos → lag
- Nanite: Simula 100M triângulos com GPU magic
```

### 3. Lumen (Unreal Engine 5)

**Problema:** Iluminação global = cálculos offline  
**Solução:** GI dinâmica em tempo real

```
- Sem precalcular lightmaps
- Luzes mudam → iluminação se atualiza instantaneamente
```

### 4. DLSS / FSR (AI Upsampling)

**Problema:** 4K = muitos pixels para calcular  
**Solução:** Renderizar em 1440p + AI interpola para 4K

```
DLSS 3 (NVIDIA):
- Renda em 540p
- IA upsamples para 4K
- +50% FPS com qualidade equivalente
```

---

## Referências

MÖLLER, T.; HAINES, E.; HOFFMAN, N. **Real-Time Rendering**. 4. ed. CRC Press, 2018.

GREGORY, J.; BIDI, I. **Foundations of Game Engine Development: Volume 2: Rendering**. 2. ed. Terathon Software, 2023.

EBERLY, D. H. **3D Game Engine Design: A Practical Approach to Real-Time Computer Graphics**. 2. ed. Morgan Kaufmann, 2006.

EPIC GAMES. **Unreal Engine 5 Rendering Documentation**. Disponível em: <https://docs.unrealengine.com>. Acesso em: 2025.

NVIDIA. **RTX Ray Tracing Technology Guide**. Disponível em: <https://developer.nvidia.com/rtx>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Próximo arquivo:** 03_Entity_Component_System.md

