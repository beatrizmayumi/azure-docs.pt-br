---
title: Transformação de junção no fluxo de dados de mapeamento
description: Combinar dados de duas fontes de dados usando a transformação de junção no fluxo de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 10149c6eb06e6d2994233aa365f237e6d9330c48
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644742"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Transformação de junção no fluxo de dados de mapeamento

Use a transformação junção para combinar dados de duas fontes ou fluxos em um fluxo de dados de mapeamento. O fluxo de saída incluirá todas as colunas de ambas as fontes correspondentes com base em uma condição de junção. 

## <a name="join-types"></a>Tipos de junção

O mapeamento de fluxos de dados atualmente dá suporte a cinco tipos de junção diferentes.

### <a name="inner-join"></a>Junção interna

A junção interna só gera linhas que têm valores correspondentes em ambas as tabelas.

### <a name="left-outer"></a>Externa esquerda

A junção externa esquerda retorna todas as linhas do fluxo à esquerda e os registros correspondentes do fluxo à direita. Se uma linha do fluxo à esquerda não tiver correspondência, as colunas de saída do fluxo à direita serão definidas como NULL. A saída será as linhas retornadas por uma junção interna mais as linhas não correspondentes do fluxo à esquerda.

> [!NOTE]
> O mecanismo do Spark usado pelos fluxos de dados ocasionalmente poderá ser possível nos produtos cartesianos em suas condições de junção. Quando esse for o caso, você poderá alternar para uma junção cruzada personalizada e inserir manualmente sua condição de junção. Isso pode resultar em um desempenho mais lento em seus fluxos de dados, pois o mecanismo de execução pode precisar calcular todas as linhas de ambos os lados da relação e, em seguida, filtrar as linhas.

### <a name="right-outer"></a>Externa direita

A junção externa direita retorna todas as linhas do fluxo à direita e os registros correspondentes do fluxo à esquerda. Se uma linha do fluxo à direita não tiver correspondência, as colunas de saída do fluxo à esquerda serão definidas como NULL. A saída será as linhas retornadas por uma junção interna mais as linhas não correspondentes do fluxo à direita.

### <a name="full-outer"></a>Externa completa

A junção externa completa gera todas as colunas e linhas de ambos os lados com valores nulos para colunas que não são correspondentes.

### <a name="custom-cross-join"></a>Junção cruzada personalizada

A junção cruzada gera o produto cruzado dos dois fluxos com base em uma condição. Se você estiver usando uma condição que não seja de igualdade, especifique uma expressão personalizada como condição de junção cruzada. O fluxo de saída será todas as linhas que atendem à condição de junção.

Você pode usar esse tipo de junção para junções não-correlacionadas e condições de ```OR```.

Se você quiser produzir explicitamente um produto cartesiano completo, use a transformação coluna derivada em cada um dos dois fluxos independentes antes da junção para criar uma chave sintética na qual corresponder. Por exemplo, crie uma nova coluna na coluna derivada em cada fluxo chamado ```SyntheticKey``` e defina-a como ```1```. Em seguida, use ```a.SyntheticKey == b.SyntheticKey``` como sua expressão de junção personalizada.

> [!NOTE]
> Certifique-se de incluir pelo menos uma coluna de cada lado da relação esquerda e direita em uma junção cruzada personalizada. A execução de Junções cruzadas com valores estáticos em vez de colunas de cada lado resulta em verificações completas de todo o conjunto de dados, fazendo com que o fluxo do seu data seja executado inadequadamente.

## <a name="configuration"></a>Configuração

1. Escolha o fluxo de dados com o qual você está ingressando no menu suspenso de **fluxo à direita** .
1. Selecione seu **tipo de junção**
1. Escolha em quais colunas de chave você deseja fazer a correspondência para a condição de junção. Por padrão, o fluxo de dados procura igualdade entre uma coluna em cada fluxo. Para comparar por meio de um valor calculado, passe o mouse sobre a lista suspensa coluna e selecione **coluna computada**.

![Transformação de junção](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Otimizando o desempenho da junção

Ao contrário da junção de mesclagem em ferramentas como o SSIS, a transformação junção não é uma operação de junção de mesclagem obrigatória. As chaves de junção não exigem classificação. A operação de junção ocorre com base na operação de junção ideal no Spark, difusão ou junção no lado do mapa.

![Transformação de junção otimizar](media/data-flow/joinoptimize.png "Otimização de junção")

Se um ou ambos os fluxos de dados couberem na memória do nó de trabalho, otimize ainda mais seu desempenho habilitando a **difusão** na guia otimizar. Você também pode reparticionar seus dados na operação de junção para que eles se adaptem melhor à memória por trabalhador.

## <a name="self-join"></a>Autojunção

Para unir automaticamente um fluxo de dados com ele mesmo, faça um alias de um fluxo existente com uma transformação selecionar. Crie uma nova ramificação clicando no ícone de adição ao lado de uma transformação e selecionando **nova ramificação**. Adicione uma transformação selecionar para alias do fluxo original. Adicione uma transformação de junção e escolha o fluxo original como o **fluxo à esquerda** e a transformação selecionar como o **fluxo à direita**.

![Auto-associação](media/data-flow/selfjoin.png "Auto-associação")

## <a name="testing-join-conditions"></a>Testando condições de junção

Ao testar as transformações de junção com a visualização de dados no modo de depuração, use um pequeno conjunto de dados conhecidos. Ao fazer amostragem de linhas de um grande conjunto de grandes, você não pode prever quais linhas e chaves serão lidas para teste. O resultado é não determinístico, o que significa que suas condições de junção podem não retornar nenhuma correspondência.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemplo de junção interna

O exemplo abaixo é uma transformação de junção chamada `JoinMatchedData` que usa fluxo à esquerda `TripData` e `TripFare`de fluxo à direita.  A condição de junção é a expressão `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` que retorna true se as colunas `hack_license`, `medallion`, `vendor_id`e `pickup_datetime` em cada fluxo correspondem. O parâmetro `joinType` é `'inner'`. Estamos habilitando a difusão somente no fluxo à esquerda para que `broadcast` tenha o valor `'left'`.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Exemplo de junção](media/data-flow/join-script1.png "Exemplo de junção")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Exemplo de junção cruzada personalizada

O exemplo abaixo é uma transformação de junção chamada `JoiningColumns` que usa fluxo à esquerda `LeftStream` e `RightStream`de fluxo à direita. Essa transformação usa dois fluxos e une todas as linhas nas quais a coluna `leftstreamcolumn` é maior que a coluna `rightstreamcolumn`. O parâmetro `joinType` é `cross`. A difusão não está habilitada `broadcast` tem valor `'none'`.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Exemplo de junção](media/data-flow/join-script2.png "Exemplo de junção")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Próximos passos

Depois de unir os dados, crie uma [coluna derivada](data-flow-derived-column.md) e [coletar](data-flow-sink.md) seus dados para um armazenamento de dados de destino.
