# Histórico de uso da IA
Ferramenta: ChatGPT  
Modelo: GPT-6

# 1. Organização do projeto

## Prompt: 
Eu recebi esse projeto da Onfly e nunca mexi com um código organizado dessa forma. Me explique como funciona a organização das pastas, seja breve na resposta. 

Estrutura
```
.
├── README.md              # este arquivo
├── pyproject.toml
├── requirements\*.txt
├── data/
│   ├── reservas\_hoteis.csv
│   ├── taxas\_cambio.csv
│   └── exemplo-validacao.csv
├── src/onfly\_report/      # CLI
├── tests/                 # testes fornecidos
├── entrega/               # sua entrega
└── output/                # relatório gerado
```
## Resposta/apoio da IA: 
O projeto está organizado em pastas com responsabilidades separadas. Como é um projeto Python, cada pasta tem uma função específica:
O que cada item faz?
- README.md: explica o projeto, suas regras e como executá-lo.
- pyproject.toml: guarda configurações e informações do projeto Python.
- requirements.txt: lista as bibliotecas necessárias para executar o projeto.
- data/: contém os arquivos CSV que servem como entrada para o sistema.
- src/: contém o código-fonte da aplicação.
  - onfly_report/: contém a lógica responsável por ler as reservas, calcular os valores, consolidar os dados e gerar o relatório.
- tests/: contém testes automatizados que verificam se o sistema está funcionando corretamente.
- entrega/: guarda a documentação exigida para apresentar sua solução.
- output/: recebe os relatórios JSON produzidos pelo programa.
Uma coisa importante: os testes não fazem parte do funcionamento normal do relatório. Eles servem para verificar se a implementação respeita as regras de negócio.
Em resumo, o seu trabalho será investigar principalmente a pasta src/onfly_report/, pois é nela que está a lógica responsável pelos resultados incorretos.

# 2. Execução dos testes

## Prompt: 
Como eu faço para executar todos os testes pelo terminal do VS Code? me passa os comandos na ordem e explique o que cada um faz
## Resposta/apoio da IA: 
Para executar os testes do projeto Onfly pelo terminal do VS Code, siga esta ordem.
1. Abra o terminal do VS Code
No menu superior, clique em Terminal → Novo Terminal.
2. Entre na pasta do projeto
Se o terminal ainda não estiver na pasta do projeto, utilize:
cd caminho/da/pasta/Onfly
Esse comando acessa a pasta onde está o projeto.
3. Crie o ambiente virtual
python -m venv .venv
Cria um ambiente virtual para instalar as dependências sem interferir em outros projetos Python.
4. Ative o ambiente virtual
No Windows (PowerShell):
.venv\Scripts\Activate.ps1
Isso ativa o ambiente virtual. Você deverá ver (.venv) no início da linha do terminal.
5. Instale as dependências
pip install -r requirements-dev.txt
Instala as bibliotecas necessárias para executar o projeto e os testes, incluindo o pytest.
6. Execute todos os testes
pytest -q
Executa todos os testes encontrados na pasta tests/.
- pytest: ferramenta que executa os testes.
- -q: modo silencioso, mostrando um resumo mais compacto dos resultados.
Como interpretar o resultado?
Resultado	Significado
passed  	Teste passou.
failed	    Teste encontrou um erro.
error	    Ocorreu um problema durante a execução.
Dica: Como o enunciado informa que os testes começam falhando, isso é esperado. O objetivo será corrigir o código até que todos passem.

## Prompt: 
Eu executei o "pytest -q", e apareceram 5 erros. Como que eu identifico qual arquivo que eu tenho que modificar olhando apenas o test? Ou eu tenho que obrigatoriamente ler cada arquivo do src só para eu ter uma noção das funcionalidades? Responda de forma resumida de novo
## Resposta/apoio da IA: 
Você não precisa ler todos os arquivos de src/ antes de começar. O ideal é usar os testes como um mapa para encontrar os defeitos.
Como identificar o arquivo que precisa ser modificado?
1. Execute:
   pytest -q
2. Para visualizar os detalhes dos 5 erros:
   pytest -v
3. Observe as informações de cada erro:
   - Nome do teste: indica o comportamento que deveria funcionar.
   - AssertionError: indica que o resultado obtido foi diferente do esperado.
   - Caminho do arquivo: geralmente mostra onde o teste encontrou o problema.
   - Traceback: mostra a sequência de chamadas que levou ao erro.
