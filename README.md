# Sistema de Monitoramento de Temperatura

## 1. Identificação

- **Aluno:** Daniel Silva Martins
- **Disciplina:** Algoritmos e Pensamento Computacional
- **Professora:** Profa. Karla Sartin
- **Título do projeto:** Sistema de Monitoramento de Temperatura

## 2. Objetivo

O programa simula um sistema de monitoramento de temperatura, útil em contextos
como salas de servidores, câmaras frias ou ambientes industriais, nos quais é
necessário identificar rapidamente situações de superaquecimento. O usuário
define um limite de temperatura considerado seguro e, em seguida, informa
sucessivas leituras. O sistema deve alertar quando uma leitura ultrapassa o
limite e encerrar automaticamente o monitoramento caso o problema persista por
três leituras seguidas, simulando uma resposta automática a uma situação
crítica.

## 3. Funcionamento do programa

- **Limite de temperatura:** é solicitado ao usuário logo no início da
  execução. A entrada é validada — se o valor digitado não for numérico, o
  programa exibe uma mensagem de erro e solicita novamente, sem avançar para
  a fase de leitura.
- **Leituras de temperatura:** após o limite ser definido, o programa entra em
  um laço de leitura contínua, pedindo uma temperatura por vez.
- **Valores inválidos:** toda leitura passa por validação com `scanf`. Se o
  valor digitado não puder ser interpretado como número (por exemplo, texto),
  o programa avisa o usuário e solicita a mesma leitura novamente — o valor
  inválido não é contabilizado nas estatísticas nem afeta o contador de
  consecutivas.
- **Identificação de temperaturas acima do limite:** cada leitura válida é
  comparada com o limite definido. Se for maior, o sistema emite um alerta na
  tela e incrementa tanto o total de leituras acima do limite quanto o
  contador de leituras consecutivas acima do limite.
- **Contagem de consecutivas:** o contador de consecutivas é incrementado a
  cada leitura acima do limite e **reiniciado (zerado)** assim que aparece uma
  leitura dentro do limite, garantindo que apenas sequências ininterruptas
  sejam contabilizadas.
- **Condições de encerramento:**
  1. **Automática:** ocorrem 3 leituras consecutivas acima do limite.
  2. **Manual:** o usuário digita o valor sentinela `-9999`, indicando que
     deseja parar o monitoramento voluntariamente.

Ao final, em ambos os casos, o programa exibe um relatório com: quantidade de
leituras válidas, temperatura média, maior e menor temperatura, quantidade e
percentual de leituras acima do limite, e o motivo do encerramento.

## 4. Estruturas de repetição utilizadas

O programa combina `while` e `do...while`, cada um usado onde seu
comportamento (testar a condição antes ou depois da execução) fazia mais
sentido:

- **`do...while`** é usado para:
  - Pedir o **limite de temperatura**: a pergunta precisa ser feita
    **pelo menos uma vez**, e só se repete se a resposta for inválida —
    encaixa perfeitamente no padrão "faça e depois verifique".
  - O **laço principal de monitoramento** (`do { ... } while (1)`, com
    `break` interno): o programa sempre precisa solicitar e processar ao
    menos uma temperatura antes de poder avaliar qualquer condição de
    parada (automática ou manual). Não haveria sentido em testar a condição
    de parada antes de existir uma primeira leitura.
- **`while`** é usado para:
  - Validar cada temperatura digitada (laço aninhado dentro do laço
    principal), repetindo a solicitação **enquanto** a entrada continuar
    inválida.
  - Limpar o buffer de entrada (`while (getchar() != '\n')`), descartando
    caracteres residuais deixados por uma leitura inválida ou pelo `\n`
    da leitura anterior.

## 5. Como executar

```bash
gcc monitoramento.c -o monitoramento
./monitoramento
```

Em seguida, informe o limite de temperatura e, depois, uma temperatura por
vez. Para encerrar manualmente o monitoramento a qualquer momento, digite
`-9999`.

## 6. Testes realizados

As capturas de tela de cada teste estão na pasta `evidencias/` (`teste01.png`,
`teste02.png`, `teste03.png`).

### Teste 1 — Validação de entradas inválidas
**Entrada:** ao ser solicitado o limite de temperatura, foram digitados os
valores `abc` e `teste`.
**Resultado:** o programa identificou corretamente que nenhum dos dois valores
é numérico, exibiu a mensagem "Entrada invalida! Digite um valor numerico." e
repetiu a pergunta em laço até que um valor válido fosse informado, sem
avançar para a fase de leitura das temperaturas.

### Teste 2 — Temperaturas acima do limite, porém não consecutivas
**Entrada:** limite `40`, seguido das temperaturas `49, 30, 75, 21, 65`.
**Resultado:** as leituras 49, 75 e 65 ficaram acima do limite e geraram
alerta, porém sempre intercaladas por leituras dentro do limite (30 e 21).
Por isso, o contador de consecutivas foi reiniciado a cada uma delas
(permanecendo sempre em "1 consecutiva(s)"), e o monitoramento **não** foi
encerrado automaticamente, confirmando que o reinício do contador funciona
corretamente.

### Teste 3 — Três temperaturas consecutivas acima do limite
**Entrada:** limite `40`, seguido das temperaturas `42, 47, 50`.
**Resultado:** as três leituras vieram em sequência, todas acima do limite,
incrementando o contador de consecutivas até 3. Ao atingir o valor máximo, o
programa encerrou o monitoramento **automaticamente** e exibiu o relatório
final: 3 leituras válidas, média de 46,33 °C, maior temperatura 50,00 °C,
menor 42,00 °C, 100% das leituras acima do limite e motivo do encerramento
"Automático (3 temperaturas consecutivas acima do limite)".

## 7. Reflexão final

Optei por combinar `while` e `do...while` porque o problema tem duas naturezas
diferentes de repetição. Para a **validação de entradas** (limite e
temperaturas), a lógica natural é "faça a pergunta e valide depois", o que é
exatamente o que o `do...while` oferece: testar a condição *depois* de
executar o bloco garante que o usuário sempre seja questionado ao menos uma
vez, mesmo que a primeira resposta já seja válida.

A diferença entre testar a condição antes ou depois foi decisiva também no
**laço principal de monitoramento**: como o programa não tem como saber, antes
da primeira leitura, se o monitoramento deve continuar ou parar (isso só pode
ser avaliado depois que a temperatura foi lida e comparada ao limite), um
`while` tradicional exigiria uma leitura "fantasma" antes do laço só para
inicializar a condição. Usar `do...while` eliminou essa duplicação de código e
tornou a lógica mais direta: sempre lê, processa e só então decide se
continua.

Já o `while` puro fez mais sentido nos pontos em que a repetição depende de
uma condição que **já existe antes de começar** — como limpar caracteres
residuais do buffer de entrada, em que a condição (`getchar() != '\n'`) já
está definida antes de qualquer execução do laço, não havendo necessidade de
executá-lo ao menos uma vez.
