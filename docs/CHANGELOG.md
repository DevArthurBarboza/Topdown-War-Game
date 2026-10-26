# Changelog

## 2026-10-23

- Adicionado `docs/GDD.md` v0.1, proposta inicial do shooter top-down em pixel art. Define pilares, loop de run, geração de salas, combate, inimigos, direção de arte, MVP, critérios de aceitação e decisões pendentes; separa requisitos fornecidos de propostas.
- Registrada a referência conceitual fornecida pelo usuário em `docs/references/concept-art.png`, sem atribuir origem ou licença não confirmadas.
- Esclarecida a orientação do usuário: a imagem é exploratória para uma possível região de trincheiras e não define o visual global. Acrescentada matriz de quatro regiões com climas combináveis, ênfases de encontro e navegação; reorganizado o roadmap para consolidar o GDD antes de briefs e concept art por categoria/região, seguida de protótipo/vertical slice. Título, guerra fictícia e exclusão de iconografia histórica são identificados como propostas, não requisitos definidos. Nenhuma concept art foi produzida nesta etapa.
- Validação: revisão documental de estrutura e escopo. Não há código ou suíte de testes no scaffold; nenhum teste foi executado.

## Atualização v0.2 — 2026-10-23

- Registrados Godot como engine confirmada, desenvolvimento dos componentes próprios do jogo do zero e modos solo e coop local como requisitos. PC, dois jogadores, combinações de controles e regras iniciais de coop permanecem propostas onde indicado; online fica fora do MVP.
- Adicionadas regras de coop local, critérios de aceitação e validação manual/testes futuros para roteamento de dispositivos, pickups simultâneos, morte do grupo, transições, loot e escala de ameaça. Adicionada estrutura técnica proposta para Godot com referências oficiais.
- Atualizado o escopo do MVP para completar uma run tanto em solo quanto em coop local. Versão da engine e linguagem seguem pendentes; GDScript é apenas uma opção.
- Validação: revisão documental; não há código e nenhum teste foi executado.

## Atualização v0.3 — 2026-10-23

- Adicionado `docs/ARCHITECTURE.md` com estrutura modular por feature, responsabilidades, direção de dependências, fluxo de disparo, coop local, estado e dados, geração reproduzível, testes futuros e sequência de implementação.
- Adicionado `rules/architecture.md` com 15 regras acionáveis para orientar futuras mudanças de código.
- Atualizada a seção técnica do GDD para recomendar GDScript tipado sem fixar linguagem ou versão da Godot, e ligar os documentos de arquitetura.
- Validação: revisão documental de coerência e links relativos; nenhum código ou teste foi executado.

## Roteiro de decisões centrais — 2026-10-23

- Adicionado `docs/CORE_DECISIONS.md` com 12 grupos de decisões pendentes, recomendações claramente candidatas, efeitos arquiteturais, critérios verificáveis e requisitos essenciais do primeiro slice.
- Atualizada a seção de decisões do GDD para apontar ao roteiro sem aprovar recomendações nem alterar regras de gameplay.
- Validação: revisão documental de coerência com GDD e arquitetura; nenhum código ou teste foi executado.

## Atualização v0.4 — 2026-10-23

- Atualizados GDD e decisões centrais para registrar ritmo tático ágil, fluxo setorial até destino indefinido, saídas bloqueadas durante combate, planos reais dentro de salas, tiro entre planos quando há linha de visão, defesa por posicionamento/cobertura sem dash no MVP, hitscan para ambos os lados, coop local confirmado para dois jogadores sem reanimação no MVP, slots de equipamento e geração de salas autorais com encontros/itens aleatórios.
- Mantidos em aberto economia, resultado quando um jogador coop morre, persistência/suspensão, detalhes de XY sobreposto e transições entre planos, quantidade/conteúdo, sistemas operacionais e hardware de referência. Godot 4.7 foi confirmada; Godot 4.7.2-stable é recomendação de patch com base no arquivo oficial consultado em 2026-10-23; fixar patch no setup.
- Atualizada arquitetura e regras para posição `room_id`/XY/`elevation_id`, transições autoradas, resolução compartilhada de visão/cobertura para hitscan, VFX sem dano, overrides de conteúdo futuro por instância e benchmark sustentado de 30 FPS (média/p95, solo/coop) sem cap obrigatório.
- Validação: revisão documental de consistência; nenhum código ou teste foi executado.

