# Estágio em Engenharia de Dados - Teste Técnico

A TERA Capital é um MFO (Multi Family Office) e está recrutando um estagiário para trabalhar com Engenharia de Dados no time de Investimentos. O foco da vaga é ajudar no processo de extração e consolidação de dados externos e internos, a fim de gerar insights e empoderar o processo de alocação de recursos.

## Contextualizando o problema

Em 2010, um cliente resolveu montar uma carteira de investimentos em renda fixa, comprando títulos pré-fixados (ou seja, que rendem uma taxa fixa, anualmente) de dívida emitidos por diversos países, conforme tabela abaixo:

<table>
    <thead>
        <th>País</th>
        <th>Retorno</th>
        <th>Investimento</th>
        <th>Moeda</th>
    </thead>
    <tbody>
        <tr>
            <td>Brasil</td>
            <td>13% a.a.</td>
            <td>R$ 15.734.123,72</td>
            <td>Real</td>
        </tr>
        <tr>
            <td>Reino Unido</td>
            <td>3% a.a.</td>
            <td>£ 2.457.738,51</td>
            <td>Libra esterlina</td>
        </tr>
        <tr>
            <td>Alemanha</td>
            <td>3,5% a.a.</td>
            <td>€ 3.678.905,67</td>
            <td>Euro</td>
        </tr>
        <tr>
            <td>EUA</td>
            <td>2,38% a.a.</td>
            <td>US$ 356.789,02</td>
            <td>Dólar americano</td>
        </tr>
        <tr>
            <td>Japão</td>
            <td>0,05% a.a.</td>
            <td>¥ 5.402.346,70</td>
            <td>Iene japonês</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>15,45% a.a.</td>
            <td>$125.402.346,70</td>
            <td>Peso Argentino</td>
        </tr>
    </tbody>
<table>

Essas eram as posições em 1 de janeiro de 2010. O investimento foi feito por contas bancárias nos países de emissão de cada título. Sendo assim, o cliente sempre viu a cotação em sua respectiva moeda, o que, considerando a flutuação cambial, não dava noção exata de quais ativos tiveram melhor performance e qual era o desempenho geral da carteira.

Seu desafio, portanto, é criar um script que forneça o desempenho dessa carteira em real e dólar.

## Instruções

### Câmbio

O BACEN (Banco Central do Brasil) mantém uma cotação histórica oficial de moedas, denominado PTAX. Ela é disponibilizada através de um web service, que pode ser consultado pela API abaixo:

`https://ptax.bcb.gov.br/ptax_internet/consultaBoletim.do?method=gerarCSVFechamentoMoedaNoPeriodo&ChkMoeda=código&DATAINI=DD/MM/YYYY&DATAFIM=DD/MM/YYYY`

Os códigos para as moedas da carteira são: 

* Euro: `222` 
* Iene: `101`
* Dólar: `61`
* Libra: `115`
* Peso argentino: `706`

Onde consta `DD/MM/YYYY` refere-se ao formato de data que deve ser utilizado nos parâmetros.

O retorno da requisição é um CSV com algumas colunas, das quais serão utilizadas a Coluna 0 (data de cotação), Coluna 4 (taxa de compra) e Coluna 6 (paridade de compra). Elas permitem fazer a conversão das moedas através das fórmulas abaixo:

$$
\text{Montante em Dólar} = \frac{\text{Montante na Moeda Original}}{\text{Paridade de Compra}}
$$

$$
\text{Montante em Real} = \text{Montante na Moeda Original} \times \text{Taxa de Compra}
$$


### Retorno

Um título pré-fixado é um investimento em que se sabe de antemão qual será o retorno. No caso do portfólio apresentado, o montante investido rende, anualmente, a taxa informada na coluna "Retorno". Por exemplo, se você investiu R$ 100,00 em um ativo que rende 4% a.a., após um ano de investimento, seu montante será de R$ 104,00. Porém, no segundo ano, o rendimento dele será de R$ 108,16, já que os juros aqui são compostos. A taxa de 4% no segundo ano é aplicada sobre os R$ 104,00 e não mais sobre o investimento inicial de R$ 100,00.

O retorno total do ativo, ao longo dos anos, pode ser demonstrado pela fórmula dos Juros Compostos:

$$
M = C \cdot (1 + i)^{n}
$$


**Onde:**
- \(M\): valor final  
- \(C\): capital investido  
- \(i\): taxa em base decimal (ex.: 4% = 0,04)  
- \(n\): número de períodos (anos)

### Script

Você deve criar um script Python no qual um ano deve ser informado na linha de comando (`python analise.py 2025`) para que seja criado um relatório em Excel com o desempenho da carteira, no seguinte formato:

<table>
    <thead>
        <th>Título</th>
        <th>Retorno em Real (%)</th>
        <th>Retorno em Dólar (%)</th>
        <th>Retorno em Real (R$)</th>
        <th>Retorno em Dólar (US$)</th>
    <thead>
    <tbody>
        <tr>
            <td>Alemanha</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
        </tr>
        <tr>
            <td>Argentina</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
        </tr>
        <tr>
            <td>Brasil</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
        </tr>
        <tr>
            <td>EUA</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
        </tr>
        <tr>
            <td>Reino Unido</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
            <td>x</td>
        </tr>
    </tbody>
</table>

O retorno do ativo deve ser calculado considerando a diferença entre o valor dele no ano inserido na linha de comando e o valor dele no ano posterior.

Portanto, para calcular as métricas do relatório, siga o passo-a-passo:

1. Observe a taxa de retorno de cada ativo informado na tabela e aplique a fórmula dos juros compostos para descobrir quanto ele vale no ano desejado. Lembre-se que n é a contagem de anos desde 2010.

2. Repita o mesmo processo para descobrir quanto ele deveria valer no ano subsequente ao informado.

3. Consulte o câmbio de cada moeda para o primeiro dia útil do ano informado e o primeiro dia útil do ano posterior. Agora faça a conversão para dólar e real de cada valor

4. Agora, basta fazer a subtração entre o valor do ano desejado e o valor do ano posterior para obter os retornos.

### Bônus
* Acrescente uma linha no relatório com o desempenho da carteira inteira;
* Crie um parâmetro extra no script para que o usuário possa informar em qual formato ele deseja gerar o relatório (CSV, Excel ou Parquet);

### Instruções

* Faça a resolução do teste em um repositório público do GitHub.
* Mantenha a organização dos commits e crie um README.md explicando o que foi feito e como executar o script.
* A organização do código e linha de raciocínio fazem parte da avaliação.
* Utilize o pandas para armazenar o câmbio, fazer os cálculos e gerar o Excel final.
* Sinta-se à vontade para fazer perguntas sobre finanças.

Boa sorte!