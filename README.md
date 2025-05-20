# Refatoração do Sistema de Vendas com Object Calisthenics

# ALUNO: Amadeu Seabra - Turma: 1 Noturno - Modulo: 5

## ✅ Etapa 1: Análise Inicial – Violações dos Princípios de Object Calisthenics

Abaixo estão listadas as violações encontradas no código original da classe `Venda`, com base nos 9 princípios de **Object Calisthenics**:

---

### 1. Only One Level of Indentation per Method *(Apenas um nível de indentação por método)*

* O método `calcDesc()` possui múltiplos níveis aninhados (`if` dentro de `if`).
* O método `genRpt()` mistura laço `for` com chamadas de métodos e concatenação.

---

### 2. Don't Use the ELSE Keyword *(Não use a palavra-chave ELSE)*

* O `else` é utilizado no método `calcDesc()`, o que pode ser evitado com retornos antecipados.

---

### 3. Wrap All Primitives and Strings *(Encapsule todos os primitivos e Strings)*

* Os atributos `double valTot`, `boolean conf`, `String cli` e `List<String> itns` são tipos primitivos ou Strings diretos.

---

### 4. First Class Collections *(Coleções como objetos de primeira classe)*

* A coleção `List<String> itns` deveria estar encapsulada em uma classe como `ItensVenda` para conter a lógica associada.

---

### 5. One Dot per Line *(Um ponto por linha)*

* O acesso `not.send(...)` mostra acoplamento direto com métodos internos de um objeto composto.

---

### 6. Don't Abbreviate *(Não abrevie)*

* Abreviações excessivas como `valTot`, `cli`, `conf`, `itns`, `not`, `itm` comprometem a clareza do código.

---

### 7. Keep All Entities Small *(Mantenha todas as classes pequenas)*

* A classe `Venda` tem muitas responsabilidades: adiciona itens, calcula desconto, gera relatório, confirma venda e envia notificação.

---

### 8. No Classes with More Than Two Instance Variables *(No máximo duas variáveis por classe)*

* A classe `Venda` possui **cinco atributos**, ultrapassando o limite recomendado.

---

### 9. Use Getters/Setters with Caution *(Use getters/setters com cautela)*

* Getter e setter diretos para `conf`. Além disso, o setter possui lógica acoplada (`not.send(...)`).

---

Essas violações comprometem a manutenção, reutilização e clareza do código. A próxima etapa é aplicar os princípios para realizar uma refatoração completa e aderente a boas práticas de projeto orientado a objetos.

---

## ✅ Etapa 2: Refatoração com base nos 9 Princípios de Object Calisthenics

### 1. Only One Level of Indentation per Method

**Como foi resolvido:**

* A maioria dos métodos foi reestruturada para conter apenas um nível de indentação, utilizando retornos antecipados. Por exemplo, os métodos `adicionar` em `ItensVenda`, `confirmar` em `Confirmacao` e `enviar` em `NotificacaoEmail` possuem lógica simples com um único nível de indentação.
* **Observação**: O método `calcularDesconto` em `DetalhesVenda` possui dois níveis de indentação devido a um `if` aninhado (`if (itens.quantidade() > 5)`), violando parcialmente este princípio.

### 2. Don't Use the ELSE Keyword

**Como foi resolvido:**

* O uso de `else` foi eliminado em todos os métodos. Em `calcularDesconto` da classe `DetalhesVenda`, retornos antecipados foram usados para evitar `else` explícito, utilizando blocos `if` consecutivos para lidar com as condições de desconto.
* Exemplo: Em `calcularDesconto`, a lógica usa retornos diretos para valores de desconto baseados nas condições, sem `else`.

### 3. Wrap All Primitives and Strings

**Como foi resolvido:**