## Atualização v0.5 — 2026-10-23

- Confirmado MVP de dois setores com 4–6 salas cada; removida a duração estimada não aprovada. RunDefinition/SectorDefinition concentram limites; futuras variações por dificuldade/eventos ficam configuráveis, sem mudança silenciosa ou novo framework agora.
- Confirmado melee fraco/arriscado e suprimentos predefinidos por setor com localização aleatória dentro do orçamento; sem ajuste MVP da oferta conforme vida/munição. Quantidades econômicas seguem pendentes; assistência adaptativa é intenção pós-MVP.
- Confirmado checkpoint em memória para derrota solo, sem save/suspensão de run entre execuções; perfil/configuração seguem separados. Coop individual mantém sobrevivente ativo e morto espectador, sem reanimação no MVP. Wipe, local/escopo do checkpoint, conteúdo do snapshot e retorno do parceiro permanecem perguntas abertas; recomendação de fotografia completa está marcada como candidata.
- Atualizados arquitetura e regras com snapshot imutável/idempotente, restauração coordenada, configuração reproduzível de setores e testes futuros para economy, melee, snapshot e restore.
- Validação: revisão documental de coerência; nenhum código ou teste foi executado.

## Atualização v0.6 — 2026-10-23

- Confirmado checkpoint do MVP na entrada de cada setor; removida a pergunta sobre sua localização básica. Setores grandes podem ter pontos adicionais pós-MVP, com localização/quantidade configuráveis por dificuldade, sem implementação nesta etapa.
- Atualizado CORE_DECISIONS e arquitetura para manter pendentes o momento da captura, conteúdo/restauração do snapshot e regra de wipe coop. Corrigido critério do slice para os dois setores com 4–6 salas cada.
- Validação documental apenas; nenhum código ou teste foi executado.


## Atualização v0.7 — 2026-10-23

- Confirmada restauração completa do snapshot na derrota solo e no wipe coop: vida registrada, inventário/equipamento e mundo retornam ao estado capturado; sem cura automática, reroll de conteúdo ou reset de setores concluídos.
- Confirmadas as regras de morte coop: sobrevivente segue, jogador morto especta e volta quando o sobrevivente alcança próximo setor; wipe restaura a equipe inteira. Recurso do parceiro ao retornar e ordem relativa à nova captura seguem pendentes/candidatas.
- Atualizados critérios e arquitetura para rollback idempotente e testes futuros de estado completo. Nenhum código ou teste foi executado.

## Atualização v0.8 — 2026-10-23

- Definido retorno do parceiro espectador ao chegar o sobrevivente ao próximo setor: conserva equipamento que tinha ao morrer e recebe mínimos predefinidos de vida/munição, com valores numéricos pendentes. Sobrevivente não é alterado; kits/consumíveis não são repostos automaticamente.
- Wipe continua restaurando o snapshot completo do grupo, sem aplicar os mínimos de retorno individual. Atualizados arquitetura, regras e testes futuros para reter equipamento por IDs/valores e fazer retorno idempotente antes do próximo snapshot, como ordem técnica recomendada.
- Validação documental apenas; nenhum código ou teste foi executado.


## Atualização v0.9 — 2026-10-23

- Fogo amigo ligado no MVP: hitscan e melee do jogador podem danificar parceiro pela mesma política de acerto/oclusão/cobertura/planos e usando o dano configurado pelo ataque.
- Retirada a decisão pendente sobre fogo amigo; multiplicador/redução e dano ao próprio autor não foram inferidos. Registrados testes futuros de acerto aliado, oclusão e aplicação única por impacto.
- Validação documental apenas; nenhum código ou teste foi executado.

## Atualização v0.10 — 2026-10-23

- Pickup único, incluindo munição e cura, passa ao primeiro jogador que interagir explicitamente; interações simultâneas têm resolução estável e concessão única, sem prioridade fixa por jogador. Coleta por proximidade e duplicação/partilha automática ficam excluídas.
- Tratamento de concessão rejeitada por capacidade permanece pendente; preservar o pickup é recomendação técnica, sem troca automática.
- Atualizados GDD, decisões, arquitetura e regra de arquitetura. Revisão documental apenas; nenhum código ou teste foi executado.


