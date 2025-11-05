# Glossário: Termos Técnicos de Game Engines

**Pesquisa:** O Papel dos Motores Gráficos na Evolução e Acessibilidade do Desenvolvimento de Jogos Digitais  
**Autor:** Diego  
**Orientador:** Mateus Filipe Domingos  
**Data:** Outubro de 2025

---

## A

**API (Application Programming Interface)**
Interface que permite que o código acesse funcionalidades do sistema operacional ou engine. Ex: DirectX 12 é a API gráfica do Windows.

**Asset**
Qualquer conteúdo de mídia usado no jogo: modelos 3D, texturas, sons, animações, etc.

**Asset Store**
Marketplace digital de game engines onde desenvolvedores compram/vendem assets. Exemplo: Unity Asset Store.

---

## B

**Backface Culling**
Otimização de renderização que descarta triângulos que não enfrentam a câmera, economizando performance.

**Billboard**
Geometria plana (quad) que sempre enfrenta a câmera. Usado para particulas, árvores distantes.

**Blueprints**
Sistema visual de scripting do Unreal Engine que permite criar lógica sem escrever código.

**BRDF (Bidirectional Reflectance Distribution Function)**
Função matemática que descreve como uma superfície reflete luz em diferentes ângulos.

---

## C

**Cache (CPU/GPU)**
Memória ultrarrápida perto do processador que armazena dados frequentemente acessados, melhorando performance.

**Cel Shading**
Estilo de renderização que faz objetos 3D parecerem desenhados/cartoon.

**Cinema 4D**
Software de modelagem e animação 3D profissional. Usado para criar assets.

**Cloth Simulation**
Simulação física de tecidos, cordas, cabelos. Exemplo: capa que se move realisticamente.

**Collision Detection**
Processo de detectar quando dois objetos colidem no espaço 3D/2D.

**Construct 3**
Game engine web-based com visual scripting, popular para indie games.

**Culling**
Processo de descartar objetos que não precisam ser renderizados (fora da câmera, ocluídos, etc).

---

## D

**Deferred Rendering**
Técnica de renderização que permite muitas luzes renderizando geom etria primeiro, depois aplicando iluminação.

**Decima Engine**
Motor proprietário de Guerrilla Games, usado em Horizon Zero Dawn e God of War.

**DLSS (Deep Learning Super Sampling)**
Tecnologia NVIDIA que usa IA para renderizar em resolução baixa e upsampling inteligente para resolução alta.

**Draw Call**
Comando para GPU renderizar um objeto. Muitas draw calls = lag na CPU.

---

## E

**ECS (Entity-Component-System)**
Padrão de arquitetura que separa dados (components) de lógica (systems) para melhor performance.

**Entidade**
Objeto no mundo do jogo. Em ECS, é apenas um ID com componentes associados.

**Engine as Service (EaaS)**
Modelo de distribuição onde o engine é uma serviço em nuvem, não um download.

---

## F

**Forward Rendering**
Técnica de renderização tradicional que renderiza cada objeto com todas as luzes. Lento com muitas luzes.

**Frustum**
Volume de visualização da câmera (forma de pirâmide). Objetos fora dele são culled.

**FSR (FidelityFX Super Resolution)**
Tecnologia AMD similar a DLSS, upsampling com IA.

---

## G

**GameMaker Studio**
Game engine especializado em 2D, popular para indie games.

**Godot**
Game engine open-source, gratuito, em crescimento desde 2023.

**GPU (Graphics Processing Unit)**
"Processador gráfico" que renderiza imagens. Milhares de cores de cores em paralelo.

**GDScript**
Linguagem de programação de Godot Engine, similar a Python.

---

## H

**Half-Life**
Jogo lendário de 1998 que usou Quake engine licenciada. Provou modelo de licenciamento viável.

**HDR (High Dynamic Range)**
Renderização com valores de cor além de 0-1, permitindo brilho extremo e efeitos cinematográficos.

**Havok Physics**
Motor de física profissional usado em muitos games AAA. Alternativa a PhysX.

**Hollow Knight**
Indie metroidvania feito em Unity por 3 pessoas. Sucesso crítico e comercial.

---

## I

**Instancing (GPU Instancing)**
Técnica que renderiza múltiplas cópias do mesmo modelo em 1 draw call. 100 trees = 1 call.

**Inverse Kinematics (IK)**
Técnica de animação onde você define posição de mão, e o sistema calcula posição de braço/ombro automaticamente.

---

## J

**JSON (JavaScript Object Notation)**
Formato de arquivo de dados comumente usado para salvar configurações, dados de jogo.

---

## L

**Lighting**
Simulação de como luz interage com objetos no mundo. Pode ser real-time, baked, ou híbrida.

