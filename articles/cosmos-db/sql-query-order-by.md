---
title: Cláusula ORDER BY no Azure Cosmos DB
description: Saiba mais sobre a cláusula ORDER BY do SQL para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: fc5c875f4ae54ed334318efc5a1d5610b89bdda5
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929595"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Cláusula ORDER BY no Azure Cosmos DB

A cláusula ORDER BY opcional especifica a ordem de classificação dos resultados retornados pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um nome ou alias de propriedade.  
  
   Várias propriedades podem ser especificadas. Os nomes de propriedade devem ser exclusivos. A sequência das propriedades de classificação na cláusula ORDER BY define a organização do conjunto de resultados classificado. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
   Os nomes de propriedade referenciados na cláusula ORDER BY devem corresponder a uma propriedade na lista de seleção ou a uma propriedade definida na coleção especificada na cláusula FROM sem nenhuma ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica uma ou mais propriedades ou expressões nas quais classificar o conjunto de resultados da consulta.  
  
- `<scalar_expression>`  
  
   Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  
- `ASC | DESC`  
  
   Define que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. ASC classifica do valor mais baixo para o valor mais alto. DESC classifica do valor mais alto para o valor mais baixo. ASC é a ordem de classificação padrão. Valores nulos são tratados como os menores valores possíveis.  
  
## <a name="remarks"></a>Comentários  
  
   A cláusula ORDER BY requer que a política de indexação inclua um índice para os campos que estão sendo classificados. O tempo de execução de Azure Cosmos DB consulta dá suporte à classificação em relação a um nome de propriedade e não a Propriedades computadas. Azure Cosmos DB dá suporte a várias propriedades ORDER BY. Para executar uma consulta com várias propriedades ORDER BY, você deve definir um [índice composto](index-policy.md#composite-indexes) nos campos que estão sendo classificados.
   
> [!Note] 
> Se as propriedades que estão sendo classificadas puderem ser indefinidas para alguns documentos e você quiser recuperá-las em uma consulta ORDER BY, você deverá criar explicitamente um índice nessas propriedades. A política de indexação padrão não permitirá a recuperação dos documentos em que a Propriedade Sort é indefinida.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que recupera famílias em ordem crescente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A consulta a seguir recupera a família `id`s em ordem de data de criação do item. O item `creationDate` é um número que representa o *tempo de época*ou tempo decorrido desde Jan. 1, 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Além disso, você pode ordenar por várias propriedades. Uma consulta que ordena por várias propriedades requer um [índice composto](index-policy.md#composite-indexes). Considere a consulta a seguir.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Essa consulta recupera a família `id` em ordem crescente do nome da cidade. Se vários itens tiverem o mesmo nome de cidade, a consulta será ordenada pelo `creationDate` em ordem decrescente.

## <a name="next-steps"></a>Próximos passos

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula de limite de deslocamento](sql-query-offset-limit.md)
