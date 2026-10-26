# Resumo da conversa — Topdown War Game

**Data:** 23 de outubro de 2026  
**Repositório:** `/home/arthur/coding/arthur/projects/topdown-war-game`  
**Status:** especificação e briefs textuais em evolução; ainda não há implementação jogável.

## Estado atual do repositório

O scaffold contém documentação em `docs/`, regras de contribuição em `rules/` e a imagem fornecida pelo usuário em `docs/references/concept-art.png`. Não há `project.godot`, runtime de gameplay, framework/testes executáveis, exports nem comandos configurados. Nenhum teste foi executado e nenhuma imagem de concept art ou sprite novo foi gerado.

Documentos atuais (caminhos relativos ao repositório): `AGENTS.md`, `docs/GDD.md` (v0.25), `docs/CORE_DECISIONS.md`, `docs/ARCHITECTURE.md`, `rules/architecture.md`, `docs/MVP_IMPLEMENTATION.md` e `docs/CHANGELOG.md`. Os briefs estão em `docs/art/README.md`, `ENVIRONMENTS.md`, `CHARACTERS.md`, `WEAPONS_AND_ITEMS.md` e `COVERS_AND_EFFECTS.md` dentro de `docs/art/`.

A imagem de referência foi fornecida para uma possível região de trincheiras, não como visual global. O plano atual é fechar e revisar briefs antes de produzir imagens/concepts e assets finais.

## Instruções de trabalho

Manter GDD, arquitetura, regras e changelog coerentes. Ao implementar, acrescentar testes unitários e de integração significativos, versionados junto ao código, evitando testes triviais de getters/setters; executar e registrar resultados em `docs/`. Comandos, framework de testes e CI não estão configurados; não presumir comandos. Layout futuro: `src/`, `content/`, `assets/`, `tests/`.

`AGENTS.md` orienta plano por modelo avançado e execução por modelo leve, com papéis explícitos. Esta etapa é documental: sem projeto, gameplay ou imagens.

## Plataforma e alvo

- Godot 4.7, GDScript tipado, Windows e Linux estão confirmados.
- Godot 4.7.2-stable foi recomendado com base no arquivo oficial consultado em 23/10/2026, mas o patch exato ainda deve ser fixado ao configurar o projeto.
- Meta: ao menos 30 FPS em computador médio, em solo e coop. Modelo CPU/GPU/RAM, resolução e cena de benchmark seguem pendentes. Medir média e p95; não impor cap como substituto da simulação correta.
- Rodar a suíte headless nos dois sistemas, fazer smoke test dos exports e validar coop nas combinações aprovadas.

## Decisões centrais de gameplay

