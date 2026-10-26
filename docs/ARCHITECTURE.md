# Arquitetura proposta

**Status:** proposta documental para revisão  
**Escopo:** projeto futuro em Godot; nenhum código é implementado por este documento

## Objetivo e decisões

A arquitetura deve manter as regras de gameplay pequenas, testáveis e independentes da árvore de cenas, sem afastar o código da forma natural de trabalhar em Godot. A proposta é um monólito modular organizado por funcionalidades, com cenas compostas por nós e componentes próprios. Não se propõe framework ECS nem camadas de abstração sem necessidade demonstrada.

Godot 4.7 e GDScript tipado estão confirmados; Windows e Linux são os alvos do MVP. A versão exata do patch deve ser fixada no setup; recomenda-se Godot 4.7.2-stable, listada como estável no [arquivo oficial consultado em 2026-10-23](https://godotengine.org/download/archive/). Os caminhos e extensões `.gd` abaixo são layout futuro ilustrativo. A recomendação de compor cenas no nó pai segue as práticas de [organização de cenas](https://docs.godotengine.org/en/stable/tutorials/best_practices/scene_organization.html). Definições em `Resource` devem ser tratadas como dados compartilhados em cache, separadas do estado de cada instância, conforme a [documentação de Resources](https://docs.godotengine.org/en/stable/tutorials/scripting/resources.html).

## Estrutura de pastas proposta

```text
project.godot                 # futuro arquivo raiz do projeto
src/
  app/                        # composição e ciclo da sessão
  features/
    combat/
      rules/ runtime/ data/ presentation/
    actors/
      rules/ runtime/ data/ presentation/
    weapons/
      rules/ runtime/ data/ presentation/
    inventory/
      rules/ runtime/ data/ presentation/
    world/
      generation/ rooms/ encounters/ biomes/
    progression/              # futuro opcional; não criar no MVP
  ui/
  platform/
    persistence/
    settings/
  shared/                     # contratos e tipos de valor realmente comuns
content/
  rooms/                      # salas autorais .tscn por região
  definitions/                # Resources de armas, equipamentos, inimigos etc.
assets/                       # arquivos fonte: sprites, áudio, fontes e demais insumos
tests/
  unit/                       # espelha regras das features
  integration/                # cenas, sinais, input e colisões
  fixtures/
docs/
rules/
```

As pastas `rules`, `runtime`, `data` e `presentation` descrevem responsabilidades, não obrigação de criar quatro diretórios vazios em cada feature. Manter arquivos `.tscn` junto aos scripts e dados da feature a que pertencem; reservar `content/` para definições e composições autorais reutilizadas por região. `assets/` guarda os arquivos brutos importados pela engine, enquanto `content/` guarda instâncias e dados de jogo.

## Limites de responsabilidade

| Módulo | Responsabilidade e estado que possui |
|---|---|
| `app` | Bootstrap, criação da `RunSession`, roteador de input e fluxo entre cenas. Compõe dependências; não decide regras de combate. |
| `combat` | Regras de dano, vida, resposta a acertos sem invulnerabilidade pós-dano, linha de visão e oclusão por cobertura/altura. Um resolvedor comum serve hitscan/melee, jogadores e IA; fogo amigo está ligado no MVP. Reanimação não pertence ao MVP. |
| `actors` | Composição e comportamento runtime do jogador e roster MVP de fuzileiro, flanqueador e pesado. IA produz comandos compatíveis com os do jogador em vez de aplicar dano por atalho. |
| `weapons` | Até duas armas de fogo alternáveis por jogador, além de melee; estado por arma inclui cadência, recarga e carregador. Reserva pertence ao jogador nas categorias MVP pistola/fuzil/escopeta. Recebe comandos e consulta definições. |
| `inventory` | Slots, kits de cura armazenados e consumíveis por jogador; interação explícita para conceder pickup único, com validação e commit atômicos. |
| `world` | `RunDefinition`/`SectorDefinition`, grafo de salas autorais, perfis de luz/ambiência por sala ou setor, variação aleatória de inimigos/equipamentos, planos espaciais e estado de sala/loot. Estado da run não vive apenas em nós descarregáveis. |
| `progression` | Fronteira futura opcional para dados entre runs; não implementar desbloqueios/metaprogressão no MVP nem criar dependência/runtime vazio. Persistência de perfil/configuração é separada. |
| `RunSession` | Dois participantes configurados antes da run, razões de pausa, sessão ativa, validação de saída/transições, checkpoint em memória e restauração completa após derrota solo ou wipe coop. |
| `ui` e `presentation` | Leitura de estado e apresentação visual/sonora. Podem enviar comandos ou observar eventos, mas não decidem dano, inventário ou geração. |
| `platform` | Adaptadores de persistência e configurações; não contém regras de combate ou mundo. |

Um exemplo de cena de jogador, a ajustar durante implementação:

```text
Player (CharacterBody2D)
├── AimPivot
│   └── WeaponMount
├── HealthComponent
├── InventoryComponent
├── InputAdapter
├── AnimationPlayer
└── AudioStreamPlayer2D
```

A cena raiz compõe os nós e configura dependências antes de entrarem em execução. Regras puras podem usar `RefCounted` e tipos de valor nativos; não dependem de `SceneTree`, nós globais, APIs de input ou arquivos. Quando necessário, recebem um port, `Callable` ou resultado de adaptador runtime.

### Posição e planos espaciais

Uma posição lógica de gameplay combina `room_id`, coordenada XY e `elevation_id`. `world` define geometria e travessias autorizadas dentro da sala. No MVP, troca de plano ocorre somente por rampas, escadas ou passagens específicas autoradas, sem salto/queda livre. Uma conexão para outra sala posiciona participantes no plano normal. Use camadas de altura discretas com ligações autoradas; não pressuponha simulação de física 3D. Planos podem trocar tiros quando há linha de visão. Barrancos e bordas bloqueiam por oclusão geométrica; altura maior não cria imunidade. Validar que footprints navegáveis de níveis distintos não se sobrepõem em XY; conexões por rampas/escadas continuam válidas, mas passar sobre/baixo do mesmo footprint fica fora do MVP.

O resolvedor de combate consulta uma política comum de visibilidade e cobertura para jogadores e IA. Não basta chamar um `RayCast2D` sem considerar a altura lógica: o teste de impacto também consulta `elevation_id` e relações entre planos. Implementar essa política como consulta compartilhada, não como duas regras parecidas em cada ator.

Cobertura pertence a `world`: `CoverState` por ID estável guarda material e integridade mutável; thresholds e definição geométrica vêm de Resource somente leitura. Estágios `intact/damaged/broken` são proposta de representação. `combat` consulta LOS, plano, direção e postura (agachado/em pé); obstáculos baixos protegem totalmente apenas quando agachado e bloqueiam geometricamente o disparo; para atirar por cima, o jogador precisa levantar, embora possa disparar agachado em área livre ou ângulo aberto. Blindfire agachado é intenção pós-MVP. Resolver geometria antes do dano: identificar primeira cobertura; impacto que a quebra é absorvido e não atinge ator atrás dela. Reduzir integridade e atualizar estágio no dono do estado uma vez; presentation recebe evento para VFX, nunca controla colisores pela sprite. Tiros subsequentes podem passar pela abertura; cobertura quebrada não bloqueia tiros nem movimento. Snapshot restaura integridade/estágio e atualiza colisores/navegação locais; recarregar sala preserva dano até rollback. Valores de integridade/dano e detalhes da postura seguem balanceamento pendente. Atualizar navegação da IA localmente após mudança, sem rebuild global a cada frame.

## Direção das dependências

O composition root em `app` cria a sessão e instancia features/adaptadores. Código runtime pode chamar suas próprias regras e ler definições de dados. Regras recebem valores e dependências explícitas. UI e apresentação observam estado ou eventos públicos e encaminham comandos; não modificam estado interno de outra feature. Contratos compartilhados em `shared/` devem ser pequenos e existir apenas quando dois módulos realmente precisam do mesmo tipo ou mensagem. Nenhuma dependência circular entre features.

Chamadas diretas representam comandos que precisam de resultado; sinais representam fatos ocorridos para ouvintes que não são donos da regra. Preferir dependências explícitas passadas pelo pai a autoloads globais. `NodePath` é adequado dentro de uma cena; dependências entre cenas devem ser configuradas ou injetadas pelo compositor. Ao conectar sinais, a instância que os conecta também responde pelo ciclo de vida e desconexão.

### Fluxo de disparo ilustrativo

1. `input_router` converte evento do dispositivo em `PlayerCommand`, associado a `player_id`.
2. Ator entrega o comando à arma ativa; regras de arma validam munição e cadência.
3. Resolvedor hitscan/melee consulta alvos aliados ou hostis, postura, altura e cobertura usando a política comum; aplica dano ao alvo ou integridade da primeira cobertura atingida conforme a regra geométrica. Fogo amigo está ativo; não inferir dano ao próprio autor.
4. Regras aplicam dano a cada impacto válido sem invulnerabilidade pós-dano, mas deduplicam reprocessamento do mesmo par (impact_id, target_id). Ao acertar jogador, aplicam breve status de desequilíbrio: movimento reduzido, mira disponível, sem atordoamento completo. Duração, intensidade e acumulação/renovação seguem pendentes; não travar o jogador indefinidamente. Traçantes/VFX são cosméticos e não atrasam ou repetem o dano.
5. Evento de acerto/dano alimenta animação, som, efeitos e HUD. Ataques inimigos também apresentam telegraph legível antes do hitscan.

Cada estado tem uma fonte de verdade. Definição de arma contém um `ammo_category_id`; estado do jogador mantém reserva por categoria, enquanto cada instância de arma mantém carregador e capacidade. Duas armas da mesma categoria compartilham reserva, sem compartilhar carregador nem converter munição entre categorias. Recarga antecipada descarta o restante no início, uma vez, sem reembolso. Trocar de arma cancela recarga, deixa o carregador vazio e não debita reserva tardiamente; ao voltar à arma, recarrega novamente. Proposta técnica: debitar reserva e preencher carregador na conclusão; rejeitar antes do descarte apenas se não houver munição compatível e, com reserva parcial, preencher até o menor entre capacidade e reserva disponível. Reconciliação: munição anterior = munição posterior + balas descartadas + disparos. UI lê estados, não mantém contagens próprias. Pickup, recarga e concessão de kit validam/confirmam mudança numa chamada síncrona.

Kit de cura é armazenado e ativado manualmente. Durante a aplicação temporizada, ator move-se lentamente, não dispara e permanece vulnerável. Dano recebido, inclusive aliado, ou cancelamento manual encerra a ação e consome o kit sem cura; conclusão consome uma vez e cura o valor configurado até o máximo de vida. Reservar o kit no início é proposta técnica para evitar dupla cobrança e permitir custo ao cancelar. Duração e quantidade curada e se troca de arma cancela continuam pendentes. Pausa congela timers/ação; morte ou restore descarta callbacks antigos antes de reaplicar estado.

## Câmera, encontros e coop local

A sessão fornece à câmera alvos dos jogadores vivos; espectadores não puxam o enquadramento. Componente runtime da câmera mantém viewport compartilhado e pode reduzir zoom, entre limites configurados, para salas maiores. Valores de zoom/margem e centro/alvo do enquadramento seguem pendentes; jogadores podem sair do viewport sem limitar movimento ou usar split-screen/teleporte. UI apresenta indicadores de direção do parceiro fora da tela, a partir da posição mundial, sem autoridade sobre gameplay. Câmera é apresentação: consulta visibilidade/enquadramento por adaptador runtime e não decide dano. Inimigos só executam disparo hitscan após entrarem na tela e anunciarem o ataque; IA pode se mover fora dela. Adaptador/runtime fornece contexto de enquadramento a combat, que valida a condição antes do disparo; apresentação exibe telegraph, sem autoridade sobre o estado do ataque. Viewport não concede imunidade nem bloqueia ataques de jogadores fora da tela: resolução de hits usa alcance, LOS e altura, independentemente da câmera. Verificar visualmente no solo/coop e incluir salas grandes no benchmark.

Na inicialização do encontro, compor a população inicial completa com IDs estáveis e seed/configuração. `EnemyDefinition` de cada arquétipo configura papel e ações: fuzileiro pressiona coberturas, flanqueador escolhe outros ângulos válidos e pesado degrada cobertura com impactos normais. Não reduzir esses papéis a HP distinto nem criar framework de classes. O MVP não adiciona reforços dinâmicos; sinalização é intenção pós-MVP. `RoomState` persiste população e estado de encontro entre visitas: inimigos mortos não reaparecem, itens coletados continuam coletados, loot não coletado mantém ID/posição, e integridade de cobertura persiste mesmo com unload/reload. O grafo de cada setor oferece caminho principal e ramos conectados opcionais dentro do orçamento total de 4–6 salas; a saída não pode depender de arma aleatória específica. Só o rollback completo do checkpoint restaura o baseline conjunto de mundo e inventário.

Antes de iniciar a run, menu escolhe solo ou coop e `RunSession` associa jogadores/dispositivos; após início, sem drop-in/out de participante. Mantenha conjunto de razões de pausa, como manual e controle desconectado. Enquanto não vazio, congelar mundo, IA, disparos e timers de reload/cura/desequilíbrio; manter UI e input necessários para retomar/reconectar ativos. Reconexão pode atualizar o device_id validado, reassociando ao mesmo player_id sem recriar ator, conceder starter, reviver ou alterar recursos. Pausa não captura checkpoint. Remover uma razão não retoma enquanto outra permanecer.

Saída emite intenção/evento de interação; app/RunSession valida condição de domínio: encontro limpo e todos os participantes vivos na mesma área da mesma saída. Espectadores mortos são excluídos. Só então fazer commit da transição uma vez, descarregar a sala e cancelar callbacks; manter estado da sessão/inventários. Sala em combate, jogador vivo fora da área ou vivos em saídas diferentes não transicionam.

## Coop local, dados e ciclo de vida

Manter `player_id` separado do identificador de dispositivo. `input_router` associa dispositivos aos dois jogadores e mantém buffer por jogador. A sessão fornece à câmera ambos os alvos e associa munição, cura e HUD ao jogador correspondente. Um pickup único — também munição e cura — requer interação explícita e é concedido ao primeiro jogador que interagir; sem coleta por proximidade ou duplicata/partilha. Validar e confirmar em uma operação atômica. Se duas interações ocorrerem no mesmo tick, ordenar de forma estável e conceder uma vez, sem prioridade fixa pelo ID do jogador. Se a capacidade rejeitar a concessão, recomenda-se preservar o pickup; tratamento final pendente e sem troca automática. Na morte individual, guardar equipamentos do parceiro por IDs/valores na sessão, não pela cena destruída; o sobrevivente e seus recursos ficam intactos enquanto ele segue, com o morto espectador. No próximo setor, o parceiro retorna com os equipamentos retidos e mínimos predefinidos positivos de vida/munição, estes sujeitos a balanceamento; limitar munição à capacidade da arma compatível. Não repor automaticamente kits/consumíveis. Wipe restaura a equipe inteira ao baseline do checkpoint. Não permitir late join. Desconexão adiciona razão pause para toda a sessão; reconectar pode reatribuir device_id validado ao mesmo player_id sem recriar ator ou repor recursos.

Definições `.tres` são recursos compartilhados e somente leitura durante a run. O estado mutável — munição, saúde, modificadores temporários — pertence a cada jogador, arma ou inimigo instanciado. Persistir identificadores e valores serializáveis, não referências a `Node`. Se saves forem implementados, introduzir versão de formato e validação de dados antes de carregar; save de run não está aprovado como escopo atual.

### Checkpoint e configuração da run

Na entrada de cada setor do MVP, um marcador aciona a captura em memória. `RunSession` detém o `CheckpointSnapshot` completo: vida atual, status de desequilíbrio com timers/modificadores, reservas de munição por categoria, carregadores por arma, kits/ações de cura, equipamento/inventário, estado de sala/mundo, progresso apenas da run atual (sem metaprogressão), inimigos, loot, portas, layout/seed e RNG. O snapshot é imutável, contém apenas valores/IDs e não mantém `Node` nem aliases para `Resource` mutáveis. Restaurar anula ganhos e gastos após o snapshot; não cura automaticamente nem reinicia setores concluídos, e não rerolla layout, encontros ou loot. Ao avançar de setor, a fronteira é irreversível: prepare o retorno individual do parceiro morto uma única vez antes de substituir o checkpoint, sem reabastecer o sobrevivente nem conceder suprimentos extras. Só descarte o estado anterior após confirmar transição e novo snapshot; falha antes do commit preserva o checkpoint vigente.

No solo, derrota retorna ao checkpoint em memória. Run não é salva/suspensa entre execuções; persistência de perfil/configuração permanece decisão separada. Wipe coop restaura toda a equipe ao snapshot baseline, não aos mínimos de retorno. Ao atravessar para setor seguinte, parceiro morto retorna uma vez antes da nova captura (ordem técnica recomendada), mantendo equipamentos da morte e recebendo valores configurados de vida/munição sujeitos a balanceamento. Não curar o sobrevivente nem repor automaticamente kits/consumíveis. Não há reanimação em sala comum.

Ao restaurar, `RunSession` coordena `world`, atores e inventários. A transação deve ser idempotente: interromper timers/conexões da sala ativa, aplicar todos os donos de estado e só então sair de `restoring_checkpoint`. Não anunciar game over irreversível nem emitir sinais que permitam aplicar duas vezes munição/loot. Recarregar só o nó de sala enquanto inventário permanece em outro estado exige política explícita de rollback, para que a operação não permita farm.

`RunDefinition` contém `min_sectors`/`max_sectors`, com default MVP 2/2, e PlayerStartDefinition para pistola padrão e recursos iniciais por jogador, iguais em todas as novas runs do MVP. Conceda esse loadout uma vez ao iniciar a nova run, nunca ao entrar em sala, restaurar checkpoint ou retornar parceiro. Outros modos podem fornecer outro conjunto pós-MVP; não criar seletor agora. Cada `SectorDefinition` contém `min_rooms`/`max_rooms`, default MVP 4/6. Resolva plano inicial, seed, versão do gerador e perfil/config antes de iniciar para reproduzir composição. Não espalhe contagens em scripts nem as altere silenciosamente durante a run. Pós-MVP, setores grandes podem ter pontos extras de checkpoint cuja quantidade/localização varia com dificuldade; isso é dado futuro, sem implementar agora sistema de dificuldade.

Geração procedural usa grafo de salas autorais, com caminho principal e ramos opcionais/alternativos dentro do orçamento total de 4–6 salas; saída do setor não exige loot específico. `SectorDefinition` pode referenciar perfil de luz e ambiência de cada setor/sala; não introduzir relógio contínuo de dia/noite. Para os ambientes MVP, chuva tem apresentação visual/sonora sem física de água ou obscurecimento injusto. Revisitas e rollback seguem RoomState/checkpoint acima. A transição entre setores só avança e fecha acesso ao grafo anterior; mantenha apenas o estado necessário ao setor atual e ao snapshot vigente. Geração usa inimigos/equipamentos aleatórios e seed explícita; streams separados servem topologia, encontros, loot e efeitos cosméticos. Tipos/quantidades de suprimento são predefinidos por setor e não se adaptam à vida/munição do jogador no MVP; localização é aleatória dentro do orçamento. Orçamento de ameaça pode considerar o grupo. Versionar gerador/configuração lógica, não prometer determinismo de física/replay. Após MVP, salas autoradas podem declarar ponto de variação por instância: `inherit`, `force_empty` ou `force_content`, usando IDs estáveis. Resolver ao compor a instância; nunca editar `Resource` global ou conteúdo autorado em runtime.

Configurar dependências antes do `_ready` de nós que as utilizam. Considerar que sinais e referências a nós precisam ser limpos quando uma sala sai da árvore; estado persistente de loot ou run não deve depender da vida útil da cena. Autoload é aceitável para bootstrap ou configuração global pequena e explícita, não para um `GameManager`, jogador, inventário, sessão ou `EventBus` universal.

## Testabilidade e validação

Testes unitários devem concentrar-se nas regras sem subir `SceneTree`: pistola e recursos por jogador concedidos uma vez na nova run; fuzil/escopeta são loot e até duas armas podem ser mantidas e restore/retorno não reaplicam starter; reservas por categoria e carregadores individuais; invariante de munição incluindo descarte/disparos; recarga descarta uma vez no início, troca de arma não completa nem debita reserva tardiamente, e reserva parcial nunca excede capacidade nem munição disponível; kit permite movimento lento mas bloqueia tiro, dano/cancelamento manual consome sem curar, conclusão cura uma vez; pausa congela ação e morte/restore impede callback tardio. Duração/valor e troca de arma cancelando cura seguem pendentes. Testar snapshot/rollback de recursos, grafo com ramos dentro do orçamento, revisita sem respawn/duplicação, cobertura e papéis de inimigos. Integração confirma retorno uma vez no próximo setor e consistência do novo snapshot; wipe restaura baseline completo. Não implementar reanimação no MVP.

Testes de integração verificam cenas, transições de altura/sala, sinais e roteamento de dispositivo. Testes de regra cobrem cobertura alta/baixa, agachar, flancos, degradação e quebra; integração verifica input de agachar por dispositivo e clear por IDs de inimigo. Verificações manuais cobrem telegraphs hitscan, HUD, câmera para dois jogadores e regra escolhida para morte. Meta técnica confirmada: pelo menos 30 FPS durante gameplay em computador médio. Hardware, resolução e carga de estresse serão definidos; medir em solo e coop, com média e p95 do tempo de frame (alvo aproximado de 33,3 ms), sem exigir cap. Simulação deve ser independente da taxa de renderização. Medição de desempenho é benchmark de aceitação, não teste unitário. Framework e comandos seguem pendentes; nenhum código ou teste é entregue por este documento.

## Convenções adotadas

Usar GDScript tipado, com anotações explícitas em interfaces e estado. Adotar `snake_case` em arquivos, funções e variáveis; `PascalCase` em classes/`class_name`; indentação por tabs. Nomes de sinais descrevem fatos ocorridos em passado. Engine 4.7, linguagem, convenções e exports Windows/Linux estão escolhidos; fixar o patch exato ao configurar o projeto.

Executar a suíte de regras em modo headless no Windows e Linux; fazer smoke test dos exports e coop com dois gamepads ou teclado/mouse+gamepad. A meta mínima é 30 FPS nos exports, em solo e coop; hardware/resolução/carga de referência ainda serão definidos. Não embutir runner de testes no release. Framework e integração CI permanecem escolhas de implementação.

## Sequência de implementação sugerida

Após aprovação do GDD e briefs visuais, construir o MVP de dois setores com 4–6 salas cada: dois participantes locais; movimento/altura; hitscan e melee; salas autorais; suprimentos fixos por setor; captura e rollback completo de checkpoint. Desenvolver testes de regras e integração junto dos sistemas. Valores numéricos mínimos de vida/munição do parceiro e persistência de perfil aguardam decisões; suspensão/save de run entre execuções está fora do MVP. Aumentar setores/salas por configuração é trabalho futuro, sem engine de dificuldade agora.
