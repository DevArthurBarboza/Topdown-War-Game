# GDD — [título provisório: Frente de Cinzas]

**Versão:** 0.25
**Status:** proposta inicial para validação  
**Gênero:** shooter de ação top-down com exploração procedural  
**Engine:** Godot 4.7; GDScript tipado confirmados; patch exato de setup pendente  
**Modo:** solo e cooperativo local; alvo Windows e Linux confirmados

## 1. Visão do jogo

**Frente de Cinzas** é título provisório. A premissa proposta acompanha um combatente isolado atravessando posições hostis rumo a um destino ainda indefinido. Cada incursão combina salas conectadas, combates intensos, equipamento encontrado e decisões sobre gastar ou preservar munição. A guerra é fictícia e inspirada na primeira metade do século XX; identidade específica de facções ainda será definida.

Proporções semirrealistas compactas e pixel art de alto detalhe com texturas densas foram confirmadas como direção visual. Os briefs textuais de ambientes, personagens, armas/itens e coberturas estão no [índice de arte](art/README.md); paletas, silhuetas concretas e elementos de cada facção seguem em exploração.

### Pilares

1. **Combate tático ágil:** movimento, mira e disparo respondem prontamente; o jogador lê ameaças e escolhe posicionamento.
2. **Escassez justa:** recursos são limitados; regras de distribuição, recuperação e risco de esgotamento precisam de validação. Softlock estrutural de rota é questão separada da vitória após gastar recursos.
3. **Exploração procedural:** cada run recompõe salas autorais em uma rota coerente, com risco e recompensa legíveis.
4. **Atmosfera legível:** o ambiente transmite perigo e desgaste sem esconder projéteis, inimigos ou saídas importantes.

## 2. Escopo e requisitos

### Requisitos definidos pelo usuário

- Shooter top-down em pixel art, inspirado na estrutura de salas de *Binding of Isaac*, sem reproduzir seus personagens, salas ou identidade visual.
- Mundo de guerra devastado, inimigos e equipamentos aleatórios, combate central, recursos escassos e impacto imersivo de disparos e dano.
- Guerra fictícia inspirada na primeira metade do século XX; proporções compactas semirrealistas e pixel art de alto detalhe com texturas densas.
- Cenários de trincheiras, barrancos, estruturas improvisadas, pontes, floresta queimada ou derrubada e postos saqueados; climas de noite chuvosa, clareiras ensolaradas, inverno intenso e céu nublado.
- Ambientes escolhidos para o MVP: primeiro setor começa à beira da praia no fim da tarde e segue para uma trincheira ainda relativamente ensolarada; segundo setor ocorre em trincheiras durante uma noite chuvosa.
- Modos de jogo solo e cooperativo local.
- Usar Godot e desenvolver do zero os componentes próprios do jogo.

A imagem enviada está registrada em [references/concept-art.png](references/concept-art.png) como referência exploratória para uma possível região de trincheiras, não como requisito visual nem definição da direção visual global. O usuário confirmou técnica e nível de detalhe; a referência ajuda a orientar esses briefs, mas não define sozinha a direção de todos os ambientes. Os documentos atuais são briefs textuais; produzir imagens/concept art e assets fica para etapa posterior de revisão visual.

### Propostas deste documento

Alvos MVP confirmados: Windows e Linux; hardware de referência segue pendente. A run avança por setores até um destino ainda indefinido. Extração obrigatória e chefe final não estão aprovados. O MVP tem dois setores com 4–6 salas cada; não há duração aprovada. Variação configurável por dificuldade/eventos é possibilidade futura, não requisito do MVP. O espaço pode ter vários planos reais dentro de uma sala, ligados por travessias definidas no cenário; salas conectam-se no plano normal. Godot 4.7 e GDScript tipado foram escolhidos; fixar patch exato ao configurar o projeto.

## 3. Fantasia, objetivo e estrutura da run

