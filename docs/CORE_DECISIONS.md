# Decisões centrais: confirmadas e pendentes

**Status:** registro de decisões e perguntas restantes  
**Escopo:** ciclo de gameplay, limites dos sistemas e critérios do primeiro slice. Recomendações identificadas como candidatas não são decisões aprovadas.

## Ritmo e estrutura da run

### D01 — Ritmo de combate — confirmado

**Decisão:** combate tático ágil.  
**Pendente:** letalidade moderada foi recomendada anteriormente, mas não foi explicitamente confirmada.  
**Efeito:** orienta tempo de resposta da IA, intensidade dos encontros e orçamento de ameaça.  
**Critério candidato:** jogadores leem a ameaça, reagem e entendem a causa do dano; a curva de letalidade ainda precisa ser validada.

### D02 — Progressão da run — parcialmente confirmada

**Decisões:** a run avança por setores até destino ainda indefinido; sem extração ou chefe obrigatórios. MVP tem dois setores com 4–6 salas cada. Cada nova run começa com pistola e recursos iniciais predefinidos; fuzil e escopeta são armas disponíveis como loot, sem garantir ambas em toda run. Não há duração em minutos aprovada.  
**Pendente/futuro:** definir conteúdo do destino. Outros modos poderão escolher conjuntos iniciais diferentes pós-MVP; não criar seletor de loadout/modos no MVP. Setores/salas podem variar por configuração de dificuldade/eventos em versão futura.  
**Efeito:** `RunDefinition`/`SectorDefinition` contém os limites e valores iniciais (2 setores, 4–6 salas); resolver plano por seed, versão e perfil antes de iniciar. Não espalhar contagens fixas pelo código nem alterá-las silenciosamente durante a run; variações dinâmicas futuras exigem estado versionado.  
**Critério candidato:** configuração inicial tem dois setores, cada qual com 4–6 salas conectadas, rota válida e destino alcançável.

## Sala, espaço e combate

### D03 — Saídas da sala — confirmado

**Decisão:** saídas bloqueiam enquanto há combate e reabrem quando a sala é limpa.  
**Decisão MVP:** saída reabre somente após eliminar todos os inimigos do conjunto inicial do encontro, inclusive inimigos fora da tela. Depois, transição só ocorre quando todos os jogadores vivos estão na mesma área da mesma saída da sala; espectadores mortos não contam. Sem reforços dinâmicos; estado rastreia IDs de inimigos restantes, não apenas contagem de nós.  
**Efeito:** governa estado da sala, atividade de IA e persistência ao descarregar/recarregar. Corrige o exemplo anterior do GDD que permitia recuar em combate.  
**Critério:** combate deve estar limpo e todos os jogadores vivos na mesma área da mesma saída; um jogador vivo basta quando o parceiro é espectador, enquanto vivo fora da área ou vivos em saídas distintas impedem transição. Revisitar sala limpa preserva inimigos mortos, loot coletado e itens deixados no chão com seus IDs/posições; cobertura preserva integridade. Só rollback integral do checkpoint restaura baseline.

### D04 — Planos de altura — confirmado em parte

**Decisão:** uma sala pode conter múltiplos planos espaciais reais, e a conexão entre salas usa o plano normal. Altura afeta defesa e linha de visão.  
**Decisão:** no MVP, trocar de plano somente por rampas, escadas ou passagens específicas autoradas; sem salto ou queda livre. Camadas discretas são proposta técnica, sem física 3D automática.  
**Decisão:** planos podem trocar tiros quando há linha de visão; barrancos e bordas bloqueiam pela oclusão geométrica. Altura maior não concede imunidade por si só. No MVP, geometrias navegáveis em alturas diferentes não ocupam XY sobreposto (não há travessias por cima/baixo do mesmo footprint); múltiplos planos ligados por rampas/escadas continuam permitidos.
**Efeito:** posição lógica precisa de `room_id` e `elevation_id`, além de XY; geometria/travessia pertence a `world`, e `combat` consulta política compartilhada de visão e cobertura.  
**Critério candidato:** movimento só muda de plano em transição autorizada; tiro entre planos segue linha de visão/oclusão geométrica igual para jogador e IA.