- **Estrutura:** shooter top-down em pixel art, combate tático ágil, escassez e exploração por salas autorais em grafo procedural. Dois setores de 4–6 salas cada, contando ramos opcionais/alternativos; cada setor tem caminho alcançável e loot aleatório não pode ser obrigatório para chegar à saída. Revisitar salas limpas do setor atual preserva inimigos mortos, loot coletado/no chão e cobertura. Avançar entre setores é irreversível.
- **Objetivo:** chegar a um destino ainda indefinido; extração ou chefe não são obrigatórios. A run não tem duração aprovada. Não há metaprogressão permanente, save ou suspensão de run entre execuções; checkpoint existe em memória.
- **Setores MVP:** setor 1 começa à beira da praia no fim da tarde e avança para uma trincheira ainda relativamente ensolarada. Setor 2 ocorre em trincheiras durante noite chuvosa. Cada um conserva o orçamento existente de 4–6 salas. Perfis de clima são visuais/sonoros; sem exigir relógio contínuo de dia/noite nem física de água; preservar a leitura do combate.
- **Salas e saída:** todos os inimigos do encontro aparecem no início; a saída libera após eliminar todo o conjunto, inclusive inimigos fora de tela. Todos os jogadores vivos devem estar na mesma área da mesma saída; espectadores não contam. Transição e restauração são coordenadas pela sessão.
- **Solo/coop:** até dois jogadores, modo escolhido no menu antes da run; sem late join. Solo: teclado/mouse ou gamepad. Coop: teclado/mouse+gamepad ou dois gamepads. Player IDs persistem; ao desconectar qualquer controle, toda a simulação pausa. Reconexão pode reatribuir um device ID validado, sem revive, starter ou duplicação. Pausas manuais e por desconexão coexistem.
- **Câmera:** tela compartilhada com zoom limitado configurável; jogadores podem sair do viewport e recebem indicadores de direção. Sem split-screen, teleporte de câmera ou bloqueio de input por posição. Só jogadores vivos atraem a câmera.
- **Combate:** hitscan para jogador e inimigos; VFX de traçante não é projétil balístico. Inimigo só dispara depois de entrar na tela e anunciar ataque, mas pode mirar jogador fora do viewport se alcance/LOS/altura permitirem. Jogadores fora da tela continuam atacando e recebendo dano. Fogo amigo por tiro e melee está ligado; dano não é reduzido por regra aprovada e multiplicadores seguem pendentes.
- **Armas e recursos:** uma pistola padrão por jogador ao iniciar a run; fuzil/escopeta são loot e não têm aparição garantida. Até duas armas de fogo, além do melee fraco/arriscado que não ocupa slot. Reservas são por categoria (pistola, fuzil, escopeta) e carregadores por arma; armas da mesma categoria compartilham reserva. Recarga antecipada descarta o carregador no início; troca de arma cancela, sem reposição tardia. Proposta técnica: débito/preenchimento no término, carga parcial até min(capacidade, reserva); validar indisponibilidade antes do descarte quando não houver munição compatível. Cura: kit manual temporizado; jogador move-se lentamente, não dispara e permanece vulnerável. Dano ou cancelamento manual consome kit sem curar; troca de arma cancelando cura ainda está pendente. Quantidades, capacidades e tempos precisam de balanceamento.
- **Dano:** não há i-frames após acerto. Cada impacto/alvo distinto aplica dano; deduplicar por `(impact_id, target_id)`, não apenas por ataque global. Dano causa breve desequilíbrio e reduz movimento, mantendo mira. Duração/intensidade/stacking pendentes.
- **Espaço/cobertura:** vários planos reais por sala, transição apenas por rampas/escadas/passagens autoradas, sem salto/queda livre. Footprints de planos diferentes não se sobrepõem; conexões entre salas usam plano normal. Tiros cruzam planos quando há LOS; bordas/barrancos podem bloquear. Cobertura baixa dá proteção total na postura agachada quando oculta; é preciso levantar para atirar por cima, mas é permitido atirar agachado em espaço/ângulo livre. Coberturas frágeis degradam; representar desgaste intermediário de superfície sem alterar altura/oclusão é a proposta visual inicial, ainda sujeita à revisão. O tiro que quebra é absorvido; após quebrar, cobertura deixa de bloquear movimento e tiros. Isso não se estende à destruição total do cenário.
- **Checkpoints e morte:** capturado na entrada de cada setor. Derrota solo ou wipe restaura o snapshot completo atual (vida, munição, inventário/equipamento, sala/mundo, inimigos, loot, portas, seed/RNG), sem reroll nem cura automática. Morte individual deixa parceiro espectador e sobrevivente ativo. Ao avançar, parceiro retorna uma vez com equipamento da morte e mínimos predefinidos de vida/munição por categoria compatível; os valores numéricos seguem pendentes. Sobrevivente não é reabastecido. Wipe restaura ambos ao snapshot, não aos mínimos. Preparar o retorno antes do novo snapshot evita duplicação.

## Arquitetura proposta

Monólito modular por feature com composição de cenas/componentes Godot; sem framework ECS ou abstrações especulativas. `app` compõe sessão, input e fluxo de cenas. `combat` possui regras de vida/dano/LOS; `weapons` possui carregadores e estado de tiro; `inventory` possui kits/slots/pickups; `world` possui definições de salas/setores e `RoomState`; `RunSession` coordena participantes, transições e checkpoints. Estado de sala/loot/cobertura persiste por IDs/valores fora de Nodes descarregáveis. Resources `.tres` compartilhados são somente leitura durante runtime; estado mutável é por instância. `presentation` exibe eventos sem autoridade de gameplay. Dependências são compostas pelos pais e contratos explícitos; evitar `GameManager`, jogador global ou `EventBus` universal. Seed/configuração versionada garante composição lógica, não replay determinístico de física.