Preciso conhecer o src/?
Sim, mas não precisa ler tudo de uma vez.
Minha recomendação é:
1. Leia o teste que falhou.
2. Identifique qual funcionalidade ele está verificando.
3. Procure no src/ o código responsável por aquela funcionalidade.
4. Leia esse arquivo e entenda seu funcionamento antes de modificar.
Uma dica: os testes mostram o comportamento esperado, enquanto o src/ mostra como esse comportamento foi implementado. Comparar os dois é o caminho para encontrar os defeitos.

# 3. Conversão de moedas

## Prompt: 
O sistema precisa converter os valores das reservas para reais usando a tabela de taxas de câmbio, o return aparenta estar apresntando problemas. Voce poderia me falar apenas qual é a formula correta para essa mudança de valor?
## Resposta/apoio da IA: 
A fórmula correta, conforme as regras do projeto, é:
valor_brl = amount * rate_to_brl
- amount: valor original da reserva.
- rate_to_brl: taxa de conversão da moeda para reais, obtida no CSV de câmbio.
- valor_brl: valor convertido para reais.

Exemplo: se uma reserva custa 100 dólares e a taxa é 5,50:
100 * 5,50 = 550,00

Importante: utilize Decimal em vez de float para evitar imprecisões nos cálculos monetários. Não arredonde durante a conversão; arredonde somente na geração do JSON.

## Prompt: 
Não entendi qual a diferença de usar decimais e float, me explique brevemente
## Resposta/apoio da IA: 
A diferença está na precisão dos números, principalmente quando trabalhamos com dinheiro.
float
É um tipo numérico que representa números decimais usando aproximações binárias. Por isso, pode apresentar pequenas imprecisões.

Exemplo em Python:
print(0.1 + 0.2)

Resultado:
0.30000000000000004

Decimal
É um tipo numérico que permite representar valores decimais com precisão, sendo mais adequado para cálculos financeiros.

Exemplo:
from decimal import Decimalprint(Decimal("0.1") + Decimal("0.2"))

Resultado:
0.3

Qual utilizar no projeto Onfly?
Tipo	Utilização
float	Cálculos gerais, aproximações e medidas.
Decimal	Valores monetários e cálculos financeiros.

# 4. Cálculo de noites

## Prompt: 
Na parte do cálculo de noites ocupadas esta essa formula, return (checkout_date.day - checkin_date.day) * rooms. Pode me explicar como funciona e como que é a logica por tras do datetime? sem repostas, apenas me induza para o raciocinio certo
## Resposta/apoio da IA: 
Vamos entender primeiro o que essa fórmula está fazendo:
return (checkout_date.day - checkin_date.day) * rooms
1. O que significa .day?
Imagine que você tenha duas datas:
checkin_date = date(2025, 1, 10)checkout_date = date(2025, 1, 13)
O atributo .day extrai apenas o dia do mês.
- checkin_date.day → 10
- checkout_date.day → 13
Percebe alguma limitação nisso?
2. Como funciona o datetime?
Em Python, o módulo datetime permite trabalhar com datas e horários.
Por exemplo:
from datetime import datedata1 = date(2025, 1, 10)data2 = date(2025, 1, 13)
Uma característica importante é que você pode realizar operações matemáticas entre datas.
Pense nesta pergunta:
Se você subtrair data1 de data2, o Python precisa considerar apenas os dias do mês ou a data completa (dia, mês e ano)?
3. Pense em um cenário diferente
Imagine uma reserva:
- Check-in: 30/01/2025
- Check-out: 02/02/2025
- Quartos: 2
Agora reflita:
1. A fórmula atual consegue calcular corretamente essa diferença?
2. O que acontece quando o mês muda?
3. E quando o ano muda?
4. Se o Python consegue subtrair duas datas completas, será que você precisa acessar .day?
4. Seu desafio
Antes de modificar o código, tente descobrir:
- Qual é o tipo de dado retornado pela subtração de duas datas?
- Qual atributo desse resultado permite obter a quantidade de dias?
- Depois de descobrir a quantidade de noites, onde entra a multiplicação por rooms?
Dica: pesquise sobre timedelta na documentação do Python.
Não vou entregar a fórmula ainda. Quero que você consiga entender por que a implementação atual está incorreta e chegue à solução por conta própria!