### D05 — Defesa e resposta a dano — parcialmente confirmada

**Decisão:** posicionamento, cobertura, agachar e altura são defesas do MVP; dash fica fora do MVP. Obstáculos altos/bulky protegem geometricamente conforme LOS, altura e direção. Obstáculos baixos protegem totalmente de tiros que bloqueiam quando personagem está agachado. Materiais frágeis perdem integridade gradualmente com impactos até quebrar.  
**Decisões:** para atirar por cima de cobertura baixa, personagem deve levantar; ainda pode disparar agachado em área livre ou por ângulo sem cobertura. Disparo agachado atrás de cobertura com precisão muito baixa é intenção pós-MVP. Impacto que quebra cobertura é absorvido; tiros seguintes podem atravessar a abertura. Cobertura quebrada não bloqueia tiros nem movimento. **Decisão:** não há invulnerabilidade pós-dano; impactos válidos distintos podem causar dano. Acerto provoca breve desequilíbrio e redução de movimento, mantendo mira disponível. **Pendente:** duração/intensidade e regra de acumular ou renovar o efeito; postura/velocidade/mira ao agachar, números de integridade/dano e letalidade. Atordoamento total não faz parte do MVP.  
**Efeito:** postura, geometria, material e integridade de cobertura participam da consulta compartilhada de LOS/dano; world é dono do estado de cobertura e combat consulta antes de aplicar impacto.  
**Critério candidato:** cobertura intacta protege 100% dos disparos que geometricamente bloqueia na postura protegida; flancos/planos sem oclusão permanecem vulneráveis. Dano e recuperação são legíveis.

### D06 — Resolução de disparos — confirmado em parte

**Decisão:** jogador e inimigos usam hitscan. Traçantes são VFX, sem deslocamento balístico nem dano atrasado. Ataques inimigos anunciam linha, ângulo e intervalo antes do disparo.  
**Decisão:** inimigos só disparam hitscan depois de entrar na tela e anunciar o ataque; podem se mover fora da tela. Jogadores fora do viewport continuam podendo atacar e receber dano, respeitando alcance, linha de visão e altura. **Pendente:** granadas e explosivos. Granadeiro não é inimigo obrigatório do MVP.
**Efeito:** um resolvedor comum avalia acerto, cobertura e altura para player e IA; efeitos visuais não aplicam dano.  
**Critério candidato:** cada disparo aplica dano no máximo uma vez e respeita a mesma política de oclusão; telegraphs dão informação suficiente para resposta.

### D07 — Economia de recursos — em aberto

**Decisões:** uma alternativa de emergência de melee, com baixo dano e exposição ao perigo, está confirmada; cargas/stamina não foram definidas. Cada setor usa tipos e quantidades de suprimentos predefinidos, com localização aleatória dentro do orçamento. No MVP, não ajustar a oferta de suprimentos conforme vida/munição do jogador; orçamento de ameaça pode considerar o grupo.  
**Decisões:** suprimentos incluem kits de cura armazenados para uso manual, com aplicação temporizada e vulnerável; munição reserva usa categorias compartilhadas por armas compatíveis, separadas dos carregadores por arma. Suprimentos por setor têm tipos/quantidades predefinidos; a localização é aleatória, sem ajuste à vida/munição no MVP.
**Decisões:** durante aplicação temporizada do kit, jogador move-se lentamente e não dispara; permanece vulnerável. Dano recebido cancela e consome um kit sem curar. Conclusão consome um kit uma vez e cura valor configurado sem exceder vida máxima. Recarga antecipada descarta munição restante do carregador, sem devolver à reserva.
**Decisões:** cura também pode ser cancelada manualmente, perdendo o kit sem curar. Recarga pode ser cancelada ao trocar de arma; o restante do carregador é descartado no início, uma única vez, e não retorna à reserva. Recarga cancelada não completa o carregador nem debita reserva posteriormente.
**Pendente:** valores de munição/cura, número de kits, duração e quantidade curada, se trocar arma cancela kit, capacidades/quantidades por categoria e outras interrupções de recarga. Intenção pós-MVP: avaliar assistência adaptativa, sem implementá-la agora. Não inferir munição infinita ou farming de melee.
**Efeito:** economia e distribuição continuam parâmetros explícitos; gerador não lê vida/munição para adaptar oferta de suprimentos.  
**Critério candidato:** tipos/quantidade de suprimentos por setor ficam invariantes sob estados diferentes de vida/munição; melee exige alcance e cadência, causa dano baixo e deixa o jogador vulnerável a hitscan.