## Direção visual e briefs

Confirmado: guerra fictícia inspirada na primeira metade do século XX; proporções semirrealistas compactas; pixel art de alto detalhe e texturas densas próximas à referência. A referência mostra top-down com faces verticais, soldados compactos, trincheiras, lama/crateras, fortificações e luzes pontuais; não define todo o jogo. Os briefs atuais são texto; paletas, silhuetas concretas, marcas/facções, ângulo final, resolução/tile grid, tamanho de sprite e animações seguem em revisão. Afiliações específicas permanecem em aberto.

Os cinco documentos em `docs/art/` cobrem dois ambientes, jogador P1/P2 e três inimigos (fuzileiro pressiona posições protegidas, flanqueador busca outros ângulos, pesado ameaça destruir coberturas por impactos da arma), pistola/fuzil/escopeta/melee/munição/kit, e coberturas/efeitos. Não propor nado, novas classes, poderes, munições/explosivos, cura grátis ou mecânica ambiental não aprovada. A proposta inicial dos briefs mantém altura/oclusão durante o desgaste visual intermediário. Estão confirmados: o impacto que quebra é absorvido e a cobertura quebrada deixa de bloquear tiros e movimento. Sangue moderado é proposta pendente, não direção aprovada de gore.

## Plano de marcos

1. Revisar GDD e briefs; após isso, explorar concepts dos ambientes/categorias selecionados (ainda sem imagens produzidas nesta tarefa).
2. Fixar patch/setup, ferramenta de teste/CI e sessão/controles locais.
3. Arena de combate: movimento, armas, munição/cura, cobertura e feedback.
4. IA/clear de encontro com três arquétipos.
5. Grafo de dois setores, revisitas, checkpoints, rollback e coop.
6. Vertical slice, UI/acessibilidade, exports Windows/Linux, teste de controle e benchmark solo/coop.

Cada marco de código inclui testes unitários/integração versionados e registro de resultados. Critérios: `docs/MVP_IMPLEMENTATION.md`.

## Pendências reais

Balanceamento: vida/dano, cadência/precisão, capacidades de carregador, suprimentos, duração/quantidade da cura e mínimos de retorno. UX: se trocar arma cancela cura e demais políticas de interação/capacidade ainda marcadas. Conteúdo: destino/narrativa final, equipamentos além das armas/kit definidos e distribuição autorada. Arte: paletas finais, roupa/marcas específicas, ângulo de câmera, resolução/grid/sprites/frames. Toolchain: patch 4.7.x exato, framework/runner/CI, hardware de referência/resolução/carga de benchmark. Não confundir decisões já tomadas (dois setores e respectivos ambientes, 30 FPS, controles, roster e regras de cobertura) com essas pendências.

## Revisão visual no ambiente

A PNG de referência está em `docs/references/concept-art.png` (1448×1086, arquivo legível). Foi solicitada revisão visual via imagem/terminal; a inspeção visual foi feita pelo agente principal. Abrir a imagem com o aplicativo `code --reuse-window` apontando para o caminho absoluto terminou com código de saída 0; ainda não há confirmação do usuário de que a janela ficou visível. A abertura por `xdg-open` encaminhou o PNG ao Chrome, associação padrão identificada no ambiente; o usuário mostrou uma janela com falha, sem causa exata confirmada. O arquivo passou na verificação completa com PIL. O visualizador ImageMagick está instalado, mas a tentativa `display` falhou com `unable to open X server :1` na sessão automatizada. O usuário autorizou abrir a imagem; em seguida foi usado o VS Code, conforme o resultado acima. Não afirmar que o agente gerou ou visualizou uma nova imagem; até aqui não houve geração de assets.
