# Estágio em Engenharia de Dados - Teste Técnico

A TERA Capital é um MFO (Multi Family Office) e está recrutando um estagiário para trabalhar com Engenharia de Dados no time de Investimentos. O foco da vaga é ajudar no processo de extração e consolidação de dados externos e internos, a fim de gerar insights e empoderar o processo de alocação de recursos.

## Contextualizando o problema

Em 1 de janeiro de 2010, um cliente aplicou um valor único em títulos pré-fixados (ou seja, que rendem uma taxa fixa ao ano) emitidos por diferentes países.  

O valor investido e a taxa de retorno de cada título estão na tabela abaixo.

<b>Importante:</b> esses títulos não receberam novos aportes depois de 2010. Cada posição segue crescendo ano a ano apenas pela taxa fixa indicada.

| País        | Retorno     | Investimento        | Moeda            |
|-------------|-------------|---------------------|------------------|
| Brasil      | 13% a.a.    | R$ 15.734.123,72    | Real             |
| Reino Unido | 3% a.a.     | £ 2.457.738,51      | Libra esterlina  |
| Alemanha    | 3,5% a.a.   | € 3.678.905,67      | Euro             |
| EUA         | 2,38% a.a.  | US$ 356.789,02      | Dólar americano  |
| Japão       | 0,05% a.a.  | ¥ 5.402.346,70      | Iene japonês     |
| Argentina   | 15,45% a.a. | $ 125.402.346,70    | Peso argentino   |

Essas eram as posições em 1 de janeiro de 2010.

Como o investimento foi feito por contas bancárias nos países de emissão de cada título, o cliente sempre visualizou os saldos em suas respectivas moedas. Isso, considerando a flutuação cambial, não dava noção exata de quais ativos tiveram melhor performance e qual era o desempenho geral da carteira.

**Objetivo:** Dado um ano, calcular quanto cada título da carteira rendeu em real (BRL) e em dólar (USD) naquele ano, considerando juros compostos e a conversão cambial.

---

## Câmbio

O BACEN (Banco Central do Brasil) mantém uma cotação histórica oficial de moedas, denominada PTAX. Ela é disponibilizada através de um web service, que pode ser consultado pela API:

```
https://ptax.bcb.gov.br/ptax_internet/consultaBoletim.do?method=gerarCSVFechamentoMoedaNoPeriodo&ChkMoeda=código&DATAINI=DD/MM/YYYY&DATAFIM=DD/MM/YYYY
```

Os códigos para as moedas da carteira são:

- Euro: `222`
- Iene: `101`
- Dólar: `61`
- Libra: `115`
- Peso argentino: `706`

### Como interpretar as colunas do CSV

O retorno da requisição é um CSV com várias colunas. Para este teste, use apenas:

- **Coluna 0 - Data**: data do câmbio

- **Coluna 4 – Taxa de compra**: quantos reais você precisa para comprar 1 unidade da moeda estrangeira.  
  Exemplo: se a taxa de compra do dólar for 4,00 → 1 US$ = R$ 4,00.  

- **Coluna 6 – Paridade de compra**: quantas unidades da moeda estrangeira equivalem a 1 dólar americano.  
  Exemplo: se a paridade do euro for 0,90 → 1 US$ = € 0,90.  

Resumo:  
- Para converter qualquer moeda em real (BRL), use a Taxa de compra.  
- Para converter qualquer moeda em dólar (USD), use a Paridade de compra.  

As fórmulas são:

$\text{Montante em Dólar} = \frac{\text{Montante na Moeda Original}}{\text{Paridade de Compra}}$

$\text{Montante em Real} = \text{Montante na Moeda Original} \times \text{Taxa de Compra}$
---

## Retorno (juros compostos)

Um título pré-fixado é um investimento em que se sabe de antemão qual será o retorno. O retorno total ao longo dos anos pode ser calculado pela fórmula dos Juros Compostos:

$$
M = C \cdot (1 + i)^{n}
$$

**Onde:**
- \(M\): valor final  
- \(C\): capital investido  
- \(i\): taxa em base decimal (ex.: 4% = 0,04)  
- \(n\): número de períodos (anos desde 2010)

### Exemplo prático
Um título de R$ 100,00 a 10% a.a. terá:

- Valor em 2015: $$100 \times (1+0,10)^5 = 161,05$$
- Valor em 2016: $$100 \times (1+0,10)^6 = 177,16$$
- Retorno de 2015 = diferença entre os dois anos = $$177,16 - 161,05 = 16,11$$.  

Esse valor deve então ser convertido para real e para dólar usando as cotações da PTAX.

---

## Script

Você deve criar um script Python no qual um ano seja informado na linha de comando (`python analise.py 2025`) para que seja criado um relatório em Excel com o desempenho da carteira, no seguinte formato:

| Título      | Retorno em Real (%) | Retorno em Dólar (%) | Retorno em Real (R$) | Retorno em Dólar (US$) |
|-------------|----------------------|-----------------------|-----------------------|-------------------------|
| Alemanha    | x                    | x                     | x                     | x                       |
| Argentina   | x                    | x                     | x                     | x                       |
| Brasil      | x                    | x                     | x                     | x                       |
| EUA         | x                    | x                     | x                     | x                       |
| Japão       | x                    | x                     | x                     | x                       |
| Reino Unido | x                    | x                     | x                     | x                       |

Passo a passo para o cálculo:

1. Use a taxa de retorno de cada ativo e aplique a fórmula dos juros compostos para descobrir quanto ele vale no ano desejado.  
2. Repita o mesmo processo para o ano subsequente.  
3. Consulte o câmbio para o primeiro dia útil de cada um desses anos.  
4. Converta os montantes em real e dólar.  
5. Calcule o retorno como a diferença entre os dois anos.  

---

## Bônus
- Acrescente uma linha no relatório com o desempenho da carteira inteira.  
- Crie um parâmetro extra no script para que o usuário possa escolher o formato do relatório (CSV, Excel ou Parquet).  

---

## Instruções
- Faça a resolução do teste em um repositório público do GitHub.  
- Mantenha a organização dos commits e crie um README.md explicando o que foi feito e como executar o script.  
- A organização do código e o raciocínio fazem parte da avaliação.  
- Utilize pandas para armazenar o câmbio, fazer os cálculos e gerar o arquivo final.  
- Sinta-se à vontade para fazer perguntas sobre finanças.  

Boa sorte!