### D08 — Equipamento durante a run — confirmado em parte

**Decisão:** até duas armas de fogo alternáveis por jogador além de melee; nova run inicia com pistola e recursos predefinidos; fuzil e escopeta são armas disponíveis como loot, sem garantir ambas em toda run. O loadout inicial não é reaplicado ao entrar em sala, restaurar checkpoint ou retornar parceiro. Equipamentos ocupam slots limitados e oferecem vantagens com desvantagens.
**Decisões adicionais:** kit de cura temporizado com movimento lento, sem disparo e consumo sem efeito de cura se dano ou cancelamento manual interrompem; recarga antecipada descarta munição remanescente no início sem reembolso à reserva, e trocar de arma cancela a recarga.
**Pendente:** demais limites/compatibilidades e capacidades por arma/categoria, duração/quantidade de cura, se trocar arma cancela kit e outras interrupções de recarga. Perks ou modificadores de run são uma possibilidade pós-MVP.
**Pós-MVP:** outros modos podem receber outro conjunto inicial via configuração de run, sem construir sistema de seleção de loadout agora. **Efeito:** inventário modela slots e kit de cura armazenado por jogador; reservas de munição são nas três categorias pistola/fuzil/escopeta e carregadores por arma; quantidades/capacidades seguem balanceamento. Definições permanecem separadas do estado carregado.
**Critério candidato:** conteúdo explica o tradeoff e uma rota não depende de item aleatório obrigatório; recarga conserva o invariante entre munição carregada, reserva, disparos e balas descartadas.

## Cooperação e progressão entre runs

### D09 — Coop local — confirmado em parte

**Decisões:** menu oferece Solo/Coop; coop com dois participantes é configurado antes da run, sem entrada dinâmica. Solo usa teclado/mouse ou gamepad; coop usa teclado/mouse+gamepad ou dois gamepads, com input isolado por dispositivo. Dois jogadores em tela compartilhada; munição e cura individuais; fogo amigo ligado para hitscan e melee do jogador; sem reanimação no MVP. Se um morre, sobrevivente continua e morto acompanha como espectador. Ao alcançar o próximo setor, parceiro retorna com os equipamentos que tinha ao morrer e mínimos predefinidos de vida e reserva de munição por categoria compatível; armas da mesma categoria não multiplicam o mínimo. Se ambos morrem, a equipe restaura o checkpoint.
**Decisões:** pickup único, inclusive munição e cura, vai ao primeiro jogador que interagir explicitamente; não é automático por proximidade e o grupo não recebe duplicata ou partilha. Interações no mesmo tick seguem ordem estável e concedem uma vez, sem prioridade fixa por jogador.  
**Decisões:** desconexão de controle pausa a partida inteira até reconectar o mesmo player_id, permitindo atualizar o identificador do dispositivo validado; reconexão não revive, reseta nem duplica ator, inventário ou recursos. Pausas manuais e por desconexão têm razões simultâneas; remover uma razão não retoma enquanto outra persistir. **Pendências:** valores numéricos mínimos de vida/munição, eventual multiplicador/redução do dano aliado, inventários/equipamentos separados (proposta) e tratamento quando concessão falha por capacidade cheia. Recomenda-se não consumir o pickup em concessão rejeitada, sem troca automática. Não alterar recursos do sobrevivente nem repor kits/consumíveis automaticamente. Ordem técnica recomendada: retornar parceiro antes de capturar checkpoint do novo setor.
**Efeito:** sessão guarda equipamento do morto por IDs/valores após destruir sua cena, associa mínimos configuráveis no retorno e restaura equipe após wipe, sem revive em sala comum.  
**Critério candidato:** matriz de dispositivos permite teclado/mouse ou gamepad em solo, e teclado/mouse+gamepad ou dois gamepads no coop, sem input cruzado e recusando atribuição duplicada de dispositivo; morte individual mantém sobrevivente e recursos inalterados; retorno ocorre uma vez com equipamento retido e mínimos válidos; tiro/melee pode danificar parceiro, mas cobertura/plano ocluído impede acerto; cada impacto aplica dano uma vez; wipe restaura baseline sem reaparecimentos intermediários; desconexão durante timers congela simulação e reconexão retoma o mesmo participante; saída exige todos vivos na mesma área da mesma saída.