Na premissa proposta, o personagem ou equipe se separa de sua unidade após uma ofensiva. Em solo ou coop local, deve atravessar posições hostis e chegar a um destino ainda indefinido. A narrativa surge de objetos, bilhetes curtos, sinais ambientais e encontros; cenas longas não são requisito do MVP.

Uma run começa em sala segura e progride por setores até um destino ainda a definir. Extração e chefe não são requisitos confirmados. No MVP, avançar ao próximo setor é irreversível: o grupo não retorna a setores anteriores, e o checkpoint anterior é substituído pelo novo na entrada do setor seguinte. Derrota solo ou wipe coop restaura o snapshot completo do checkpoint atual em memória: vida, munição, equipamento/inventário e estado do mundo/salas, inimigos, loot, portas, progresso, seed e RNG. A run usa o mesmo layout e encontros; alterações posteriores ao snapshot são desfeitas, setores concluídos não são reiniciados e não há cura total automática. Não há progressão permanente entre partidas no MVP: novas runs começam com a mesma arma e recursos iniciais predefinidos, embora seed e drops variem. Run não é salva nem suspensa entre execuções. Salvar perfil/configuração é questão separada; persistência de perfil ainda precisa de regra. Em coop, se um jogador morre, o sobrevivente continua e o morto acompanha como espectador; ao alcançar o próximo setor, o parceiro volta uma única vez com os equipamentos que tinha ao morrer e mínimos predefinidos de vida e munição. Os valores numéricos seguem pendentes. Não alterar recursos do sobrevivente nem repor automaticamente kits/consumíveis. Preparar o retorno do parceiro antes de capturar o novo checkpoint é a ordem técnica recomendada. Se ambos morrerem, toda a equipe restaura o checkpoint baseline, sem aplicar os mínimos individuais. Não há reanimação no MVP.

Loop principal: explorar a sala → identificar ameaças e cobertura → combater ou reposicionar → recolher recursos/equipamento → escolher a próxima conexão → avançar por setores → alcançar o destino. Objetivos opcionais podem oferecer recompensa com risco adicional, como abrir uma sala de suprimentos ou ativar um posto de rádio.

## 4. Salas, geração e exploração

Salas autorais formam um grafo procedural por seed: há caminho principal alcançável do início à saída do setor, com ramos conectados de salas opcionais ou rotas alternativas. O total do setor continua 4–6 salas, contando ramos dentro do orçamento; um ramo não exige arma aleatória específica para alcançar a saída. Inimigos e equipamentos variam conforme orçamento. A geração garante conectividade, início seguro para todos os participantes, objetivos alcançáveis e ausência de inimigos sobre posições de spawn.

Cada setor tem tipos e quantidades de suprimentos predefinidos; a posição de inimigos e recursos varia aleatoriamente dentro do orçamento. No MVP, não ajustar a oferta de suprimentos conforme vida/munição do jogador; o orçamento de ameaça pode considerar o grupo. Uma alternativa de emergência de melee, com baixo dano e exposição ao perigo, está confirmada; não foram definidos cargas, stamina ou limite de uso. Quantidades e distribuição exatas da economia seguem pendentes. Assistência adaptativa é intenção pós-MVP, não implementar agora. Ao revisitar sala limpa dentro do mesmo setor, inimigos mortos não reaparecem; loot coletado não volta, loot deixado no chão permanece na posição/ID, e integridade de cobertura mantém seu estado. Só o rollback integral do checkpoint desfaz esses estados.

Exemplo de sala: trincheira alagada em dois planos ligados por uma rampa autorada, duas entradas e cobertura de sacos de areia. Um fuzileiro protege a saída; sua linha de tiro é anunciada antes do disparo. Uma caixa pode conter munição ou equipamento. Planos podem trocar tiros quando há linha de visão; barrancos e bordas bloqueiam por oclusão geométrica.

## 5. Controles e combate

