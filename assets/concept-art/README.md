# Concept art — Project WarRoom / Frente de Cinzas

Este diretório guarda **estudos visuais exploratórios** originados nas conversas de concept art do ChatGPT. Nenhuma imagem aqui é um sprite final, tilemap aprovado ou requisito de gameplay. O GDD em `docs/GDD.md` e os briefs em `docs/art/` prevalecem sobre interpretações presentes nas imagens.

## Organização

- `environments/trenches/`: trincheiras, terra de ninguém, artilharia e barricadas.
- `environments/headquarters/`: salas de comando, bunkers e quartéis subterrâneos, inclusive QG inimigo.
- `environments/forest/`: florestas e acampamentos diurnos/noturnos.
- `environments/snow/`: campos de neve, acampamentos e rotas congeladas.
- `characters/`: futuros estudos de personagens, jogadores, inimigos e bosses.
- `equipment/`: futuros estudos de armas, armaduras, ferramentas e consumíveis.
- `props/`: futuros estudos de objetos de cenário, barricadas, vegetação e cobertura.
- `ui/`: futuros estudos de interface e HUD.
- `style-guides/`: futuros estudos de paletas, escala, perspectiva e iluminação.

Arquivos de produção utilizáveis pela engine devem ficar em `assets/sprites/`, `assets/tilesets/`, etc., **não aqui**. Novos estudos são adicionados sem sobrescrever variantes antigas.

## Nomenclatura e revisão

Usar nomes em inglês, `kebab-case`, numerados por variante (`sunny-camp-01.png`); nunca incluir datas, `final` ou `approved` no nome. O estado de revisão vai no manifesto/documentação. Registrar descrição, origem e status, e comparar com o GDD antes de promover para asset jogável. Não presumir que insígnias, armas, tecnologias ou uniformes presentes nas gerações estejam aprovados.

## Importação e cautelas

PNG original em resolução de concept, não sprite pronto. As cenas são esboços visuais e podem conter anacronismos ou características incompatíveis com a guerra fictícia definida no GDD. Evitar importar automaticamente como recursos de jogo até haver seleção, recorte e validação artística.

`manifest.json` lista todos os arquivos e seus SHA-256 para auditoria e deduplicação.
