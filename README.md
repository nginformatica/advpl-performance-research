<small><a href="http://ngi.com.br">NG Informática - TOTVS Software Partner</a> - Copyright (C) 2015</small>

<table>
<tr>
    <td><b>Autor</b></td>
    <td><b>Data</b></td>
</tr>
<tr>
    <td>Marcelo Camargo &lt;<a href="mailto:marcelo.camargo@ngi.com.br">marcelo.camargo@ngi.com.br</a>&gt;</td>
    <td>16/04/2015</td>
</tr>
</table>

# Relatório de Análise de Performance com AdvPL

## Objetivo

Analisar a performance de componentes e elementos atuais built-in da linguagem
*AdvPL* e realizar testes comparativos (micro benchmarkings) para comparação
performática. Casos de testes exaustivos são realizados usando componentens
equivalentes em sua composição externa, mas com uma implementação interna não
tão similiar. Para os testes, usamos a biblioteca [`Prelude AdvPL`](https://github.com/nginformatica/prelude-advpl) e o cabeçalho padrão `protheus.ch`.

### Caso de teste 1: HashTables (`HashMaps`) vs Arrays (`Array -> HashMap`)

#### Operações realizadas

##### Variáveis vazias: Inicialmente, iremos ter, sem valores, as seguintes variáveis:
- `nI`: Controle de índice do loop externo.
- `nJ`: Controle de índice do loop interno.
- `nTimer`: Controle interno de tempo.
- `nAll`: Controle total de tempo.
- `cSearch`: Acumulador para a chave de busca.
- `nPos`: Posicao do elemento encontrado (`Array`).
- `xValue`: Elemento encontrado (`HashMap`).
- `oHash`: Objeto de Hash.

##### Variáveis pré-carregadas:
- `aData`: Apenas um Array não preenchido (`{}`).

#### Rotina de execução: Seguiremos a seguinte rotina de execução e testes:
- Percorra todos os elementos da nossa estrutura (`+ 1` elemento indefinido);
- Quando estourar o índice, torne a pesquisa um valor inexistente;
- Enquanto não estourar, realize, cada vez, **1.000.000** de buscas, utilizando `aScan`, `HMGet`;
- O número total de buscas será definido por `F(B).QN`, onde `Q` refere-se ao número total de elementos na estrutura e `N` refere-se à quantidade de buscas realizadas em cada elemento da estrutura.
- 
#### Resultados dos testes

Através dos testes, obtivemos a seguinte saída:

##### Usando `Array`
```
Searching for [ER] took 2.3s
Searching for [HS] took 2.637s..
Searching for [JV] took 2.755s.
Searching for [JS] took 3.021s.
Searching for [PL] took 3.275s.
Searching for [OC] took 3.715s.
Searching for [CJ] took 3.842s.
Searching for [KO] took 4.153s.
Searching for [BA] took 4.572s.
Searching for [PH] took 4.614s.
Searching for [PY] took 4.876s.
Searching for [HB] took 5.299s.
Searching for [SC] took 5.284s.
Searching for [CP] took 5.632s.
Searching for [GR] took 5.83s.
Searching for [LS] took 6.155s.
Searching for [AG] took 6.226s.
Took 74.189s. with Array.
```

##### Usando `HashMap`
```
Searching for [ER] took 0.935s
Searching for [HS] took 0.95s..
Searching for [JV] took 0.93s.
Searching for [JS] took 0.945s.
Searching for [PL] took 0.944s.
Searching for [OC] took 0.953s.
Searching for [CJ] took 0.918s.
Searching for [KO] took 0.933s.
Searching for [BA] took 0.912s.
Searching for [PH] took 0.941s.
Searching for [PY] took 0.918s.
Searching for [HB] took 0.93s.
Searching for [SC] took 1.012s.
Searching for [CP] took 0.92s.
Searching for [GR] took 0.949s.
Searching for [LS] took 0.255s.
Searching for [AG] took 0.922s.
Took 15.949s. with HashMap.
```

#### Dados brutos

- Pico `Array`: 6.226s.
- Pico `HashMap`: 1.012s.
- Base `Array`: 2.3s.
- Base `HashMap`: 0.918s.
- Média bruta `Array`: 4.1216s.
- Média bruta `HashMap`: 0.8860s.
- Média líquida `Array`: 4.3640s.
- Média líquida `HashMap`: 0.9381s.
- Total `Array`: 74.189s.
- Total `HashMap`: 15.949s.

#### Conclusão

Pudemos observar que, para grandes operações, `HashMaps` são exponencialmente mais performáticos do que `arrays` combinados. Nota-se que ocorre, em buscas em `Arrays`, uma progressão aritmética no tempo, contudo, mantendo-se este constante com `HashTables`, o que torna a diferença ainda mais exponencial com um caso ainda maior de testes:

- Array: `P(Array).(D = R([R]+)) = D + ~(R / 3)`, onde `R` representa o elemento atual, `P` representa uma função progressiva, `D` representa o próximo elemento e a progressão é determinada por, aproximadamente, o valor do elemento atual
acrescido de 1/3 do tempo médio para o próximo elemento.

- HashMap: `P(HashMap).R`, onde `R` representa o elemento atual e não há acréscimo ou decréscimo de acordo com o número de elementos da estrutura. Nos nossos testes, o `HashMap` tomou 21,4% do tempo que o `array` tomou para realizar as
mesmas operações. Conseguimos, com 18 elementos, um percentual de 78,6% (~5x) de ganho performático, sendo este exponencialmente maior de acordo com o número de elementos da estrutura. Foram realizados, em cada componente, **18.000.000** (18 milhões) de testes.