Controles confirmados: solo usa teclado e mouse ou gamepad; coop usa teclado/mouse mais gamepad ou dois gamepads. Cada dispositivo é associado ao jogador sem input cruzado. Direcional ou WASD move; mouse ou analógico mira e dispara; ações dedicadas incluem agachar, recarregar, trocar armas e usar consumíveis. Teclas/botões específicos e remapeamento seguem configuração futura. Dash fica fora do MVP.

### Cooperação local

Solo/Coop são opções de menu; segundo jogador só entra pela opção Coop antes de iniciar a run. Após iniciar, os participantes e seus player_id permanecem estáveis, sem entrada dinâmica. Na reconexão, o dispositivo validado pode ser reassociado ao mesmo jogador, mesmo que seu identificador runtime mude. Se controle desconectar, toda simulação pausa até reconectar o mesmo jogador. A câmera compartilhada acompanha os jogadores vivos e pode reduzir zoom, dentro de limites configurados, para salas maiores; espectadores não atraem a câmera. Jogadores podem sair do enquadramento e recebem indicadores de direção na tela. Não limitar movimento para caber no viewport, usar split-screen ou teleporte de câmera. Após combate encerrado, a saída só transita quando todos os jogadores vivos estiverem na mesma área da mesma saída; espectadores mortos não contam. Munição e cura são individuais. Inventário/equipamentos separados por jogador são uma proposta. Pickups únicos, inclusive munição e cura, são concedidos ao primeiro jogador que interagir explicitamente; não há coleta automática por proximidade nem duplicata/partilha para o restante do grupo. Interações simultâneas no mesmo tick são ordenadas de modo estável e concedem o item uma única vez, sem prioridade fixa por número de jogador. O que ocorre se a concessão for rejeitada por inventário cheio ou limite de recurso permanece pendente; recomenda-se não consumir o pickup nessa falha, sem troca automática. Fogo amigo está ligado no MVP: tiros hitscan e melee do jogador podem danificar o parceiro, usando a mesma política de oclusão por cobertura/planos. O dano segue a configuração do ataque; multiplicador ou redução não foram definidos, e não se assume dano ao próprio autor.

Se um jogador coop morre, o sobrevivente continua e o morto acompanha como espectador; quando o sobrevivente alcança o próximo setor, o parceiro retorna. Se ambos morrerem, a equipe inteira retorna ao checkpoint. Reanimação não faz parte do MVP. O parceiro mantém os equipamentos que tinha ao morrer e recebe mínimos predefinidos de vida e munição por categoria compatível com suas armas, ainda sujeitos a balanceamento; armas retidas da mesma categoria compartilham um único mínimo de reserva. Não alterar recursos do sobrevivente nem repor kits/consumíveis automaticamente. A ordem técnica recomendada é retornar o parceiro antes de capturar o novo checkpoint. Desconexão pausa a run até reconectar; reconectar associa um dispositivo validado ao mesmo player_id e não revive nem altera inventário/recursos.

Equipamentos usam slots limitados e trocas de vantagens/desvantagens durante a run. Cada personagem inicia uma run com uma pistola padrão e recursos iniciais predefinidos; fuzil e escopeta podem aparecer como loot durante a run, sem garantia de obter ambos em toda run. Cada personagem pode carregar até duas armas de fogo alternáveis, além de melee, que não ocupa slot; isso é um limite de carga, não obrigação de começar com duas armas. Kit de cura é armazenado e ativado manualmente: durante a aplicação temporizada o jogador move-se lentamente, não pode disparar e permanece vulnerável. Dano recebido, inclusive fogo amigo, ou cancelamento manual interrompe a aplicação e consome o kit sem curar; concluída a aplicação, o kit é consumido uma vez e restaura a quantidade configurada sem exceder a vida máxima. Troca de arma durante a cura ainda precisa de regra. Duração e valor de cura seguem pendentes.

