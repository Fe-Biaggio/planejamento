# Backlog — Roteiro de Entregas (planejamento_entregas.html)

> Lista de melhorias pedida em 2026-08-04. **Implementada em 2026-08-05** (após o limite ser restaurado). Mantido como registro histórico das decisões tomadas nos pontos que estavam em aberto.

## Layout / visual

- [x] **Grade semanal**: linhas verticais tracejadas mais claras dentro de cada mês (segunda-feira como início de semana).
- [x] **Painel "Editar Item" à esquerda**: painel de edição/criação agora abre do lado esquerdo.
- [x] **Espaçamento no painel de edição**: separador e respiro extra antes do bloco de Status.
- [x] **Largura da coluna esquerda ajustável**: alça de arraste na borda da coluna, largura persistida.
- [x] **Remover subtítulo**: removido; cabeçalho mais compacto.
- [x] **Barra de rolagem horizontal**: já existia via `overflow:auto` do board-scroll — confirmado visível.
- [x] **Renomear botão**: "+ Nova fase" → "+ Nova Tarefa".
- [x] **Renomear cabeçalho de coluna**: "Grupo / Entrega" → "Grupo / Tarefa".
- [x] **Corrigir texto de ajuda**: "Define a hierarquia (Grupo → Tarefa)."
- [x] **Badge de contagem**: sem sub-itens → nada; com sub-itens → só o número.

## Novos campos por item

- [x] **Categoria**: campo novo, lista fechada inicial (`Bases`, `Melhorias`, `Novo CRM`, `Salesforce`, `Automatizações`).
- [x] **Descrição**: campo novo, textarea.
- [x] **Listas fechadas**: Responsável e Categoria agora são `<select>` restritos às listas cadastradas (`state.people` / `state.categories`), com opção "— Nenhum —"/"— Nenhuma —".

## Menu de configurações (engrenagem, canto superior direito)

- [x] Botão de engrenagem com menu:
  - [x] Alternar modo claro/escuro (persistido em `state.theme`; sem escolha explícita, segue o SO).
  - [x] Gerenciar Responsáveis (criar, renomear, excluir — renomear atualiza as tarefas atribuídas; excluir deixa as tarefas sem responsável).
  - [x] Gerenciar Categorias (mesma lógica).
  - [x] "Adicionar Marco" movido para dentro do menu.
  - [x] Exportar em Excel — implementado como **CSV** (ver decisão abaixo).

## Nova visão

- [x] **"Por Categoria"**: terceira visão, mesmo padrão de "Por responsável", agrupando pelas categorias das tarefas (categoria vazia agrupa em "Sem categoria").

## Datas em cascata

- [x] **Data automática do grupo = intervalo dos filhos**: implementado para todo grupo com `depth > 0`. A **fase raiz (depth 0) é a exceção**: usa suas próprias datas manuais quando definidas, caindo para o intervalo calculado dos filhos só se não tiver datas próprias.
- [x] **Barra cheia ao recolher**: grupos com `depth > 0` recolhidos mostram uma barra grande (estilo tarefa) com o nome do grupo, cobrindo o intervalo mínimo–máximo dos filhos.
- [x] **Ajuste em cascata**:
  - Arrastar pelo meio (mover) desloca **todos** os sub-itens pelo mesmo número de dias.
  - Arrastar uma borda (redimensionar) só ajusta (clampa) os sub-itens cujo início/fim ficaria fora do novo intervalo; os demais não mudam.

## Decisões nos pontos que ficaram em aberto

1. **Exceção da fase raiz**: fases (depth 0) mantêm data própria e editável; grupos abaixo delas sempre seguem o cálculo automático dos filhos.
2. **Responsável vazio**: adicionada opção "— Nenhum —" no select.
3. **Persistência do tema**: fica salva em `state.theme` (sobrevive a recarregar a página no mesmo navegador), não é só da sessão.
4. **"Exportar em Excel"**: implementado como exportação **CSV** (abre nativamente no Excel), não `.xlsx` binário — a extensão `.xlsx`/`.xls` não está na lista de formatos liberados pela função de download do artifact; `.csv` está.
