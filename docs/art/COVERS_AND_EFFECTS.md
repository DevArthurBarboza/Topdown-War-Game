# Brief — coberturas, materiais e efeitos

## ART-COV01 — Alturas e postura

Mostrar lado a lado personagens em pé e agachados atrás de uma cobertura baixa, e um personagem atrás de cobertura alta. A ilustração comunica geometria direcional: proteção total existe quando a cobertura corretamente posicionada bloqueia a linha de tiro. Levantar é necessário para atirar por cima da cobertura baixa; agachado ainda pode disparar em espaço livre ou pelo ângulo descoberto. Flancos e diferença de plano podem deixar exposição. Não desenhar a cobertura como aura/esfera de invulnerabilidade nem inventar percentuais de proteção em pé.

**Proposta visual.** Um corte simples ao lado da vista top-down pode esclarecer face vertical, topo da cobertura e linha de mira. Não transformar o corte em perspectiva obrigatória do gameplay. Marcar footprints/collider como informação de produção separada da textura.

## ART-COV02 — Materiais e quebra

**Estados.** Intacto, danificado e quebrado são estados visuais propostos. Explorar madeira/caixote frágil, pedra e sacos de terra resistentes; resistências e materiais quebráveis seguem balanceamento. “Resistente” não significa indestrutível por definição.

**Sequência ilustrativa.** Prancha de impactos: intacta; desgaste de superfície com lascas/rachaduras/deformações; quebrada com vão navegável. Como proposta visual inicial para o MVP, representar o dano intermediário por desgaste de superfície, preservando altura e bloqueio até a quebra; frestas funcionais ou redução progressiva de proteção exigiriam revisão das regras de gameplay. Marcas não definem frames/thresholds. O impacto que rompe é absorvido; só os próximos atravessam. Detritos decorativos não colidem.

**Estado do mundo.** As camadas de desenho devem permitir associar cobertura, dano e collider por ID estável. Sprite quebrado não pode sozinho decidir colisão; checkpoint restaura aparência e estado de colisão correspondentes. Não expandir isso para destruição total de cenário.

## ART-COV03 — Impactos e resposta

Criar estudos de leitura em areia/terra, madeira, pedra, metal e água, distinguindo som/partícula por material. Propostas: poeira/grãos para terra, lascas para madeira, fragmentos pequenos para pedra, faísca contida para metal e respingo cosmético para água. Evitar partículas densas que tampem a cena ou insinuem penetração/dano extra.

Acertos podem usar muzzle flash e traçante visual curto para apoiar leitura; hitscan resolve dano sem viagem balística ou atraso. Telegraph de inimigo deve preceder o disparo e ser legível quando o inimigo está na tela. VFX não anuncia sozinho nem autoriza ataque. Tiro que quebra cobertura não atinge ator atrás dela; o feedback visual reforça que a cobertura absorveu aquele impacto.

**Resposta de personagem.** Dano ao jogador produz breve desequilíbrio e reduz movimento, mantendo mira disponível; não há i-frames. Pose/efeito curto não bloqueia silhueta nem sugere stun total. Sangue moderado/configurável é proposta pendente de validação; não elevar gore por conta própria.

## ART-COV04 — Chuva, luz e custo visual

Nos conceitos do MVP, chuva e luz são camadas visuais/sonoras do ambiente. A chuva noturna precisa preservar leitura de personagens, balas, telegraphs, cobertura e saídas; poças e respingos são decorativos, sem física de água. Separar chuva de partículas de impacto para poder reduzir efeitos mantendo a leitura principal. Iluminação quente pontual não deve apagar o contraste das sombras ou se tornar o único indicador de ameaça.

**Conferência.** Montar a mesma amostra com jogadores, três inimigos, item, bala/VFX, cobertura e saída; comparar os dois ambientes do MVP em cor e cinza. Quando zoom mínimo for definido, distinguir postura, material, estágio de cobertura e ameaça. Não fixar grid, taxa de partículas ou shader.