**LOD (Level of Detail)**
Técnica que reduz complexidade visual (polígonos, texturas) conforme distância da câmera aumenta.

**Lumen**
Sistema de iluminação global dinâmica em tempo real de Unreal Engine 5.

---

## M

**Material**
Combinação de shaders, texturas e propriedades que definem como um objeto é renderizado.

**Metaverse**
Mundo virtual persistente onde múltiplos usuários interagem. Conceito emergente.

**Metal**
API gráfica de baixo nível da Apple (iOS, macOS), similar a DirectX 12.

**Mesh**
Geometria 3D composta de vértices, arestas e faces. Representa forma de um objeto.

**MiHoYo**
Desenvolvedora chinesa de Genshin Impact e Honkai series.

**Multiplayer**
Múltiplos jogadores em um game. Pode ser local (splitscreen) ou online (networked).

---

## N

**Nanite**
Tecnologia de Unreal Engine 5 que virtualiza geometria, permitindo detalhes infinitos.

**NPC (Non-Player Character)**
Personagem controlado pelo IA no jogo.

**NVIDIA PhysX**
Motor de física open-source de NVIDIA, muito usado em game engines.

---

## O

**Open-World**
Mundo virtual grande e explorador livre. Exemplo: Skyrim, The Witcher 3, Fortnite.

**Optimization**
Processo de melhorar performance e reduzir consumo de recursos.

---

## P

**PBR (Physically-Based Rendering)**
Abordagem de renderização que simula física real de como luz interage com materiais.

**Pipeline de Renderização**
Sequência de operações que transforma dados 3D em imagem 2D na tela.

**Pixel**
Menor unidade de uma imagem digital. Resolução 1920x1080 = 1920 pixels largura x 1080 altura.

**Portal**
Jogo de puzzle inovador de Valve usando Source engine. Provou física-based gameplay.

**Post-Processing**
Efeitos aplicados após renderização completa. Exemplo: bloom, color grading, motion blur.

**Procedural Generation (PCG)**
Geração automática de conteúdo (mapas, texturas) usando algoritmos.

---

## Q

**Quake**
Jogo lendário de 1996 cujo engine (id Tech) foi licenciada para muitos games.

---

## R

**Rasterization**
Processo de converter geometria 3D em pixels 2D. Oposto de ray tracing.

**Ray Tracing**
Técnica que simula traçado de raios de luz para iluminação fotorrealista (caro).

**REDengine**
Motor proprietário de CD Projekt Red, usado em The Witcher 3 e Cyberpunk 2077.

**Reflection**
Técnica que reflete luz/câmera em superfícies para efeitos de espelho e água.

**Rigidbody**
Objeto que obedece leis da física (gravidade, colisão, impulso).

**RTS (Real-Time Strategy)**
Gênero de jogo de estratégia jogado em tempo real. Exemplo: StarCraft.

---

## S

**Scripting**
Programação de lógica de jogo em linguagens geralmente simples. Exemplo: GDScript, Lua.

**Shader**
Programa executado na GPU que define como pixels são renderizados. Vertex shader, pixel shader.

**Skeletal Animation**
Animação baseada em "ossos" que deformam geometria. Mais eficiente que morph targets.

**Streaming**
Carregamento de assets assincronamente para não bloquear o jogo.

**Switch (Nintendo)**
Console portátil de Nintendo. Performance limitada (70km² reduzida em The Witcher 3).

---

## T

**Texture**
Imagem 2D mapeada em superfície 3D para detalhe visual.

**Tilemap**
Grade de "tiles" (blocos pequenos) que forma um mapa 2D. Popular em indie games.

**The Witcher 3**
RPG open-world de CD Projekt Red (2015). Sucesso crítico, ~40M copies vendidas.

---

## U

**Unreal Engine**
Game engine profissional de Epic Games. Padrão AAA.

**Unity**
Game engine popular com comunidade indie massiva. Gratuita, multiplataforma.

**Unreal Script**
Linguagem de scripting de Unreal Engine 3 (descontinuada em UE4).

---

## V

**Vertex**
Ponto individual em geometria 3D. Triângulo = 3 vértices.

**Vertex Shader**
Programa que processa cada vértice (transformação, animação).

**Vulkan**
API gráfica moderna, open-source, multi-plataforma. Alternativa a DirectX 12.

---

## W

**Witcher 3**
Veja "The Witcher 3".

**WebGL**
API gráfica para renderização 3D em navegadores web.

---

## X

**XR (Extended Reality)**
Termo guarda-chuva para VR (virtual reality) + AR (augmented reality).

---

## Y

**Yield**
Keyword em linguagens de scripting para pausar execução e retomar no próximo frame.

---

## Z

**Z-Buffer**
Técnica que armazena profundidade de cada pixel para determinar qual objeto está na frente.

---

**Última atualização:** Outubro de 2025

