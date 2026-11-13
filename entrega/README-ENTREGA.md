# 1. Defeitos corrigidos

Durante a execução do comando `pytest -q`, foram identificados cinco testes com falhas. Anotei os erros apresentados e investiguei cada um deles utilizando a busca global do VS Code (`Ctrl + Shift + F`) para localizar os trechos de código relacionados.

## 1.1. Conversão de moeda para BRL

- Teste: `tests/test_currency.py`
- Arquivo: `src/onfly_report/service/currency_converter.py`
- Causa raiz: A operação de conversão estava incorreta, utilizando uma lógica inadequada para multiplicar os valores, além do uso de `float` em vez de `Decimal`.
- Investigação e abordagem: Analisei a função responsável pela conversão e identifiquei que o cálculo correto deveria ser `amount * rate`. Também identifiquei que ambos os valores já estavam convertidos para `Decimal`, não sendo necessária uma nova conversão.
- Validação: Após as alterações, o teste de conversão passou.

## 1.2. Cálculo de noites entre meses diferentes

- Teste: `tests/test_nights.py`
- Arquivo: `src/onfly_report/services/stay_calculator.py`
- Causa raiz: O cálculo considerava apenas os dias do mês (`.days`), em vez de utilizar a diferença entre as datas completas de check-in e check-out.
- Investigação e abordagem: Analisei o cálculo das datas e utilizei o ChatGPT para entender melhor o funcionamento de `datetime` e como realizar a subtração corretamente. A IA orientou a correção, e também consultei as explicações para compreender o problema.
- Validação: Após o ajuste, o cálculo passou a considerar corretamente a diferença entre as datas, inclusive na mudança de mês.

## 1.3. Relatório de regressão

- Teste: `tests/test_regression_report.py`
- Causa raiz: As divergências no resultado do relatório eram consequências dos erros na conversão de moedas e no cálculo de noites.
- Investigação e abordagem: Analisei a comparação entre os dicionários esperados e obtidos e identifiquei que as correções realizadas nos dois problemas anteriores também resolveram essa falha.
- Validação: Após as correções de câmbio e datas, o teste passou sem necessidade de uma alteração específica nesse ponto.

## 1.4. Regras de status

- Teste: `tests/test_status_rules.py`
- Arquivo: `src/onfly_report/services/status_policy.py`
- Causa raiz: A soma dos valores estava considerando incorretamente os status das reservas, incluindo valores de `no_show` junto aos confirmados.
- Investigação e abordagem: Analisei a condição utilizada na soma. Inicialmente, tentei utilizar `status != cancelled or no_show`, mas a condição não apresentou o resultado esperado. Depois, alterei a lógica para considerar apenas `status == confirmed`, que atendia à regra necessária.
- Validação: A alteração fez com que apenas as reservas confirmadas fossem consideradas, e o teste passou.

## 1.5. Ranking dos hotéis

- Teste: `tests/test_top_hotels.py`
- Causa raiz: A lista de hotéis estava sendo retornada em uma ordem diferente da esperada, sem respeitar a classificação decrescente.
- Investigação e abordagem: Analisei a ordenação da lista e identifiquei a necessidade de inverter a ordem dos resultados. Para isso, adicionei o parâmetro `reverse=True` à ordenação.
- Validação: Após a alteração, os hotéis passaram a ser apresentados em ordem decrescente e o teste passou.

# 2. Uso de Inteligência Artificial

Durante a investigação, utilizei o ChatGPT como ferramenta de apoio para esclarecer dúvidas sobre o código e compreender melhor alguns conceitos, principalmente o funcionamento de `datetime` e o cálculo de diferenças entre datas.

Também utilizei a IA para tirar dúvidas durante a análise dos problemas. As alterações foram avaliadas e aplicadas por mim, com base no comportamento esperado dos testes. Além disso, a utilizei como apoio na elaboração e organização de todas as partes da documentação do projeto.

# 3. Minha experiência e dificuldades

Esta foi minha primeira experiência trabalhando diretamente com testes automatizados. Estou começando a estudar esse conteúdo agora na faculdade, em Programação Modular, utilizando Java. Apesar de a linguagem ser diferente, consegui compreender e aplicar os conceitos também em Python.

Minhas maiores dificuldades foram entender e manipular as datas utilizando datetime e corrigir a ordenação das listas de hotéis, principalmente para atender aos critérios de classificação esperados.

# 4. Como validar

Para executar os testes automatizados, utilize o comando: `pytest -q`

# 5. Resultado dos testes

Os cinco defeitos identificados foram corrigidos. As alterações foram verificadas por meio dos testes automatizados.

Resultado final do pytest: 


(.venv) PS C:\Users\gpier\Downloads\onfly-desafio\onfly-desafio> pytest -q
.....                                                                                                      [100%]
5 passed in 0.60s

