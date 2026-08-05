# Backlog — Roteiro de Entregas (planejamento_entregas.html)

> Lista de melhorias pedidas em 2026-08-04, a implementar quando o limite de uso for restaurado. Cada item referencia o comportamento atual em [planejamento_entregas.html](planejamento_entregas.html) (e seu espelho em `scratchpad/planejamento_template.html` durante o desenvolvimento).

## Layout / visual

- [ ] **Grade semanal**: dentro de cada mês, adicionar linhas verticais de separação de semana, mais claras/discretas que a linha de fronteira de mês já existente.
- [ ] **Painel "Editar Item" à esquerda**: mover o painel deslizante de edição para abrir do lado esquerdo da tela (hoje abre pela direita). O mesmo painel é usado tanto para editar quanto para criar (Nova fase/Sub-item), então a mudança vale para os dois casos.
- [ ] **Espaçamento no painel de edição**: melhorar o espaçamento visual entre os campos "Responsável" e "Status".
- [ ] **Largura da coluna esquerda ajustável**: permitir arrastar/redimensionar a largura da coluna de Grupo/Entrega/Responsável (hoje fixa em 340px).
- [ ] **Remover subtítulo**: tirar o texto "Escopo, prazos e responsáveis até janeiro de 2027. Clique em qualquer item para editar." abaixo do título, subindo o restante do conteúdo.
- [ ] **Barra de rolagem horizontal**: garantir que fique visível na parte inferior da página.
- [ ] **Renomear botão**: "+ Nova fase" → "+ Nova Tarefa".
- [ ] **Renomear cabeçalho de coluna**: "Grupo / Entrega" → "Grupo / Tarefa".
- [ ] **Corrigir texto de ajuda** no painel de edição: "Define a hierarquia (fase → grupo → entrega)." → "Define a hierarquia (Grupo → Tarefa)."
- [ ] **Badge de contagem**: se o item não tem sub-itens, não mostrar nada (nem "0 entregas" nem "A definir"). Se tem sub-itens, mostrar só o número (ex.: "3", sem a palavra "entregas").

## Novos campos por item

- [ ] **Categoria**: campo novo por item, com lista fixa inicial: `Bases`, `Melhorias`, `Novo CRM`, `Salesforce`, `Automatizações`.
- [ ] **Descrição**: campo novo por item, caixa de texto longa (textarea).
- [ ] **Listas fechadas**: "Responsável" e "Categoria" deixam de ser texto livre (com sugestão) e passam a ser seleção obrigatória de uma lista cadastrada (ver menu de configurações abaixo).
  - ⚠️ Nota: itens existentes com um responsável que não estiver mais na lista cadastrada precisarão de alguma regra de migração (mantive como ponto em aberto).

## Menu de configurações (ícone de engrenagem, canto superior direito)

- [ ] Adicionar botão de engrenagem no canto superior direito, abrindo um menu/painel com:
  - [ ] Alternar modo claro/escuro (hoje o tema só segue o SO/navegador).
  - [ ] Gerenciar Responsáveis — criar, editar, excluir. Um responsável pode existir cadastrado sem nenhuma tarefa atribuída (lista de pessoas independente das tarefas).
  - [ ] Gerenciar Categorias — criar, editar, excluir.
  - [ ] Mover o botão "Adicionar Marco" para dentro deste menu (sai da barra superior).
  - [ ] Exportar em Excel (`.xlsx`), como opção adicional ao "Exportar" (JSON) que já existe.

## Nova visão

- [ ] **"Por Categoria"**: terceira visão agrupada, no mesmo padrão de "Por entrega" e "Por responsável", mas agrupando pelas categorias cadastradas.

## Datas em cascata (item mais complexo — releia com atenção)

- [ ] **Data automática do pai = intervalo dos filhos**: início = mínimo dos inícios dos filhos; fim = máximo dos fins dos filhos. Calculado automaticamente, não editável direto.
  - ⚠️ Exceção citada: "desconsiderando a raiz inicial (fase macro)" — não ficou claro se isso significa (a) a fase raiz NÃO segue essa regra automática (mantém datas próprias, editáveis), ou (b) é só uma observação de que a fase raiz normalmente já cobre toda a janela do projeto. **Vou confirmar isso com você antes de implementar.**
- [ ] **Barra alta quando recolhido**: quando um item com sub-itens está com a visualização recolhida (collapsed), a barra fina (envelope) atual vira uma barra "cheia", do mesmo tamanho visual das barras de tarefa normais, cobrindo o intervalo mínimo-início / máximo-fim.
- [ ] **Ajuste em cascata**: ao arrastar o início/fim dessa barra do item recolhido:
  - Sub-itens cujo início/fim ficava dentro do intervalo antigo mas fora do novo intervalo são ajustados (clampados) para caber no novo intervalo.
  - Sub-itens que já estavam dentro do novo intervalo permanecem inalterados.
  - Interpretação: é essencialmente um "clamp" — o sub-item só muda se a nova borda do pai "invadir" o espaço que ele ocupava.

## Pontos para confirmar antes de implementar

1. A exceção da "fase macro" na regra de data automática (ver item acima).
2. Se "Responsável" vazio (sem ninguém) deve aparecer como uma opção selecionável no campo (ex.: "— Nenhum —") já que a lista passa a ser fechada.
3. Se ao trocar tema no menu de engrenagem isso deve sobrescrever a preferência do sistema operacional permanentemente (salvo no estado) ou só durante a sessão.