## Atualização v0.11 — 2026-10-23

- Confirmado limite de duas armas de fogo alternáveis além de melee, kit de cura armazenado e usado manualmente com aplicação vulnerável, e reserva de munição por categoria separada dos carregadores por arma.
- Registradas como pendentes capacidades e valores, uso/movimento durante a cura e suas regras de interrupção/consumo. Incluídos estados de munição e cura nos checkpoints e critérios de verificação futuros.
- Atualizados GDD, decisões, arquitetura e regras. Revisão documental apenas; nenhum código ou teste foi executado.

## Atualização v0.12 — 2026-10-23

- Cura manual agora permite movimento lento, bloqueia disparo e é cancelada por dano, consumindo o kit sem cura; ao completar, cura uma vez até a vida máxima.
- Recarga antecipada descarta balas restantes do carregador em vez de devolvê-las à reserva. Momento de descarte/cancelamento da recarga e detalhes numéricos de cura permanecem pendentes.
- Atualizados GDD, decisões, arquitetura e regras, incluindo estados e critérios de validação. Revisão documental apenas; nenhum código ou teste foi executado.

## Atualização v0.13 — 2026-10-23

- Confirmada câmera compartilhada com zoom limitado em salas maiores, acompanhando jogadores vivos e mostrando indicadores direcionais quando saem da tela; movimento não é limitado para caber no viewport.
- Confirmadas transições de altura somente por rampas, escadas e passagens autoradas no MVP, sem salto/queda livre. Encontros iniciam com toda a população posicionada; reforços sinalizados ficam como intenção pós-MVP.
- Inimigos só disparam hitscan após entrar na tela e anunciar ataque, embora possam mover-se fora dela. Regras de mira/disparo de jogador fora da tela seguem pendentes. Atualizados GDD, decisões, arquitetura e regras; revisão documental, sem testes executados.

## Atualização v0.14 — 2026-10-23

- Jogadores fora do viewport continuam atacando e recebendo dano normalmente, sob alcance, linha de visão e altura; câmera não limita input nem concede imunidade. Inimigos ainda só disparam após entrar na tela e anunciar ataque.
- Corrigido o resumo de decisões espaciais: tipos de transição de altura já estão definidos; XY sobreposto permanece pendente.
- Atualizados GDD, decisões, arquitetura e regras. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.15 — 2026-10-23

- Definido que portas reabrem após eliminar todos os inimigos do conjunto inicial, inclusive fora da tela; controles de solo e combinações de dispositivos no coop foram confirmados.
- Cobertura no MVP inclui agachar, proteção geométrica completa por obstáculos baixos na postura agachada e degradação de materiais frágeis até quebra; detalhes de tiro que quebra e navegação após quebra seguem pendentes. Destruição total do cenário permanece fora do MVP.
- Atualizados GDD, decisões, arquitetura e regras. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.16 — 2026-10-23

- Definido que disparar por cima de cobertura baixa exige levantar; agachar ainda permite disparos em áreas/ângulos livres. Blindfire agachado fica como intenção pós-MVP.
- O impacto que quebra cobertura é absorvido; cobertura quebrada deixa de bloquear tiros e movimento. Atualizados regras de resolução, estado mutável, snapshot e critérios futuros.
- GDD, decisões, arquitetura e regras atualizados. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.17 — 2026-10-23

- Confirmado que não há invulnerabilidade após dano; cada impacto válido distinto causa dano, com deduplicação por ID. Acerto gera desequilíbrio breve que reduz movimento e mantém mira.
- Nova run começa com uma arma padrão e recursos predefinidos por jogador; segunda arma é encontrada. Conjuntos iniciais alternativos ficam para modos futuros, sem seletor no MVP.
- Atualizados GDD, decisões, arquitetura, regras e critérios futuros. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.18 — 2026-10-23

- Deduplicação de dano especificada por par impacto/alvo, preservando dano de impactos distintos do mesmo ataque.
- Definido que footprints navegáveis de planos diferentes não se sobrepõem no MVP, embora continuem existindo múltiplas alturas e transições por rampas/escadas.
- Confirmada ausência de metaprogressão entre runs: cada nova run inicia com arma e recursos padrão, sem herdar progressos. Perfil/configuração salva permanece decisão separada; nenhuma implementação ou teste foi feito.

