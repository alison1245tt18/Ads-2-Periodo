# Sistema de Análise de Dados

Programa em Python que lê um arquivo CSV com dados de pessoas (nome, e-mail, CPF, telefone e data de nascimento), valida cada campo com expressões regulares e gera um relatório com o que passou e o que não passou na validação.

## Como rodar

```
python analise_dados.py              # usa o dados.csv da pasta
python analise_dados.py outro.csv    # usa outro arquivo
```

O relatório aparece no terminal e também é salvo em `relatorio.txt`. Só é preciso ter o Python 3 instalado, sem bibliotecas externas.

## Arquivos

- `analise_dados.py`: programa principal
- `dados.csv`: arquivo de exemplo (10 linhas, 3 válidas e 7 com algum problema)
- `relatorio.txt`: gerado a cada execução

## Validações

Os padrões estão em raw strings para o Python não interpretar as barras invertidas.

| Campo | Padrão | Aceita |
|---|---|---|
| E-mail | `^[\w\.-]+@[\w-]+(\.[\w-]+)*\.[a-zA-Z]{2,}$` | `ana@email.com`, `a.b@x.com.br` |
| CPF | `^\d{3}\.\d{3}\.\d{3}-\d{2}$` | `123.456.789-09` |
| Telefone | `^\(\d{2}\) 9?\d{4}-\d{4}$` | `(98) 98765-4321`, `(21) 3456-7890` |
| Data | `^\d{2}/\d{2}/\d{4}$` | `15/03/1995` |

Escolhi exigir o CPF e o telefone já formatados (com pontos, traço e parênteses) porque assim o arquivo fica padronizado. O CPF é validado só pelo formato, sem conferir os dígitos verificadores.

Como a regex da data só olha o formato, `31/02/1999` passaria por ela. Por isso, depois do regex, a data é convertida com `datetime.strptime`, que levanta `ValueError` quando o dia não existe.

## Exceções tratadas

**Na leitura do arquivo** (`try/except/else/finally`):

- `FileNotFoundError`: o arquivo informado não existe. O programa avisa e encerra sem quebrar.
- `KeyError`: falta alguma das colunas esperadas no cabeçalho do CSV.
- `else`: roda só se a leitura deu certo e mostra quantos registros foram lidos.
- `finally`: sempre mostra "Leitura finalizada", com ou sem erro.

**Na validação de cada linha:**

- `ValueError`: a data tem o formato certo mas não existe (como 31/02).
- `FormatoInvalidoError` (personalizada): lançada quando um campo não bate com o regex. Guarda o nome do campo e o valor recebido.
- `IdadeInvalidaError` (personalizada): regra de negócio. A idade calculada pela data de nascimento precisa estar entre 0 e 120 anos, então datas no futuro ou muito antigas são rejeitadas.

Uma linha inválida não interrompe o programa. O erro é registrado e a análise segue para a próxima linha.

## Exemplo

Entrada (`dados.csv`, primeiras linhas):

```
nome,email,cpf,telefone,data_nascimento
Ana Souza,ana.souza@email.com,123.456.789-09,(98) 98765-4321,15/03/1995
Bruno Lima,bruno.lima@gmail,987.654.321-00,(11) 91234-5678,22/07/1988
Diego Alves,diego.alves@email.com,12345678900,(98) 99999-0000,05/05/1990
```

Saída (`relatorio.txt`, trecho):

```
RELATÓRIO DE VALIDAÇÃO
----------------------------------------
Total de registros: 10
Válidos:            3 (30.0%)
Inválidos:          7
Idade média (válidos): 32.0 anos

Registros inválidos:
  linha 3   Bruno Lima      email com formato inválido: 'bruno.lima@gmail'
  linha 5   Diego Alves     cpf com formato inválido: '12345678900'
  linha 7   Fabio Nunes     data inexistente: '31/02/1999'
  linha 8   Gabi Melo       idade fora do aceitável: -4 anos

Erros por campo:
  email: 1
  cpf: 1
  telefone: 1
  data_nascimento: 4
```

A idade média e as idades dos casos inválidos dependem da data em que o programa é executado.
