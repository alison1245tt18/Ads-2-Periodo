# Arrays, DOM e Eventos em JavaScript

Exercícios de JavaScript moderno (ES6+) divididos em três blocos: métodos de array, manipulação do DOM e eventos. Cada bloco tem sua página HTML e seu script.

## Como rodar

Abra o `index.html` no navegador e use o console (F12) para ver as saídas. Não precisa instalar nada.

| Bloco | Página | Script |
|---|---|---|
| 1. Arrays e métodos | `arrays.html` | `arrays.js` |
| 2. Manipulação do DOM | `dom.html` | `dom.js` |
| 3. Eventos e Event Delegation | `eventos.html` | `eventos.js` |

O `arrays.js` também roda direto no terminal com `node arrays.js`. No `dom.js`, troque a constante `NOME_AUTOR` pelo seu nome.

## Métodos de array

- **`forEach`** percorre o array e executa uma função para cada item. Não devolve nada, serve para efeitos como imprimir no console.
- **`map`** devolve um **novo array** do mesmo tamanho, com cada item transformado (por exemplo, nomes em maiúsculas ou só o campo `nome` de cada produto).
- **`filter`** devolve um **novo array** só com os itens que passam no teste (por exemplo, preços acima de 20).
- **`reduce`** junta todos os itens em **um único valor**, usando um acumulador. Aqui soma os preços, começando em 0.

`map` e `filter` não alteram o array original, o que dá para ver no console: depois do `map` com `toUpperCase`, o array `nomes` continua igual.

## Manipulação do DOM

- `querySelector` e `querySelectorAll` selecionam um elemento ou todos os que combinam com o seletor CSS.
- `textContent` insere só texto, sem interpretar HTML, e por isso é mais seguro. O `innerHTML` só foi usado para inserir os dois primeiros itens da lista, que são HTML escrito por mim.
- `createElement` cria o elemento na memória e `append` coloca ele na página.
- `classList.add`, `contains` e `toggle` mexem nas classes CSS sem sobrescrever as que já existem.

No bloco 2, a lista começa vazia, recebe dois itens por `innerHTML`, depois um terceiro criado com `createElement` e mais três vindos do array `tarefas`. O primeiro item recebe a classe `feito` e o total (6) é impresso no console.

## Event Delegation

Em vez de colocar um `click` em cada `<li>`, coloquei **um único listener no `<ul id="lista">`**. Os cliques nos itens sobem (*bubbling*) até a lista, e o `e.target` diz qual elemento foi clicado. O `if (e.target.tagName === "LI")` garante que só os itens reajam, e não a lista inteira.

Isso ajuda em dois pontos:

- **Performance:** é um listener só, não um por item, o que importa quando a lista tem muitos itens.
- **Elementos dinâmicos:** itens criados depois, como o criado via JavaScript e as tarefas adicionadas pelo formulário, funcionam sem precisar registrar listener nenhum, porque o da lista já cobre eles. Dá para conferir clicando nos itens novos.

## Formulário

O `submit` usa `e.preventDefault()` para a página não recarregar. O texto do campo passa por `.trim()` e, se sobrar vazio, nada é adicionado. Se tiver conteúdo, o `<li>` é criado, vai para a lista e o campo é limpo.