Munição reserva no MVP tem três categorias: pistola, fuzil e escopeta. Armas da mesma categoria compartilham reserva, mas cada arma mantém seu próprio carregador. Recarga antecipada descarta as balas restantes no início da ação, uma única vez, sem devolvê-las à reserva. Trocar de arma cancela a recarga: o carregador anterior fica vazio e não recebe munição da reserva; ao voltar, pode ser recarregado novamente. A reserva só é debitada e o carregador preenchido quando a recarga termina (ordem técnica proposta); se não houver munição compatível, recomenda-se rejeitar antes do descarte. Com reserva parcial, recomenda-se preencher até o menor entre capacidade e reserva disponível, sem exigir carregador cheio. Por exemplo, com 3/10 no carregador e reserva 10, uma recarga concluída deixa 10 no carregador, reserva 0 e descarta as 3 balas antigas. Capacidades e quantidades exatas seguem balanceamento. Não garantir que todas as categorias sejam encontradas em toda run; suprimentos por setor continuam finitos e devem manter disponibilidade compatível com a pistola inicial.

Disparos de jogador e inimigos usam hitscan. Traçantes e outros VFX são visuais: não há deslocamento balístico nem dano atrasado. Inimigos devem telegrafar o disparo, com linha, ângulo e intervalo legíveis. Granadas e outros explosivos permanecem por decidir e não são requisito do MVP; granadeiro é candidato futuro, fora do roster MVP. Coberturas têm obstáculos altos e baixos; obstáculos baixos dão proteção total contra disparos bloqueados quando o personagem está agachado; para atirar por cima deles, é necessário levantar. Agachar não impede disparos em áreas livres ou por um ângulo sem cobertura. Disparo agachado atrás de cobertura, com precisão muito baixa, é intenção pós-MVP. A cobertura depende de geometria direcional, plano e postura; flancos e planos sem proteção não concedem imunidade geral. Materiais frágeis degradam gradualmente com impactos até quebrar. O impacto que quebra é absorvido pela cobertura, sem atingir ator atrás dela; impactos seguintes podem passar pela abertura. Cobertura quebrada deixa de bloquear tiros e movimento; postura/velocidade/mira ao agachar, quantitativos de integridade e dano seguem balanceamento pendente. A quebra de cobertura não implica destruição total do cenário, que permanece fora do MVP. Dano usa a configuração do ataque; multiplicador/redução ainda não foram definidos. Não assumir dano ao próprio autor.

Acertos combinam som, partículas por material, recuo e reação do alvo. Sangue moderado/configurável é uma proposta pendente de validação; inimigos não ficam presos em stunlock. Tremor e hitstop são mínimos e ajustáveis. UI indica dano, direção aproximada e vida. Não há invulnerabilidade após dano; impactos válidos distintos sempre podem causar dano. Cada acerto causa breve desequilíbrio e reduz movimento, mantendo mira disponível; duração, intensidade e acumulação/renovação seguem pendentes. Quantidades da economia de munição e recuperação seguem abertas; o melee de emergência fraco e arriscado está definido acima. Sem fome, sede ou durabilidade no MVP.

## 6. Inimigos e encontros

Arquétipos iniciais, sujeitos a prototipagem:

- **Fuzileiro:** pressiona posições protegidas e avança entre coberturas com rajadas telegrafadas.
- **Flanqueador:** busca outros ângulos válidos e pune permanência em uma posição.
- **Pesado:** ameaça destruir coberturas com impactos de sua arma normal; comunica pressão e degrada integridade respeitando material e geometria da cobertura.