* Todos os tipos primitivos e strings foram encapsulados em classes específicas:
  * `valTot` (double) foi encapsulado na classe `ValorTotal` com validação para valores não positivos.
  * `cli` (String) foi encapsulado na classe `Cliente` com validação para nomes nulos ou vazios.
  * `conf` (boolean) foi encapsulado na classe `Confirmacao`.
  * `itns` (List<String>) foi encapsulado na classe `ItensVenda`, com cada item em `ItemVenda`, que encapsula a string `descricao`.
* Cada classe inclui validações no construtor para garantir a integridade dos dados.

### 4. First Class Collections

**Como foi resolvido:**

* Criada a classe `ItensVenda`, que encapsula a lista de itens (`List<ItemVenda>`) e fornece métodos como `adicionar()`, `quantidade()` e `listar()`. O método `listar()` retorna uma cópia da lista para proteger a coleção interna, garantindo encapsulamento.

### 5. One Dot per Line

**Como foi resolvido:**

* A maioria dos acessos a objetos foi simplificada para evitar chamadas encadeadas. Por exemplo, em `RelatorioVenda.gerar`, acessos como `cliente.getNome()` e `detalhes.calcularDesconto(cliente)` usam um único ponto por chamada.
* **Observação**: Expressões como `detalhes.getValorTotal().getValor()` em `RelatorioVenda.gerar` violam este princípio, pois contêm dois pontos em uma única linha.

### 6. Don't Abbreviate

**Como foi resolvido:**

* Todas as abreviações do código original foram substituídas por nomes descritivos, como `Cliente` (em vez de `cli`), `ValorTotal` (em vez de `valTot`), `ItensVenda` (em vez de `itns`), `Confirmacao` (em vez de `conf`), `calcularDesconto` (em vez de `calcDesc`) e `gerarRelatorio` (em vez de `genRpt`).

### 7. Keep All Entities Small

**Como foi resolvido:**

* A classe `Venda` original foi dividida em várias classes menores, cada uma com uma responsabilidade clara: `Cliente`, `ValorTotal`, `Confirmacao`, `ItemVenda`, `ItensVenda`, `RelatorioVenda`, `Venda`, `Notificacao` e `NotificacaoEmail`.
* Métodos foram mantidos curtos, como `adicionar` em `ItensVenda` e `confirmar` em `Confirmacao`.
* **Observação**: O método `calcularDesconto` em `DetalhesVenda` é relativamente complexo e poderia ser dividido em métodos menores para maior granularidade.

### 8. No Classes with More Than Two Instance Variables

**Como foi resolvido:**

* A maioria das classes possui no máximo dois atributos:
  * `Cliente`, `ValorTotal`, `Confirmacao`, `ItemVenda` e `ItensVenda` possuem um atributo cada.
  * `Venda` e `RelatorioVenda` possuem dois atributos cada (`cliente` e `detalhes`).
* **Observação**: A classe `DetalhesVenda` possui três atributos (`valor`, `itens`, `confirmacao`), violando este princípio.

### 9. Use Getters/Setters with Caution

**Como foi resolvido:**

* Todos os setters foram eliminados, e o estado é modificado por métodos de comportamento, como `confirmar` em `Confirmacao` e `adicionar` em `ItensVenda`.
* **Observação**: Getters ainda estão presentes em várias classes (`Cliente.getNome()`, `ValorTotal.getValor()`, `DetalhesVenda.getValorTotal()`, `DetalhesVenda.getItens()`, etc.), violando parcialmente este princípio, pois expõem o estado interno diretamente.

---

## Resumo

A refatoração abordou a maioria das violações do código original, melhorando a legibilidade, manutenibilidade e robustez do sistema. Os pontos fortes incluem o encapsulamento de tipos primitivos, a eliminação de abreviações, a criação de coleções de primeira classe e a divisão de responsabilidades em classes menores. No entanto, algumas violações parciais permanecem, como o uso de dois níveis de indentação em `calcularDesconto`, chamadas com múltiplos pontos (ex.: `detalhes.getValorTotal().getValor()`), mais de dois atributos em `DetalhesVenda` e o uso de getters que expõem o estado interno.