## Prompt: 
Já tentei de mil e uma formas e não consegui, qual que é a resposta correta?
## Resposta/apoio da IA: 
A fórmula correta é:
return (checkout_date - checkin_date).days * rooms
Entendendo cada parte
- checkout_date - checkin_date: subtrai as duas datas completas.
- .days: extrai a quantidade de dias da diferença (timedelta).
- * rooms: multiplica a quantidade de noites pelo número de quartos.
Exemplo:
checkin_date = date(2025, 1, 30)checkout_date = date(2025, 2, 2)rooms = 2
Resultado:
- Diferença: 3 dias.
- Quartos: 2.
- Total: 6 noites ocupadas.
A grande diferença é que agora o Python considera dia, mês e ano, não apenas o número do dia do mês.

## Prompt: 
Mas qual é a logica em substrair duas datas, inclusive o mes e o ano, o valor ja não deveria retornar int? Por que tem que botar o .days depois? Porque o certo não seria usar um for que contabiliza as datas?
## Resposta/apoio da IA: 
Ótima pergunta! Sua dúvida faz sentido, principalmente porque estamos acostumados a pensar em subtração como uma operação que retorna um número.
O segredo é entender que o Python trabalha com datas de uma maneira diferente.
1. Por que subtrair duas datas não retorna um int?
Quando você faz:
checkout_date - checkin_date
O Python não retorna diretamente um número inteiro. Ele retorna um objeto chamado timedelta.
Esse objeto representa a diferença entre duas datas, armazenando informações como dias, segundos e microssegundos.

Por exemplo:
from datetime import datecheckin = date(2025, 1, 30)checkout = date(2025, 2, 2)diferenca = checkout - checkinprint(diferenca)

Resultado:
3 days, 0:00:00

2. Então, para que serve .days?
Como o resultado é um objeto timedelta, você precisa acessar sua propriedade .days para obter somente a quantidade de dias como inteiro.
print(diferenca.days)

Resultado:
3

Ou seja:
Expressão	Resultado
checkout - checkin	timedelta
(checkout - checkin).days	int
3. Por que não utilizar um for?
Pense no seguinte: para descobrir quantos dias existem entre 30/01 e 02/02, você poderia percorrer cada data com um for, incrementando um contador.
Mas será que isso seria necessário?
O próprio datetime já possui uma implementação interna que sabe calcular diferenças entre datas, considerando:
- Mudanças de mês;
- Mudanças de ano;
- Anos bissextos;
- Quantidade de dias de cada mês.
Portanto, não precisamos implementar essa contagem manualmente.
4. Quero que você guarde este conceito
O datetime não serve apenas para armazenar datas. Ele também permite realizar operações matemáticas com elas.
- date: representa uma data.
- timedelta: representa uma diferença entre datas.
- .days: extrai a quantidade de dias dessa diferença.
É justamente por isso que a solução do seu projeto é:
return (checkout_date - checkin_date).days * rooms
Você não está contando datas individualmente. Está utilizando a capacidade do Python de calcular a diferença entre elas.

# 5. Ordenação de hotéis
## Prompt: 
Outro erro é que o total em reais deve ficar em ordem decrescente e que, quando houver empate, deve ser considerado o hotel_id em ordem crescente. Pode analisar a ordenação atual e me explicar o que está errado? 
"ranked_hotels = sorted(hotels.items(), key=lambda item: (item[1]["amount"], item[0]))
 top_hotels = [
            {
                "hotel_id": hotel_id,
                "hotel_name": data["hotel_name"],
                "room_nights": data["room_nights"],
                "total_brl": format_money(data["amount"]),
            }
            for hotel_id, data in ranked_hotels[: self._top_n]
        ] "
## Resposta/apoio da IA: 
Sim! O problema está na forma como você configurou a ordenação dentro do sorted().

Vamos analisar:
1. Sua ordenação atual
ranked_hotels = sorted(hotels.items(), key=lambda item: (item[1] ["amount"], item[0]))
O sorted() utiliza dois critérios:
- item[1]["amount"]: valor total em reais do hotel.
- item[0]: hotel_id.
O problema é que, por padrão, o sorted() organiza os valores em ordem crescente.
Isso significa que os hotéis com menor faturamento aparecem primeiro.
2. O que você precisa considerar?
O enunciado estabelece duas regras:
1. total_brl: ordem decrescente (maior para menor).
2. hotel_id: ordem crescente quando houver empate.
O detalhe importante é que você tem dois critérios com direções diferentes.
3. Como resolver?
Uma possibilidade é utilizar o parâmetro reverse do sorted(), mas cuidado: ele inverte a ordenação dos dois critérios simultaneamente.
Pense nisso: se você colocar reverse=True, o hotel_id também ficará em ordem decrescente.
Seu desafio: como você poderia fazer o primeiro critério ficar decrescente sem inverter o segundo?
Uma dica: pesquise sobre o operador - aplicado a números ou sobre ordenações estáveis do Python.
Assim você consegue corrigir sem alterar a estrutura do restante do código