A IA usa estados de patrulha, percepção, busca, ataque e reposicionamento, com linha de visão e reação limitada ao que pode perceber. Todos os inimigos do encontro são colocados quando ele começa; a sala só fica limpa depois que todos os inimigos daquele conjunto forem eliminados, inclusive os fora da tela; reforços sinalizados são intenção pós-MVP. O MVP usa fuzileiro, flanqueador e pesado; quantidades/variações seguem budget e balanceamento. Granadeiro é candidato futuro; elites e chefes não são requisito. Inimigos só disparam hitscan depois de entrar na tela e anunciar o ataque; podem continuar se movendo fora dela. Jogadores fora do viewport continuam podendo atacar e receber dano normalmente, respeitando alcance, linha de visão e altura.

## 7. Mundo, arte e áudio

O MVP tem dois setores, cada qual com 4–6 salas, e estes ambientes aprovados. A luz e a chuva são perfis visuais/sonoros por setor ou sala; não é necessário relógio contínuo de dia/noite. Chuva é cosmética no MVP: sem física da água ou redução injusta de visibilidade. A matriz mantém separadas as possibilidades futuras, ainda propostas, para outras regiões.

| Recorte | Ambiente e clima | Construções e ênfase |
|---|---|---|---|
| MVP — setor 1 | Beira da praia no fim da tarde, seguindo para uma trincheira relativamente ensolarada | Construções e variações de encontro a definir; manter leitura clara de cobertura e travessias |
| MVP — setor 2 | Trincheiras em noite chuvosa | Fortificações e coberturas de trincheira; chuva sem ocultar injustamente ameaças |
| Possíveis regiões futuras, propostas | Floresta devastada (clareiras com sol ou chuva); frente de inverno (neve/nevasca ou céu nublado); povoado/posto saqueado (nublado ou chuva) | Abrigos de madeira; postos de metal/concreto; alvenaria e interiores saqueados. Não fazem parte da seleção de ambientes do MVP. |

Planos dentro de salas são níveis espaciais reais; no MVP, só se troca de plano por rampas, escadas ou passagens específicas construídas no cenário, sem salto ou queda livre. Conexões entre salas usam o plano normal. Planos podem trocar tiros quando há linha de visão; barrancos e bordas bloqueiam conforme oclusão geométrica. Altura maior não concede imunidade por si só. Perfis de ambiente podem alterar luz e ambiência sonora; no MVP, chuva não adiciona física de água nem prejudica visibilidade/legibilidade dos ataques.

A direção visual global tem técnica, proporções e densidade de detalhe aprovadas, mas os conceitos concretos ainda serão revisados. A direção deve preservar a leitura do piso, jogador, inimigos, disparos, cobertura e itens; contorno, contraste e cor podem variar com o ambiente sem perder legibilidade. Efeitos de impacto respeitam o material. Resolução-base, tamanho dos tiles, paleta final, linguagem de cada facção e linguagem visual específica de cada ambiente continuam pendentes. Consulte os [briefs de concept art](art/README.md).

Áudio usa camadas distintas para ambiente, armas, impactos, passos e alertas. Sons de ameaça devem ser localizáveis e não mascarar sinais de ataque. A UI deve mostrar vida, arma ativa, munição, consumível e objetivo atual sem cobrir a arena. Legendas para alertas relevantes, remapeamento de controles, opções de contraste, redução de tremor e ajuste de hitstop são requisitos de acessibilidade a considerar desde o protótipo.

## 8. MVP e fora de escopo

O MVP contém dois setores, cada um com 4–6 salas autorais em grafo, e deve levar a run até um destino, em solo e coop local para dois jogadores. Inclui pistola inicial; fuzil e escopeta disponíveis como loot, sem garantir ambos em cada run; inimigos fuzileiro, flanqueador e pesado; equipamentos aleatórios, hitscan, melee fraco e arriscado, suprimentos predefinidos por setor, checkpoint capturado na entrada de cada setor e planos locais com transições autoradas. Não há duração em minutos aprovada. Coop online, crafting, destruição total do cenário e save/suspensão da run entre execuções ficam fora do MVP. Conteúdo e regras ainda pendentes permanecem indicados neste GDD.

