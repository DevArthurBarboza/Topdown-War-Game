# Plano de implementação do MVP

**Status:** plano de trabalho; nenhuma implementação iniciada  
**Escopo:** ordenar entregas e critérios do primeiro slice em Godot 4.7, GDScript tipado, Windows e Linux.

Este plano transforma as decisões centrais em marcos incrementais. O GDD continua como fonte de requisitos de jogo; [decisões centrais](CORE_DECISIONS.md) registram confirmações e pendências, [arquitetura](ARCHITECTURE.md) define limites de módulos e estado, e [regras de arquitetura](../rules/architecture.md) estabelecem convenções para código futuro. O status de proposta do GDD não significa que conteúdo e balanceamento estejam fechados.

## Marcos

### 0. Fechar documentação e direção visual

Revisar o GDD e os [briefs textuais de arte](art/README.md), que cobrem as duas áreas escolhidas: beira da praia no fim da tarde avançando para trincheiras relativamente ensolaradas no setor 1, e trincheiras em noite chuvosa no setor 2. Briefs descrevem construções, inimigos e equipamentos selecionados para o MVP. Concepts visuais são uma etapa futura de revisão; não produzir imagens nem sprites neste marco documental. **Entrega:** briefs consistentes como base de futuras referências visuais para placeholders. **Aceitação:** briefs mantêm leitura de jogadores, inimigos, balas, cobertura, itens e saídas; divergências voltam para revisão do GDD antes de virarem conteúdo.

### 1. Projeto, toolchain e sessão local

Configurar projeto Godot 4.7 com patch estável fixado, layout da arquitetura, comandos documentados no README e framework de testes compatível selecionado ao introduzir a toolchain. Criar bootstrap/RunSession, seleção Solo/Coop antes da partida, identidade estável de participantes, roteamento isolado de controle e pausa por desconexão. **Entrega:** cenas de teste simples sem combate. **Aceitação:** teclado/mouse ou gamepad em solo; teclado/mouse+gamepad ou dois gamepads em coop; desconexão congela simulação e reconexão associa device validado ao mesmo player sem duplicar ator ou starter. Nenhum runner ou comando está predefinido no scaffold atual.

Em cada marco que introduzir código, incluir testes unitários e de integração significativos e versionados junto à implementação, e registrar execução e resultado em `docs/`.

### 2. Arena de combate e recursos

Implementar movimento, mira, hitscan, melee, health/damage, reação breve sem invulnerabilidade, agachar, cobertura degradável, duas armas de fogo máximas, pistola inicial, categorias de munição, cura temporizada e efeitos de impacto. **Entrega:** sala autoral pequena com armas temporárias/placeholders e cobertura quebrável. **Aceitação:** tiros respeitam linha de visão, plano, altura, cobertura e fogo amigo; impacto que quebra cobertura é absorvido; cura interrompida por dano ou manualmente consome kit sem curar; troca de arma interrompe recarga após descartar o carregador uma vez, sem debitar reserva tardia; pausa congela ações/timers e restore não chama callbacks antigos.

### 3. IA e ciclo de encontro

Adicionar fuzileiro, flanqueador e pesado como definições com papéis distintos; povoar encontro inteiro no início, anunciar ataques hitscan somente quando inimigo está em tela e abrir saídas após eliminar todos os IDs do encontro. **Entrega:** combate completo numa arena com entrada/saída e telegraphs. **Aceitação:** cada arquétipo usa navegação e geometria válidas; pesado degrada cobertura pelo resolvedor normal; inimigos fora de tela ainda contam para clear, mas não disparam invisivelmente; revisitar não recria inimigos.

### 4. Grafo de setores, persistência de sala e checkpoints