## Prompt: 
Eu não sei utilizar esse reverse = True, me explica como funciona e onde coloca
## Resposta/apoio da IA: 
O reverse=True é um parâmetro do sorted() que inverte a ordem da classificação.
1. Como funciona?
Por padrão:
sorted([100, 300, 200])

Resultado:
[100, 200, 300]

Com reverse=True:
sorted([100, 300, 200], reverse=True)

Resultado:
[300, 200, 100]
Ou seja:
- reverse=False: ordem crescente.
- reverse=True: ordem decrescente.
2. Onde colocar no seu código?
O parâmetro fica dentro do sorted(), depois do key:
ranked_hotels = sorted(    hotels.items(),    key=lambda item: (item[1]["amount"], item[0]),    reverse=True)
3. Mas atenção!
No seu projeto existe um detalhe importante: você precisa de duas ordenações diferentes:
- Valor total: decrescente.
- hotel_id: crescente.
Se utilizar reverse=True nessa solução, os dois critérios serão invertidos.
Portanto, essa alteração isolada não resolve completamente o problema.
O próximo passo é descobrir como ordenar cada critério em uma direção diferente.

# 6. Documentação

## Prompt: 
Me faça um resumo desse chat, pois tenho que declarar o uso de IA, me fale nas coisas que voce me ajudou a entender
## Resposta/apoio da IA: 
Durante o desenvolvimento do projeto Onfly — Consolidador de reservas de hotel, utilizei o ChatGPT (modelo GPT-6) como ferramenta de apoio para compreender a estrutura do projeto, entender os defeitos identificados pelos testes automatizados e estudar conceitos de Python necessários para realizar as correções.
Resumo dos assuntos em que recebi ajuda da IA
1. Organização das pastas: entendi a função de cada diretório (data/, src/, tests/, entrega/ e output/) e como eles se relacionam no funcionamento do sistema.

2. Execução dos testes: aprendi a configurar o ambiente virtual no VS Code, instalar as dependências com requirements-dev.txt e executar os testes utilizando pytest -q e pytest -v.

3. Interpretação dos testes: compreendi como utilizar os erros apresentados pelo pytest para identificar funcionalidades com problemas e localizar os arquivos responsáveis no código-fonte.

4. Conversão de moedas: entendi a fórmula de conversão de valores para reais, multiplicando amount pela taxa rate_to_brl.

5. Precisão monetária: aprendi a diferença entre float e Decimal, entendendo por que Decimal é mais adequado para operações financeiras e por que o arredondamento deve ocorrer somente na serialização do relatório.

6. Cálculo de noites: aprendi como funciona o módulo datetime, a diferença entre os objetos date e timedelta e como a subtração de duas datas permite calcular corretamente a quantidade de noites, inclusive em mudanças de mês e ano.

7. Ordenação de hotéis: analisei o funcionamento de sorted() e lambda, identificando que a ordenação original estava em ordem crescente. Também aprendi sobre o parâmetro reverse=True e como ele inverte a ordenação dos critérios.

8. Análise do código de ordenação: compreendi como o sorted() utiliza uma função lambda para definir os critérios de classificação e identifiquei a necessidade de ordenar o faturamento em ordem decrescente e o hotel_id em ordem crescente nos casos de empate.

9. Raciocínio e investigação: a IA explicou os conceitos e apresentou exemplos em Python, procurando orientar a compreensão dos problemas antes de fornecer soluções diretas. Em alguns momentos, recebi desafios para tentar chegar à solução por conta própria.
Participação da IA e minha participação
- IA: auxiliou na explicação da arquitetura do projeto, dos conceitos de Python, na interpretação dos testes e na identificação de possíveis causas dos defeitos.
- Minha participação: executei os comandos, analisei os arquivos, investiguei os erros, implementei correções e executei novamente os testes para verificar os resultados.