## Atualização v0.19 — 2026-10-23

- Confirmados GDScript tipado e exports Windows/Linux para o MVP. Registradas convenções de nomes/tabs, suíte headless nos dois sistemas, smoke test dos exports e combinações coop aprovadas.
- Atualizado AGENTS.md: scaffold atual tem documentação e imagem de referência, sem projeto Godot, runtime, testes ou comandos configurados; layout futuro documentado em ARCHITECTURE.md.
- GDD, decisões, arquitetura, regras e guia atualizados. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.20 — 2026-10-23

- Coop configurado no menu antes da run, sem entrada dinâmica. Desconexão pausa toda a sessão; reconectar associa o mesmo jogador sem revive/reset/duplicação.
- Saída exige combate limpo e todos os vivos na mesma área da mesma saída; espectadores são excluídos. Registrada validação transacional de sessão e testes futuros para razões concorrentes de pausa e gates.
- GDD, decisões, arquitetura e regras atualizados. Revisão documental apenas; nenhum teste foi executado.

## Atualização v0.21 — 2026-10-23

- Starter definido como pistola e recursos predefinidos; fuzil/escopeta são loot possível sem garantia de ambos, com reservas nas três categorias.
- Confirmado roster MVP de fuzileiro, flanqueador e pesado; o pesado degrada cobertura via impactos normais. Granadeiro, elite e chefe não são requisitos.
- Corrigida reconexão para preservar player_id permitindo reassociar device_id validado diferente. Atualizados GDD, decisões, arquitetura, regras e critérios futuros; nenhum teste foi executado.

## Atualização v0.22 — 2026-10-23

- Setores agora especificam grafo com rota principal e ramos opcionais/alternativos conectados, dentro do limite existente de 4–6 salas totais; a saída não depende de arma aleatória.
- Revisitas no mesmo setor preservam inimigos mortos, loot coletado ou ainda no chão e integridade de cobertura. Apenas rollback integral do checkpoint restaura o baseline de mundo e inventário.
- Atualizados GDD, decisões, arquitetura e regras. Revisão documental apenas; nenhum código ou teste foi executado.

## Atualização v0.23 — 2026-10-23

- Confirmado que avançar entre setores é irreversível no MVP; revisitas ficam limitadas ao setor atual e checkpoint anterior é substituído na entrada do próximo setor.
- Formalizada a ordem de transição: retorno individual do parceiro uma única vez, captura do novo checkpoint e descarte do estado anterior após commit; sem suprimentos extras ao avançar.
- Atualizados GDD, decisões, arquitetura e regras. Revisão documental apenas; nenhum código ou teste foi executado.

## Atualização v0.24 — 2026-10-23

- Cura manual pode ser cancelada, consumindo kit sem curar; troca de arma cancela recarga após descarte único do carregador no início, sem completar ou debitar reserva depois.
- Recarga parcial é recomendada quando há reserva compatível: preencher até capacidade ou munição disponível; rejeitar antes do descarte apenas quando não houver munição compatível.
- Criado `MVP_IMPLEMENTATION.md` com seis marcos, testes versionados em cada etapa de código, critérios de entrega, exclusões e pendências; briefs/concept art limitados às regiões e categorias selecionadas para o MVP. GDD seção 9 resumida em grupos e ligada ao plano.
- Confirmados os ambientes dos dois setores: praia no fim da tarde avançando para trincheiras relativamente ensolaradas; depois trincheiras em noite chuvosa. Registrado clima como apresentação visual/sonora, sem mecânica de água ou obscurecimento injusto.
- Atualizados GDD, decisões centrais, arquitetura e regras. Revisão documental apenas; nenhum código ou teste foi executado.

## Atualização v0.25 — 2026-10-23

- Confirmadas guerra fictícia inspirada na primeira metade do século XX, proporções compactas semirrealistas, pixel art de alto detalhe e texturas densas; marcas/facções específicas e demais parâmetros visuais seguem em revisão.
- Criados briefs textuais para dois ambientes, personagens, armas/itens, coberturas e efeitos. Ambientes incluem entradas/checkpoints e saídas legíveis; desgaste intermediário de cobertura não altera sua altura/oclusão.
- Atualizados GDD, decisões, plano e changelog. Nenhuma imagem, sprite, código ou teste foi produzido/executado.
