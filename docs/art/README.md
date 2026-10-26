# Briefs de concept art

**Status:** briefs textuais para revisão; nenhuma imagem ou sprite foi produzida  
**Escopo:** direção visual comum e quatro grupos de briefs do conteúdo selecionado para o MVP.

O [GDD](../GDD.md) define as regras e o [plano do MVP](../MVP_IMPLEMENTATION.md) organiza os marcos. Estes briefs traduzem as decisões visuais em material acionável para futura exploração de arte; não substituem nem encerram a revisão do GDD.

## Direção visual compartilhada

### Decisões confirmadas

- **Universo:** guerra fictícia inspirada na primeira metade do século XX. Uniformes e armas são autorais; afiliação específica das facções ainda não foi definida.
- **Proporções:** semirrealistas e compactas, próximas à escala visual da referência fornecida. Silhuetas devem funcionar em leitura top-down e preservar faces frontais verticais visíveis; isso não fixa câmera isométrica de 45 graus nem perspectiva final.
- **Acabamento:** pixel art de alto detalhe, com texturas densas próximas à referência. As texturas devem acompanhar o volume e o material; reservar o contraste mais forte para personagens, itens, sinais de ataque e saídas.

### Propostas de direção para revisão

Conceitos usam massas de luz/material, mas são pensados para futura rasterização pixel art. Silhueta e valores precedem marcas e ruído; textura segue material/plano sem apagar bordas de piso, navegabilidade ou alturas.

Estudos de paleta podem combinar areia bege/ocre e verde oliva/marrom no setor 1, e cinzas frios com pontos âmbar no setor 2. Não são cores finais. Luz quente pode marcar lâmpadas, mas nunca ser o único sinal de item, ameaça, saída ou jogador. Iluminação de personagens, props, faces frontais e sombras deve ser coerente.

P1 e P2 pertencem ao mesmo lado. Diferenciá-los por pequenos marcadores de forma e acentos de cor além de acessórios; cor sozinha não deve identificar o dono. Contraste de inimigo deve funcionar em cor e escala de cinza. A referência [concept-art.png](../references/concept-art.png) é exploratória para uma região de trincheiras; não dita a aparência das duas áreas, de todas as regiões nem dos demais assets.

Resolução, tile grid, tamanho de sprite, ângulo, paleta, frames e shaders seguem em aberto. Testar nearest-neighbor quando grid/zoom forem definidos; manter VFX econômico para a meta de 30 FPS, sem presumir hardware ou densidade.

## Índice e ordem sugerida

1. [Ambientes](ENVIRONMENTS.md): estabelecer dois keyframes de setor e leitura espacial.
2. [Personagens](CHARACTERS.md): desenvolver jogador e roster de três inimigos contra os fundos aprovados.
3. [Armas e itens](WEAPONS_AND_ITEMS.md): desenhar armamentos, munição, pickup e kit de cura em escala de gameplay.
4. [Coberturas e efeitos](COVERS_AND_EFFECTS.md): mostrar materiais, alturas, degradação e feedback dos impactos.

Sequência proposta: revisar briefs; explorar style frames dos dois ambientes na mesma escala; revisar luz/paleta; fazer folhas de personagens, armas e materiais; compor mockups de leitura; depois especificar assets/animações. É um fluxo de revisão, não autorização de produção final.

## Conferência transversal

Conferir vista superior/faces verticais, thumbnails, escala de cinza e futuro zoom mínimo. Distinguir jogadores, inimigos, espaço navegável, cobertura, item e saída. Chuva não esconde telegraphs, balas ou silhuetas. Conceitos não sugerem natação, balística, classes ou interação ausentes do GDD.

## Confirmado, proposto e pendente

| Estado | Itens |
|---|---|
| Confirmado | Guerra fictícia inspirada na primeira metade do século XX; proporções compactas semirrealistas; pixel art de alto detalhe/textura densa; setores do MVP: praia no fim da tarde avançando para trincheira relativamente ensolarada, depois trincheiras à noite com chuva. |
| Proposta | Famílias cromáticas por ambiente, linguagem de luz, marcadores visuais P1/P2, desenhos concretos de uniformes, silhuetas e props. |
| Pendente | Ângulo/câmera final, resolução e tile grid, paleta final, proporções exatas de sprites, quantidade de frames, limites gráficos e detalhes de uniforme/insígnias. |
