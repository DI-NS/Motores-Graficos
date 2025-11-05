# 06 - APIs Gráficas Modernas: DirectX, Vulkan e Metal

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## Sumário
1. [Introdução às APIs Gráficas](#introdução-às-apis-gráficas)
2. [Historia: OpenGL → Moderna](#historia-opengl--moderna)
3. [DirectX 12 / DirectX 11](#directx-12--directx-11)
4. [Vulkan](#vulkan)
5. [Metal (Apple)](#metal-apple)
6. [Comparação Técnica](#comparação-técnica)
7. [Impacto nos Game Engines](#impacto-nos-game-engines)
8. [Referências](#referências)

---

## Introdução às APIs Gráficas

### O que é uma API Gráfica?

Uma **API (Application Programming Interface) Gráfica** é a **interface entre o código e o hardware gráfico** (GPU).

### Responsabilidades de uma API Gráfica

1. **Abstração de Hardware:** Funcionam em múltiplas GPUs (NVIDIA, AMD, Intel)
2. **Command Submission:** Enviar instruções para a GPU renderizar
3. **Resource Management:** Texturas, buffers, shaders, etc.
4. **Sincronização:** Coordenar CPU ↔ GPU
5. **State Management:** Manter estado de renderização

### Por que Múltiplas APIs?

Diferentes filosofias e otimizações:
- **Alto nível (OpenGL):** Fácil de usar, menos controle
- **Baixo nível (Vulkan/DirectX12):** Difícil de usar, máximo controle
- **Proprietárias (Metal):** Otimizado para plataforma específica

---

## Historia: OpenGL → Moderna

### OpenGL (1992-2010s)

**OpenGL:**
- Padrão aberto, multi-plataforma
- Abordagem "state machine"
- Simples, mas ineficiente para CPUs modernas

**Problema:**
```
Render loop tradicional (OpenGL):
for (objeto em objetos) {
    SetMatrix(objeto.matrix);
    SetTexture(objeto.textura);
    SetShader(objeto.shader);
    Draw(objeto.mesh);  // 1 GPU command = 5-10 CPU calls!
}
```

**Resultado:** Bottleneck na CPU (draw call overhead)

### Transição para APIs Modernas (2014+)

**Razão:** GPUs modernas precisam de **menos CPU overhead**

**Característica:** Aproximação de "command buffers"
```
CPU: Pre-compila lista de comandos
GPU: Executa lista (muito eficiente)
```

---

## DirectX 12 / DirectX 11

### DirectX 11 (2009 - ainda em uso)

**Características:**
- API relativamente alto nível
- Gerenciamento de recursos automático
- Windows-only
- Mais simples que DX12, mas menos eficiente

**Uso:**
- Muitos games indie/AAA ainda usam
- Suporte em UE4, Unity (via ANGLE)

### DirectX 12 (2015+)

**Filosofia:** "Controle total, responsabilidade total"

**Mudanças Principais:**

#### 1. Command Lists e Command Queues

```cpp
// DirectX 12: Pre-compilar comandos
ComPtr<ID3D12GraphicsCommandList> commandList;
device->CreateCommandList(..., &commandList);

// Registrar todos os comandos
commandList->SetGraphicsRootSignature(rootSignature);
commandList->SetPipelineState(pipelineState);
commandList->SetGraphicsRootDescriptorTable(0, descriptorHandle);
commandList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
commandList->DrawInstanced(vertexCount, 1, 0, 0);

// Após fechar, enviar para GPU
commandList->Close();
commandQueue->ExecuteCommandLists(1, &commandList);
```

#### 2. Descriptor Tables (Resource Binding)

```
Antes (DX11): Set cada texture/buffer individualmente
DX12: Descrever tudo em uma tabela (descriptor table)

Benefício: 1 CPU call em vez de N calls
```

#### 3. Resource Barriers

```cpp
// Transições de estado explícitas
D3D12_RESOURCE_BARRIER barrier = {};
barrier.Type = D3D12_RESOURCE_BARRIER_TYPE_TRANSITION;
barrier.Transition.pResource = renderTarget;
barrier.Transition.StateBefore = D3D12_RESOURCE_STATE_RENDER_TARGET;
barrier.Transition.StateAfter = D3D12_RESOURCE_STATE_PRESENT;

commandList->ResourceBarrier(1, &barrier);

// Benefício: Driver otimiza transições de cache
```

**Resultado:** 50-100% melhoria de performance vs DX11

### Plataformas Suportadas

```
DirectX 12:  Windows 10/11, Xbox Series X/S
DirectX 11:  Windows Vista e acima, Xbox One
```

---

## Vulkan

### O que é Vulkan?

**Vulkan** é um padrão **open-source** de gráficos de baixo nível:
- Criada por Khronos Group (OpenGL committee)
- Sucessora espiritual de DirectX 12 + OpenGL
- Multi-plataforma: Windows, Linux, Mac, Mobile, Console

### Filosofia de Vulkan

"Você sabe o que quer fazer, diga à GPU diretamente"

**Controle Explícito:**
- Gerenciamento de memória manual
- Sincronização manual CPU ↔ GPU
- Sem driver magic (culpável por bugs/crashes)

### Estrutura de um Programa Vulkan

```cpp
// 1. Instância Vulkan
VkInstance instance;
vkCreateInstance(&createInfo, nullptr, &instance);

// 2. Dispositivo físico e lógico
VkPhysicalDevice physicalDevice;  // GPU encontrada
VkDevice device;                   // Contexto lógico
vkCreateDevice(physicalDevice, &deviceCreateInfo, &device);

// 3. Fila de comandos
VkQueue queue;
vkGetDeviceQueue(device, queueFamily, 0, &queue);

// 4. Command buffers
VkCommandBuffer cmdBuffer;
vkAllocateCommandBuffers(device, &allocInfo, &cmdBuffer);

// 5. Pre-registrar comandos
vkBeginCommandBuffer(cmdBuffer, &beginInfo);
vkCmdBindPipeline(cmdBuffer, VK_PIPELINE_BIND_POINT_GRAPHICS, pipeline);
vkCmdDraw(cmdBuffer, 3, 1, 0, 0);
vkEndCommandBuffer(cmdBuffer);

// 6. Enviar para GPU
VkSubmitInfo submitInfo = {VK_STRUCTURE_TYPE_SUBMIT_INFO};
submitInfo.commandBufferCount = 1;
submitInfo.pCommandBuffers = &cmdBuffer;
vkQueueSubmit(queue, 1, &submitInfo, VK_NULL_HANDLE);

// 7. Aguardar GPU terminar
vkQueueWaitIdle(queue);
```

### Vantagens de Vulkan

| Vantagem | Impacto |
|----------|--------|
| **Low overhead** | 50-100% mais draw calls |
| **Explícito** | Sem surpresas do driver |
| **Multi-threading** | Múltiplas threads podem criar command buffers |
| **Portatilidade** | Windows/Linux/Mac/Mobile/Console |
| **Otimizações** | Controle granular de performance |

### Desvantagens de Vulkan

| Desvantagem | Impacto |
|------------|--------|
| **Complexidade** | ~2000 linhas pra primeira imagem (vs 50 em OpenGL) |
| **Curva de aprendizado** | Muito maior que DX12 |
| **Erros fatais** | Sem validação automática = hard crashes |
| **Debugging** | Difícil debugar (validation layers ajudam) |

### Suporte em Engines

```
Unreal Engine 5: ✅ Suporte experimental
Unity: ✅ Suporte em Linux/Mac
Godot: ✅ Padrão em Linux
Proprietary: ⭐ Muitos engines AAA usam
```

---

## Metal (Apple)

### O que é Metal?

**Metal** é a API gráfica de **baixo nível de Apple** (iOS, macOS).

**Análoga a:**
- DirectX 12 (no nível de conceitos)
- Vulkan (simplicidade)
- Otimizada para hardware Apple M-series

### Características de Metal

```
Otimizado para:
✓ Apple M1/M2/M3 (ARM architecture)
✓ Unified memory (GPU + CPU compartilham RAM)
✓ Immediate mode rendering

Metal Shading Language (MSL):
- Similar a HLSL/GLSL
- Otimizado para Apple GPUs
```

### Exemplo Mínimo (Pseudo-código)

```swift
import Metal

// 1. Obter GPU
let device = MTLCreateSystemDefaultDevice()!

// 2. Command queue
let commandQueue = device.makeCommandQueue()!

// 3. Pipeline state
let pipelineStateDescriptor = MTLRenderPipelineDescriptor()
pipelineStateDescriptor.vertexFunction = vertexShader
pipelineStateDescriptor.fragmentFunction = fragmentShader
let pipelineState = try device.makeRenderPipelineState(descriptor: pipelineStateDescriptor)

// 4. Render loop
let commandBuffer = commandQueue.makeCommandBuffer()!
let renderPass = MTLRenderPassDescriptor()
// ... configurar passa de render ...
let renderEncoder = commandBuffer.makeRenderCommandEncoder(descriptor: renderPass)!

renderEncoder.setRenderPipelineState(pipelineState)
renderEncoder.drawPrimitives(type: .triangle, vertexStart: 0, vertexCount: 3)
renderEncoder.endEncoding()

commandBuffer.present(drawable)
commandBuffer.commit()
```

### Vantagens Únicas de Metal

1. **Unified Memory:** GPU acessa RAM do sistema diretamente
   - Sem cópia de dados entre CPU e GPU
   - Extremamente eficiente

2. **Tile-based Deferred Rendering:** Otimizado para Apple GPUs
   - Renderiza em tiles de 32x32 pixels
   - Cache de bandwidth economizado

3. **Integração com OS:** Acesso a features do SO
   - ProMotion (120 FPS displays)
   - Dynamic Island rendering
   - ARKit integrado

---

## Comparação Técnica

### Overhead de CPU

```
OpenGL:        ████████████████████████ (referência)
DirectX 11:    ████████████ (50% melhor)
Metal/DX12:    ████ (80% melhor)
Vulkan:        ██ (90% melhor)
```

### Facilidade de Uso

```
OpenGL:        ███████ (fácil)
DirectX 11:    █████ 
Metal:         ███ (moderado)
DirectX 12:    ███ (moderado)
Vulkan:        █ (difícil)
```

### Portabilidade

```
OpenGL:        ██████████ (todos OS, deprecated)
DirectX 11/12: █████ (Windows + Xbox)
Metal:         ██ (Apple apenas)
Vulkan:        ██████████ (ideal cross-platform)
```

### Performance Potencial

```
Vulkan:        ██████████ (máxima, se bem feito)
DirectX 12:    ██████████ (máxima)
Metal:         █████████ (excelente em Apple)
OpenGL:        ███████ (bom)
DirectX 11:    ██████ (limitado)
```

---

## Impacto nos Game Engines

### Unreal Engine 5

```
Suporte:
- DirectX 12 (padrão Windows)
- Vulkan (experimental)
- Metal (macOS/iOS)
- Proprietária (consoles)

Estratégia: Renderizador agnóstico
Usar Nanite/Lumen em qualquer backend
```

### Unity

```
Suporte:
- DirectX 11 (padrão Windows)
- Vulkan (Linux)
- Metal (macOS/iOS)
- GLES3 (Android)

Migração: Unity 6 focando em Vulkan
```

### Godot

```
Suporte:
- Vulkan (padrão Godot 4+)
- DirectX 11 (experimental)
- GLES3 (fallback)

Filosofia: Open-source first
Vulkan alinha com valores da comunidade
```

---

## Futuras Evoluções (2025+)

### 1. Ray Tracing Nativo em APIs
- Vulkan Ray Tracing extensions
- DirectX 12 enhancements
- Metal RT em Apple Silicon

### 2. Variable Rate Shading (VRS)
- Renderizar diferentes áreas com diferentes qualidades
- Economizar GPU sem perder qualidade visual

### 3. Mesh Shaders
- Unificação de vertex/geometry/tessellation
- Melhor controle de pipeline

### 4. GPUs Especializadas (NPUs)
- AI computation integrada
- Processamento de imagem IA nativa

---

## Referências

MÖLLER, T.; HAINES, E.; HOFFMAN, N. **Real-Time Rendering**. 4. ed. CRC Press, 2018.

GREGORY, J.; BIDI, I. **Foundations of Game Engine Development: Volume 2: Rendering**. 2. ed. Terathon Software, 2023.

KHRONOS GROUP. **Vulkan Specification and Resources**. Disponível em: <https://www.khronos.org/vulkan/>. Acesso em: 2025.

MICROSOFT. **DirectX 12 Documentation**. Disponível em: <https://docs.microsoft.com/en-us/windows/win32/direct3d12/>. Acesso em: 2025.

APPLE. **Metal Documentation**. Disponível em: <https://developer.apple.com/metal/>. Acesso em: 2025.

---

**Última atualização:** Outubro de 2025  
**Seção documentação: Completa!**

