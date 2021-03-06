---
title: Transformar dados com o databricks Notebook
description: Saiba como processar ou transformar dados executando um notebook do Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923051"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados executando um notebook do Databricks

A Atividade de Notebook do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um notebook do Databricks no workspace do Azure Databricks. Este documento baseia-se no artigo sobre  [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação permitidas. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade do Notebook Databricks

A seguir está a definição JSON de exemplo de uma Atividade de Notebook do Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriedades da atividade de Notebook do Databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|obrigatórios|
|---|---|---|
|Nome|Nome da atividade no pipeline.|SIM|
|Descrição|Texto que descreve o que a atividade faz.|Não|
|type|Para Atividade de Notebook do Databricks, o tipo da atividade é DatabricksNotebook.|SIM|
|linkedServiceName|Nome do serviço vinculado do Databricks no qual o notebook do Databricks executa. Para saber mais sobre esse serviço vinculado, consulte o artigo  [Serviços de computação vinculados](compute-linked-services.md) .|SIM|
|notebookPath|O caminho absoluto do notebook a ser executado no workspace do Databricks. Esse caminho deve começar com uma barra.|SIM|
|baseParameters|Uma matriz de pares chave-valor. Parâmetros básicos podem ser utilizados para cada execução de atividade. Se o notebook utilizar um parâmetro que não for especificado, será usado o valor padrão do notebook. Encontre mais informações sobre os parâmetros em [Notebooks do Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Pode ser uma matriz de \<cadeia de caracteres, object>.|Não|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do Databricks

Na definição da atividade do databricks acima, você especifica esses tipos de biblioteca: *jar*, *ovo*, *WHL*, *Maven*, *PyPI*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Para obter mais detalhes, consulte a [documentação do Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passando parâmetros entre blocos de anotações e Data Factory

Você pode passar data factory parâmetros para blocos de anotações usando a propriedade *baseparameters* na atividade do databricks. 

Em determinados casos, talvez seja necessário repassar determinados valores do bloco de anotações para data factory, que pode ser usado para o fluxo de controle (verificações condicionais) no data factory ou ser consumido pelas atividades downstream (o limite de tamanho é 2MB). 

1. No bloco de anotações, você pode chamar [dbutils. notebook. Exit ("ReturnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) e o "ReturnValue" correspondente será retornado para data Factory.

2. Você pode consumir a saída em data factory usando uma expressão como `'@activity('databricks notebook activity name').output.runOutput'`. 

   > [!IMPORTANT]
   > Se você estiver passando um objeto JSON, poderá recuperar valores acrescentando nomes de propriedade. Exemplo: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Usando o workspace do Databricks da interface do usuário](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do Databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copie a biblioteca usando a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