Gerar dois setores de 4–6 salas totais cada, com rota principal e ramos opcionais conectados, salas autorais, encontros/loot dentro de budgets e rotas que não exigem loot específico. Persistir RoomState para visitas dentro do setor; transição ao setor seguinte é irreversível. Implementar checkpoint integral na entrada de cada setor, derrota solo/wipe, retorno individual do parceiro e transição cooperativa. **Entrega:** run reproduzível por configuração/seed, sem save de run em disco. **Aceitação:** revisitar mantém mortes, loot coletado/no chão e cobertura; checkpoint restaura mundo e inventários juntos; saída requer encontro limpo e todos os vivos na mesma saída; parceiro retorna uma vez antes de checkpoint novo; setores concluídos e recursos do sobrevivente não são reiniciados; não há retorno ao setor anterior nem duplicação por transição repetida.

### 5. Slice jogável e validação de apresentação

Integrar UI essencial, indicador de parceiro fora de tela, câmera compartilhada com zoom limitado, áudio/feedback e acessibilidade básica. Rodar a mesma suíte lógica headless em Windows e Linux, fazer smoke test dos dois exports e testar coop com as duas combinações de controle. Medir gameplay solo/coop em hardware médio de referência ainda a definir, buscando ao menos 30 FPS. **Entrega:** slice de dois setores jogável do começo ao destino. **Aceitação:** nenhum softlock estrutural de grafo/objetivo; estados e impacto são legíveis; testes de regressão passam nos dois sistemas; exports iniciam e controles funcionam; benchmark registra hardware, resolução, cena, média e p95. O benchmark não substitui testes de regras.

## Critérios funcionais de entrega

- **Sessão e controles:** solo/coop antes da run; input sem cruzamento; desconexão pausa a simulação; espectadores não controlam ator nem atraem câmera.
- **Combate e dano:** hitscan jogador/inimigo, fogo amigo, planos, alcance, LOS e oclusão coerentes; inimigo só dispara visível após aviso; cada par impacto/alvo aplica dano no máximo uma vez.
- **Recursos:** starter é uma pistola; armas adicionais são loot; reservas por categoria, carregadores próprios; recarga interrompida descarta restante uma vez; cura manual e vulnerável tem cancelamentos aprovados.
- **Mundo e encontro:** dois setores, 4–6 salas cada contando ramos, litoral/trincheira em luz de fim de tarde e trincheiras à noite com chuva; encontro inicial não duplica ao revisitar; cobertura tem integridade e quebra; saída não depende de arma aleatória. Confirmar legibilidade sob luz diurna e chuva noturna, inclusive combate coop em benchmark.
- **Coop e checkpoint:** pickups únicos e transições atômicas; retorno individual/wipe sem duplicação; rollback completo restaura baseline atual; progressão de setor só avança.
- **Qualidade técnica:** testes unitários significativos e integração versionados com sistemas; exports Windows/Linux; benchmark em solo e coop com meta de pelo menos 30 FPS.

## Fora do MVP

Coop online, crafting, destruição total do cenário, save/suspensão de run entre execuções e metaprogressão. Também ficam para depois loadouts alternativos/modos, revive, suprimentos adaptativos, checkpoints extras por dificuldade, variações de conteúdo de sala configuradas manualmente, blindfire agachado de baixa precisão e reforços de inimigos. Essas ideias não são compromisso de implementação.

## Pendências por tipo

- **Balanceamento/configuração:** vida/dano, cadência e precisão, quantidades/capacidades de munição, kits e cura, tempos e força de desaceleração, mínimos de retorno do parceiro, integridade de materiais e budgets de ameaça/loot.
- **Conteúdo:** destino/narrativa final, demais equipamentos e distribuição autorada. Fuzil e escopeta não precisam aparecer juntos em toda run; ambientes e climas dos dois setores já estão escolhidos.
- **Setup e validação:** patch exato fixado ao montar o projeto, framework/runner de testes e CI, hardware de referência, resolução e cena de benchmark. Linguagem, engine base e sistemas alvo já estão confirmados.

Todos os critérios acima são de implementação futura. Este plano não cria projeto Godot, código, testes executáveis ou arte final e não afirma validação de gameplay.