### Estrutura técnica proposta

Usar cenas e componentes próprios, em arquitetura modular por feature, para jogador, armamento, saúde, IA, salas, geração, loot, sessão, câmera e UI. Godot 4.7, GDScript tipado e exports Windows/Linux estão confirmados; recomenda-se 4.7.2-stable conforme o arquivo oficial consultado em 2026-10-23 e fixar patch no setup. O alvo é gameplay a pelo menos 30 FPS em computador médio; hardware de referência, resolução e cena de medição seguem pendentes. Compor entidades a partir de sistemas próprios e manter regras testáveis; usar recursos nativos da engine para física, renderização e entrada. A [proposta detalhada de arquitetura](ARCHITECTURE.md) descreve pastas, limites e fluxo; consulte também as [regras de arquitetura](../rules/architecture.md). Referências oficiais: [nós e cenas](https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html) e [controles e dispositivos](https://docs.godotengine.org/en/stable/tutorials/inputs/controllers_gamepads_joysticks.html).

Roadmap e aceitação estão consolidados em [Plano de implementação do MVP](MVP_IMPLEMENTATION.md), que organiza seis marcos sem iniciar projeto, runtime ou produção de arte nesta etapa.

## 9. Critérios de aceitação e validação futura

Com a implementação, versionar testes não triviais junto das regras. Os critérios centrais são:

- **Combate e cobertura:** impactos distintos aplicam dano; repetir o mesmo `impact_id` no mesmo alvo não duplica. Hitscan respeita LOS/altura/cobertura; impactos que quebram cobertura são absorvidos. Agachar, equilíbrio e postura devem manter mira e leitura conforme decisões centrais.
- **Recursos:** kit move lentamente, bloqueia disparo e é vulnerável; dano ou cancelamento manual consome sem curar; conclusão cura uma vez. Recarga descarta o carregador uma vez no início; troca de arma cancela sem completar nem debitar reserva posteriormente. Testar categorias, carregadores, pausa e callbacks após morte/restore.
- **Mundo e encontros:** seeds/configuração reproduzem grafo de dois setores com 4–6 salas, ramos alcançáveis e sem loot obrigatório; revisar sala preserva morte, pickup, item no chão e cobertura; inimigos e cobertura não duplicam ao descarregar/recarregar.
- **Coop e sessão:** input isolado, pausa/reconexão, pickup único, saída com participantes vivos, espectador, retorno individual único e wipe; transição entre setores é irreversível e captura novo checkpoint após retorno.
- **Checkpoint:** rollback restaura junto inventários, munição, kits, sala, inimigos, loot, cobertura e RNG ao baseline, sem efeitos duplicados ou setor concluído reiniciado.
- **Validação manual e técnica:** playtests verificam sinais de ameaça, leitura visual, câmera e controle. Exports Windows/Linux e benchmark solo/coop seguem os critérios e pendências de [MVP_IMPLEMENTATION.md](MVP_IMPLEMENTATION.md). Nenhum teste foi executado.

Playtests devem verificar leitura de sinais hitscan, direção de tiros, planos, cobertura e câmera para dois jogadores. Registrar seed, duração, mortes, dano e munição restante. Estes critérios são futuros; nenhum teste foi executado.

## 10. Decisões pendentes e riscos

As decisões confirmadas e pendentes do ciclo central estão em [Decisões centrais](CORE_DECISIONS.md). Permanecem pendentes patch exato da Godot, postura/velocidade/mira ao agachar, valores de integridade/dano de cobertura e quantitativos da economia e mínimos de retorno do parceiro, persistência de perfil/configuração (sem metaprogressão no MVP), hardware de medição e regras UX específicas restantes, como troca de arma durante cura. Dano aliado usa a configuração do ataque; multiplicadores ainda não foram definidos. Validar leitura, equilíbrio e câmera no slice antes de ampliar conteúdo.