### D10 — Checkpoint e persistência — parcialmente confirmada

**Decisões:** derrota solo retorna ao checkpoint atual em memória; a partida não salva nem suspende uma run entre execuções. Avançar de setor é irreversível no MVP: o checkpoint anterior é substituído na entrada do novo setor. Em coop, morte individual deixa espectador e sobrevivente ativo; ao avançar, o parceiro retorna uma vez antes da captura do novo checkpoint; wipe restaura toda a equipe ao snapshot atual. Não há progressão permanente entre partidas no MVP: toda nova run começa com as mesmas condições iniciais, sem herdar desbloqueios/atributos da run anterior; seed e drops podem variar. Gravação de perfil/configuração é questão separada e sua política segue pendente.  
**Decisão MVP:** capturar snapshot completo na entrada de cada setor. Restauração devolve vida atual, munição, equipamento/inventário e estado de mundo (layout/seed/RNG, inimigos, loot, portas e progresso) exatamente ao snapshot. Ganhos e gastos posteriores são desfeitos; não há cura total automática nem reset de setores já concluídos.  
**Pós-MVP:** em setores muito grandes, podem existir pontos adicionais de checkpoint; quantidade e localização podem variar por dificuldade, sem implementar esse sistema agora.  
**Pendências:** momento exato da captura em relação a efeitos de entrada; os valores dos mínimos de retorno seguem D09. Recomenda-se retornar o parceiro antes da nova captura, sem escolher heal ou reposição de kits.  
**Efeito:** `RunSession` possui snapshot imutável em memória, sem `Node` nem alias a recursos mutáveis. Coordena uma restauração idempotente e atômica de `world`, atores e inventários; `restoring_checkpoint` evita game over irreversível durante a operação. Não importar progresso permanente entre runs no MVP. Perfil/configuração salvo pode ser tratado separadamente, sob regra ainda pendente.  
**Critério candidato:** não duplicar recursos, não rerollar layout/encontros/loot/RNG, desfazer mudanças pós-snapshot, conservar setores concluídos e repetir restore sem aplicar estado duas vezes. Após a transição, não há rota de volta nem wipe para checkpoint anterior; o novo checkpoint captura o grupo após o retorno individual, sem conceder suprimentos extra. Nova run inicia sem desbloqueios/atributos persistentes e com a carga inicial predefinida; run não é lida/escrita em disco entre execuções.

## Geração e alvo técnico

### D11 — Salas autorais e variação procedural — confirmada com extensão futura

**Decisão MVP:** grafo procedural de salas autorais com caminho principal início-saída e ramos conectados de salas opcionais/rotas alternativas. Setor permanece com 4–6 salas totais, ramos incluídos; manter caminho alcançável sem exigir arma de loot específica. Inimigos/equipamentos variam aleatoriamente conforme budget.  
**Proposta pós-MVP:** salas podem declarar conteúdo base ou pontos variáveis; cada instância autorada pode usar modo `inherit`, `force_empty` ou `force_content` com IDs estáveis. Não mutar recursos globais nem alterar conteúdo autorado durante runtime. Esta extensão não substitui nem reduz a aleatoriedade aprovada para o MVP, e não implementa um framework agora.  
**Decisão de revisita:** sala limpa no mesmo setor preserva inimigos mortos, pickups coletados e itens no chão por ID/posição, além do estado de cobertura; somente rollback completo do checkpoint atual reverte tudo. Avançar entre setores é irreversível no MVP, então revisita só ocorre dentro do setor atual. **Efeito:** grafo compõe dados autorais; RoomState e loot de chão não dependem da vida do Node.
**Ambientes MVP confirmados:** setor 1 começa à beira da praia no fim da tarde e segue para uma trincheira relativamente ensolarada; setor 2 são trincheiras em noite chuvosa. Ambos mantêm as 4–6 salas já definidas por setor. Chuva é visual/sonora no MVP, sem relógio contínuo, física de água ou obscurecimento injusto. Outras regiões permanecem propostas futuras.
**Critério candidato:** mesma seed/configuração/versão repete composição lógica; fixtures de ramo opcional são alcançáveis sem tornar item obrigatório; revisitar preserva estado sem duplicar inimigos/loot; checkpoint desfaz estado de sala e inventário juntos.

