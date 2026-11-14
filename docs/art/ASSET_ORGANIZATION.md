# Biblioteca de concept art

**Status:** organização de assets definida; imagens históricas aguardam importação binária ao repositório.

Os PNGs de concept art gerados nas conversas do Project WarRoom devem ser guardados em `assets/concept-art/`, separados de imagens finais de produção. O GDD (`docs/GDD.md`) e os briefs (`docs/art/`) permanecem como fonte de verdade para decisões aprovadas.

## Taxonomia

```text
assets/
  concept-art/
    environments/
      trenches/       # trincheiras, artilharia, terra de ninguém
      headquarters/   # QGs aliados/inimigos e bunkers subterrâneos
      forest/         # clareiras, acampamentos, noite
      snow/           # campos de neve, rotas congeladas
    characters/       # jogadores, inimigos, NPCs, bosses
    equipment/        # armamentos, acessórios, consumíveis
    props/            # barricadas, árvores, mobiliário
    ui/               # HUD, inventário, telas
    style-guides/     # cores, escala, iluminação, forma
    manifest.json     # procedência, hashes e descrição
```

## Fluxo editorial

1. **Explorar:** imagens geradas são estudos de conceito, não assets finais nem requisitos aprovados.
2. **Catalogar:** salvar PNG com nome descritivo e variante em `kebab-case` (ex.: `night-camp-01.png`), com origem e SHA-256 no manifesto.
3. **Revisar:** comparar com o GDD e com os briefs oficiais; marcar aceitação/pendência documental sem renomear como `final`.
4. **Produzir:** somente conceitos aprovados podem servir de referência para `assets/sprites/`, `assets/tilesets/` e `content/` (recursos Godot), com produção e validação próprias.

A guerra do GDD é **fictícia**, inspirada na primeira metade do século XX. Insígnias historicamente reconhecíveis, armas, composição de unidades, estética de uniformes e estruturas geradas por IA **não** definem facções, equipamentos ou mecânicas oficiais. Inclusive as imagens de neve e floresta podem representar exploração pós-MVP, sem alterar os setores confirmados para o MVP.

## Sincronização ChatGPT ↔ Codex

O GitHub guarda o arquivo de imagem e a documentação versionada. O ChatGPT fornece propostas visuais e catalogação; o Codex consulta `docs/GDD.md`, `docs/art/` e `assets/concept-art/` antes de implementar ou formalizar uma decisão. Não usar uma conversa como fonte definitiva nem alterar o GDD somente para refletir uma geração de imagem.

**Importação histórica:** existe um pacote ZIP de 17 imagens desta conversa, com `assets/concept-art/README.md` e `manifest.json`, preparado para importação local via git. Os PNGs só serão visíveis no GitHub após o push desse pacote.
