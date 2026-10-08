# JavaScript Assíncrono: fetch, async/await e estados da tela
 
## Arquivos
 
| Parte | Arquivos | Como abrir |
|---|---|---|
| 1. Validação de pedidos | `pedidos.js` | `node pedidos.js` ou colar no console do navegador |
| 2. Buscador de CEP | `cep.html`, `cep.js` | abrir `cep.html` no navegador |
| 3. Mini Pokédex | `pokedex.html`, `pokedex.js` | abrir `pokedex.html` no navegador |
 
As duas páginas usam o `style.css`. As buscas precisam de internet, porque consomem a ViaCEP e a PokéAPI.
 
## Parte 1: pedidos
 
Um pedido é válido quando o cliente é um texto não vazio (depois do `trim`) e o valor é um número maior que 0. Dos válidos, ficam só os de status "pago", o total sai de um `reduce` e cada linha é montada com `toFixed(2)`.
 
## Parte 2: Buscador de CEP
 
O CEP passa por `trim()` e por um `replace` que tira espaços e hífen, depois é validado com `/^\d{8}$/` (só dígitos e exatamente 8 caracteres). Os quatro estados da tela:
 
- **Carregando:** aviso "Buscando..." e botão desabilitado.
- **Erro:** erro de rede ou `!response.ok` caem no `catch` e mostram mensagem de falha na conexão. Se passar de 5 segundos, o `AbortSignal.timeout(5000)` cancela a requisição e o usuário recebe um aviso de demora.
- **Não encontrado:** a ViaCEP responde 200 com a chave `erro`, então o código confere `dados.erro`.
- **Sucesso:** `replaceChildren()` limpa o resultado anterior e monta Rua, Bairro, Cidade e UF com `createElement` e `textContent`.
O `finally` reabilita o botão em qualquer um dos casos. O **bônus** do histórico guarda cada busca bem-sucedida num array de objetos (`cep`, `cidade`, `uf`) e lista na tela, da mais recente para a mais antiga.
 
## Parte 3: Mini Pokédex
 
O nome é validado (não vazio) e convertido com `trim().toLowerCase()`. O `fetch` não rejeita a Promise em erros HTTP, então a função `buscarPokemon` confere a resposta: status 404 vira "Pokémon não encontrado" e qualquer outro `!response.ok` lança um erro tratado como falha de conexão. O sucesso mostra o nome, a imagem (`sprites.front_default`) e os tipos.
 
## Segurança (XSS)
 
Nenhum dado de API entra na página por `innerHTML`. Tudo usa `textContent` ou atributos como `img.src`, de modo que um texto como `<script>` apareceria como texto puro.
 
## Parte 4: Por que `Promise.all` pode ser mais rápido que vários `await` seguidos?
 
Com `await` em sequência, cada requisição só começa depois que a anterior terminou:
 
```js
const a = await fetch(urlA);
const b = await fetch(urlB);
const c = await fetch(urlC);
```
 
Se cada uma leva 300 ms, o total é cerca de 900 ms, porque o tempo de rede é somado.
 
Com `Promise.all`, as três são disparadas ao mesmo tempo e a espera acaba quando a última termina:
 
```js
const [a, b, c] = await Promise.all([fetch(urlA), fetch(urlB), fetch(urlC)]);
```
 
O total fica perto de 300 ms, o tempo da mais lenta. O ganho vem de aproveitar o tempo de rede em paralelo, já que as requisições não dependem umas das outras. Se uma precisasse do resultado da outra, o `await` em sequência seria o correto. Outro detalhe é que o `Promise.all` rejeita assim que uma das Promises falha.
 