### D12 — Plataforma, linguagem e desempenho — confirmados em parte

**Decisões:** alvo Windows e Linux em computador médio, mínimo desejado de 30 FPS durante gameplay; Godot 4.7 e GDScript tipado. Patch recomendado: Godot 4.7.2-stable, indicada como estável no arquivo oficial consultado em 2026-10-23 ([arquivo oficial](https://godotengine.org/download/archive/)); fixar patch exato ao configurar o projeto.  
**Pendente:** CPU/GPU/RAM, resolução, carga de estresse, patch exato e condições de medição. 30 FPS corresponde a cerca de 33,3 ms por frame; executar a mesma suíte headless nos dois sistemas, fazer smoke test dos exports e medir execução sustentada, média e p95 em solo e coop, sem exigir cap. Simulação deve ser independente da taxa de renderização.  
**Efeito:** define exports, input, perfil de carga e orçamento de desempenho; não presume versão patchada/configuração antes de fixar setup.  
**Critério candidato:** slice sustenta meta no hardware de referência aprovado nos dois exports e combinações coop de dois gamepads ou teclado/mouse+gamepad.

### D13 — Câmera coop — confirmada em parte

**Decisão:** câmera compartilhada acompanha os jogadores vivos e permite zoom limitado para salas maiores; jogadores mortos em espectador não atraem o enquadramento.
**Pendente:** valores de zoom/margem. Jogadores podem sair do viewport com indicadores de direção; não limitar movimento, fazer split-screen nem teleportar câmera. Centro/alvo de enquadramento ainda é escolha técnica a validar.
**Critério candidato:** zoom limitado em salas grandes e indicadores de direção legíveis quando parceiro sai do viewport; espectadores não atraem câmera.

### D14 — População dos encontros — confirmado em parte

**Decisão MVP:** fuzileiro, flanqueador e pesado compõem o roster de inimigos; todos são posicionados na inicialização e não há reforços dinâmicos. Fuzileiro pressiona coberturas, flanqueador busca outros ângulos válidos, pesado ameaça coberturas via impactos normais de sua arma. Granadeiro é candidato futuro; elites e chefes não são requisito.
**Decisão:** ataques hitscan inimigos só acontecem após inimigo entrar na tela e anunciar; movimento da IA pode continuar fora do enquadramento. Jogadores podem atacar de/para fora da tela e receber dano; inimigos em tela podem atingir jogadores fora dela quando alcance, linha de visão e altura permitem.
**Efeito:** estado da sala registra IDs da população inicial; descarregar/recarregar não cria entidades adicionais fora da restauração de snapshot.
**Critério candidato:** roster/configuração do encontro surge uma vez por seed; fuzileiro pressiona posições protegidas, flanqueador busca ângulos válidos e pesado reduz integridade de cobertura por impactos normais sem ignorar material/geometria; inimigos não reaparecem ao recarregar a sala.

### D15 — Direção visual e briefs — confirmada em parte

**Decisões visuais:** guerra fictícia inspirada na primeira metade do século XX; proporções compactas semirrealistas próximas à referência; pixel art de alto detalhe, com texturas densas. Recorte ambiental do MVP permanece o já aprovado para os dois setores.
**Propostas:** composições, famílias de paleta, silhuetas concretas de uniformes/armas, marcadores visuais P1/P2 e propriedades de props/material devem ser exploradas nos [briefs textuais](art/README.md). A referência continua exploratória de uma região e não determina o estilo visual de todo o jogo.
**Pendências:** paleta final, ângulo de câmera, escala/resolução/tile grid, uniformes e marcas específicas de facção, dimensões de sprite e animação. Nenhuma imagem foi produzida nesta etapa.
**Critério candidato:** em ambos os ambientes do MVP e no zoom de gameplay a definir, distinguir jogador, parceiro, inimigo, bala, cobertura, pickup e saída em thumbnail e escala de cinza; detalhe denso não pode comprometer silhueta nem telegraph.

## Requisitos funcionais essenciais do slice

- **R01 — Sessão:** começar, pausar, retomar e avançar dois setores de 4–6 salas até destino; capturar checkpoint na entrada e restaurar solo/wipe conforme D10; liberar saída após combate limpo e requisito de presença de vivos em D03.
- **R02 — Participantes:** menu Solo/Coop; coop configura dois jogadores antes da run, sem entrada dinâmica; input isolado, câmera compartilhada limitada que acompanha apenas jogadores vivos e munição/cura individuais.
- **R03 — Combate:** disparos hitscan aplicam dano e consultam cobertura/altura; VFX não aplica dano.
- **R04 — Espaço:** salas podem conter planos reais sem sobreposição XY de footprints navegáveis; no MVP, transições são por rampas, escadas e passagens específicas autoradas, sem salto/queda livre; saída de sala segue D03.
- **R05 — Geração:** grafo tem caminho principal alcançável e ramos conectados opcionais no orçamento total de 4–6 salas/setor; itens aleatórios não são requisito de saída; RoomState preserva visitas até rollback do checkpoint.
- **R06 — Recursos e conteúdo:** melee de emergência, baixo dano/exposição; suprimentos predefinidos por setor, localização aleatória e sem ajuste à vida/munição no MVP; pickup não duplica.
- **R07 — Checkpoint:** snapshot completo na entrada de cada setor; solo derrota e coop wipe restauram baseline imutável/idempotente em memória, sem save de run entre execuções.
- **R08 — Persistência:** não há metaprogressão entre runs no MVP; perfil/configuração salvo é um escopo separado e sua política ainda deve ser definida.
- **R09 — Desempenho:** medir gameplay em solo e coop contra alvo mínimo e hardware que serão definidos em D12.

## Cobertura e critério de entrega

O slice deve demonstrar dois setores com 4–6 salas cada até um destino em solo e coop local, sem exigir extração/chefe. Critérios manuais verificam clareza dos telegraphs hitscan, regras entre planos, cobertura, câmera, input, transição de sala e desempenho. Inimigos só disparam após entrar na tela e anunciar ataque. Jogadores fora do viewport ainda atacam e recebem dano segundo alcance/LOS/altura.

Quando houver código, versionar testes significativos para seed/configuração e versão; grafo de dois setores com 4–6 salas; validar fixture multi-plano sem sobreposição e rejeitar fixture com footprints navegáveis sobrepostos; fixtures de grafo com ramo alcançável e sem caminho obrigatório por loot raro; revisita preserva mortes, loot coletado/no chão e cobertura, enquanto checkpoint restaura inventário/mundo juntos; melee e suprimentos invariantes à vida/munição; transições de altura; hitscan sem dano duplicado; pickup concedido à primeira interação válida, com concorrência no mesmo tick resolvida uma vez sem prioridade fixa por jogador; input isolado; snapshot sem alias; rollback total sem reroll nem perda de setores concluídos; restore idempotente de vida, munição, inventário/equipamento, inimigos, loot, portas, progresso e RNG; morte individual em espectador, retorno no próximo setor e wipe da equipe; fogo amigo em hitscan/melee, bloqueio por cobertura/plano e aplicação única por impacto; sessão/checkpoint sem save de run em disco. Testar retorno único com equipamento retido e mínimos válidos de vida/munição, limites de vida e capacidade por arma, sobrevivente inalterado, wipe e sem reposição de kits. Medir desempenho em benchmark, não como teste unitário. Nenhum teste foi executado nesta revisão.
