---
title: Criar, executar e controlar pipelines do ML
titleSuffix: Azure Machine Learning
description: Crie e execute um pipeline de aprendizado de máquina com o SDK do Azure Machine Learning para Python. Use pipelines ML para criar e gerenciar os fluxos de trabalho que unem fases de ML (Machine Learning). Essas fases incluem preparação de dados, treinamento de modelo, implantação de modelo e inferência/Pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: b50fcea6f1a479ac295a3a6832d1ab828e01ea3d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986386"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Criar e executar pipelines do Machine Learning com o SDK do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar, publicar, executar e controlar um [pipeline de aprendizado de máquina](concept-ml-pipelines.md) usando o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Use **pipelines ml** para criar um fluxo de trabalho que junte-se a várias fases de ml e, em seguida, publique esse pipeline em seu espaço de Azure Machine Learning para acessar mais tarde ou compartilhar com outras pessoas.  Os pipelines ML são ideais para cenários de pontuação em lote, usando várias computações, reutilizando etapas em vez de executá-las novamente, bem como compartilhar fluxos de trabalho de AM com outras pessoas.

Embora você possa usar um tipo diferente de pipeline chamado de [pipeline do Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) para a automação de CI/CD de tarefas do ml, esse tipo de pipeline nunca é armazenado dentro de seu espaço de trabalho. [Compare esses pipelines diferentes](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Cada fase de um pipeline de ML, como preparação de dados e treinamento de modelo, pode incluir uma ou mais etapas.

Os pipelines de ML que você cria são visíveis para os membros do seu [espaço de trabalho](how-to-manage-workspace.md)Azure Machine Learning. 

Os pipelines ML usam destinos de computação remota para computação e o armazenamento dos dados intermediários e finais associados a esse pipeline. Eles podem ler e gravar dados de e para locais de [armazenamento do Azure](https://docs.microsoft.com/azure/storage/) com suporte.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [Workspace do Azure Machine Learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou use uma [instância de computação Azure Machine Learning (versão prévia)](concept-compute-instance.md) com o SDK já instalado.

> [!NOTE]
> As instâncias de computação estão disponíveis somente para espaços de trabalho com uma região do **norte EUA Central**, **leste dos eua 2**, **Europa setentrional** ou **sul do Reino Unido**, com suporte para outras regiões em breve.
>Se o seu espaço de trabalho estiver em qualquer outra região, você poderá continuar a criar e usar uma [VM do Notebook](concept-compute-instance.md#notebookvm) em vez disso. 

Comece anexando seu espaço de trabalho:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

Crie os recursos necessários para executar um pipeline de ML:

* Configurar um repositório de dados usado para acessar os dados necessários nas etapas de pipeline.

* Configure um objeto `DataReference` para apontar para dados que residem ou podem ser acessados em um repositório de dados.

* Configure os [destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) em que suas etapas de pipeline serão executadas.

### <a name="set-up-a-datastore"></a>Configurar um repositório de dados

Um repositório de dados armazena os dados para o pipeline acessar. Cada workspace tem um repositório de dados padrão. Você pode registrar armazenamentos de dados adicionais. 

Quando você cria seu espaço de trabalho, [os arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e o [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) são anexados ao espaço de trabalho. Um repositório de armazenamento padrão é registrado para se conectar ao armazenamento de BLOBs do Azure. Para saber mais, consulte [Decidindo quando usar Arquivos do Azure, Blobs do Azure ou Discos do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Carregar arquivos de dados ou diretórios para o repositório de dados para que eles possam ser acessados de seus pipelines. Este exemplo usa o armazenamento de BLOBs como o armazenamento de repositório:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Um pipeline consiste em uma ou mais etapas. Uma etapa é uma unidade executada em um destino de computação. As etapas podem consumir fontes de dados e produzir dados "intermediários". Uma etapa pode criar dados como um modelo, um diretório com o modelo e arquivos dependentes ou dados temporários. Esses dados então ficam disponíveis para outras etapas posteriores no pipeline.

Para saber mais sobre como conectar seu pipeline a seus dados, confira os artigos [como acessar dados](how-to-access-data.md) e [como registrar os DataSets](how-to-create-register-datasets.md). 

### <a name="configure-data-reference"></a>Configurar referência de dados

Você acabou de criar uma fonte de dados que pode ser referenciada em um pipeline como entrada para uma etapa. Uma fonte de dados em um pipeline é representada por um objeto [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference). O objeto `DataReference` aponta para dados que residem ou podem ser acessados de um repositório de dados.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Dados intermediários (ou a saída de uma etapa) são representados por um objeto [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` é produzido como a saída de uma etapa e usado como a entrada de um ou mais etapas futuras. O `PipelineData` introduz uma dependência de dados entre etapas e cria uma ordem de execução implícita no pipeline. Esse objeto será usado mais tarde ao criar etapas de pipeline.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Configurar dados usando DataSets

Se você tiver dados tabulares armazenados em um arquivo ou conjunto de arquivos, um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) será uma alternativa eficiente para um `DataReference`. os objetos do `TabularDataset` dão suporte ao controle de versão, diffs e estatísticas de resumo. os `TabularDataset`s são avaliados lentamente (como geradores de Python) e é eficiente agrupá-los por divisão ou filtragem. A classe `FileDataset` fornece dados semelhantes que são avaliados lentamente, representando um ou mais arquivos. 

Você cria um `TabularDataset` usando métodos como [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Você cria uma `FileDataset` usando [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Você pode aprender mais sobre como trabalhar com conjuntos de [registros de adicionar & registrar conjuntos](how-to-create-register-datasets.md) de informações ou [este bloco de anotações de exemplo](https://aka.ms/train-datasets).

## <a name="set-up-compute-target"></a>Configurar destino de computação

No Azure Machine Learning, o termo __computações__ (ou __destino de computação__) refere-se aos computadores ou clusters que executam as etapas computacionais no pipeline do Machine Learning.   Veja [destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md) para obter uma lista completa de destinos de computação e como criá-los e anexá-los ao seu workspace.  O processo para criar e/ou anexar um destino de computação é o mesmo, não importa se você está treinando um modelo ou executando uma etapa do pipeline. Depois de criar e anexar seu destino de computação, use o objeto `ComputeTarget` em na [etapa do pipeline](#steps).

> [!IMPORTANT]
> Não há suporte para operações de gerenciamento em destinos de computação de dentro de trabalhos remotos. Como os pipelines de aprendizado de máquina são enviados como um trabalho remoto, não use operações de gerenciamento em destinos de computação de dentro do pipeline.

Abaixo estão exemplos de como criar e anexar destinos de computação para:

* Computação do Azure Machine Learning
* Azure Databricks 
* Análise Azure Data Lake

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning

Você pode criar uma computação do Azure Machine Learning para executar suas etapas.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Banco de Dados do Azure

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie um workspace do Azure Databricks antes de usá-lo. Para criar um recurso de espaço de trabalho, consulte o documento [executar um trabalho do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Para anexar o Azure Databricks como um destino de computação, forneça as seguintes informações:

* __Nome de computação do databricks__: o nome que você deseja atribuir a esse recurso de computação.
* __Nome do espaço de trabalho do databricks__: o nome do espaço de trabalho Azure Databricks.
* __Token de acesso do databricks__: o token de acesso usado para autenticar para Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

O código a seguir demonstra como anexar Azure Databricks como um destino de computação com o SDK do Azure Machine Learning (__o espaço de trabalho do databricks precisa estar presente na mesma assinatura que o seu espaço de trabalho AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-databricks) no github.

### <a id="adla"></a>Azure Data Lake Analytics

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de usá-lo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome da computa__: o nome que você deseja atribuir a este recurso de computação.
* __Grupo de recursos__: o grupo de recursos que contém a conta de data Lake Analytics.
* __Nome da conta__: o nome da conta de data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-adla) no github.

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados que você precisa para trabalhar com está em um armazenamento não padrão, você pode usar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes do treinamento.

## <a id="steps"></a>Construir suas etapas de pipeline

Depois de criar e anexar um destino de computação ao workspace, você está pronto para definir uma etapa do pipeline. Há muitas etapas internas disponíveis por meio do SDK do Azure Machine Learning. O mais básico dessas etapas é um [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), que executa um script Python em um destino de computação especificado:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

A reutilização de resultados anteriores (`allow_reuse`) é fundamental ao usar pipelines em um ambiente de colaboração, pois eliminar a reversão desnecessária oferece agilidade. Reutilização é o comportamento padrão quando o script_name, as entradas e os parâmetros de uma etapa permanecem os mesmos. Quando a saída da etapa é reutilizada, o trabalho não é enviado para a computação, em vez disso, os resultados da execução anterior ficam imediatamente disponíveis para a execução da próxima etapa. Se `allow_reuse` for definido como false, uma nova execução sempre será gerada para essa etapa durante a execução do pipeline. 

Depois de definir suas etapas, você pode criar o pipeline usando algumas ou todas essas etapas.

> [!NOTE]
> Nenhum arquivo ou dado é carregado para Azure Machine Learning quando você define as etapas ou cria o pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

O exemplo a seguir usa o destino de computação do Azure Databricks criado anteriormente: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Usar um conjunto de uma 

Para usar um `TabularDataset` ou `FileDataset` em seu pipeline, você precisa transformá-lo em um objeto [DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) chamando [as_named_input (Name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Você passa esse `DatasetConsumptionConfig` objeto como um dos `inputs` para a etapa do pipeline. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Em seguida, você recupera o conjunto de os em seu pipeline usando o dicionário [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Para obter mais informações, consulte o [pacote Azure-pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) e a referência de [classe de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Enviar o pipeline

Quando você envia o pipeline, Azure Machine Learning verifica as dependências de cada etapa e carrega um instantâneo do diretório de origem especificado. Se nenhum diretório de origem for especificado, o diretório local atual será carregado. O instantâneo também é armazenado como parte do experimento em seu espaço de trabalho.

> [!IMPORTANT]
> Para impedir que arquivos sejam incluídos no instantâneo, crie um arquivo [. gitignore](https://git-scm.com/docs/gitignore) ou `.amlignore` no diretório e adicione os arquivos a ele. O arquivo de `.amlignore` usa a mesma sintaxe e padrões que o arquivo [. gitignore](https://git-scm.com/docs/gitignore) . Se ambos os arquivos existirem, o arquivo de `.amlignore` terá precedência.
>
> Para obter mais informações, veja [cópias de sombra](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando você executar um pipeline pela primeira vez, o Azure Machine Learning:

* Baixa o instantâneo do projeto para o destino de computação do armazenamento de Blobs associado ao workspace.
* Cria uma imagem do Docker correspondente a cada etapa no pipeline.
* Baixa a imagem do Docker para cada etapa do destino de computação do registro de contêiner.
* Monta o repositório de armazenamento se um objeto `DataReference` for especificado em uma etapa. Se não houver suporte para montagem, os dados serão copiados para o destino de computação.
* Executa a etapa no destino de computação especificado na definição da etapa. 
* Cria artefatos como logs, stdout e stderr, métricas e saída especificados pela etapa. Esses artefatos então são carregados e mantidos no armazenamento de dados padrão do usuário.

![Diagrama de execução de um experimento como um pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Para obter mais informações, consulte a referência de [classe experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Exibir resultados de um pipeline

Consulte a lista de todos os seus pipelines e seus detalhes de execução no estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Exiba seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **pipelines** para ver todas as execuções de pipeline.
 ![lista de pipelines do aprendizado de máquina](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecione um pipeline específico para ver os resultados da execução.

## <a name="git-tracking-and-integration"></a>Acompanhamento e integração de git

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, consulte [integração do git para Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publicar um pipeline

Você pode publicar um pipeline para executá-lo com entradas diferentes mais tarde. Para o ponto de extremidade REST de um pipeline já publicado aceitar parâmetros, você deve parametrizar o pipeline antes da publicação.

1. Para criar um parâmetro de pipeline, use um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor padrão.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione esse objeto `PipelineParameter` como um parâmetro a qualquer uma das etapas no pipeline da seguinte maneira:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este pipeline que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Execute um pipeline publicado

Todos os pipelines publicados têm um ponto de extremidade REST. Esse ponto de extremidade REST invoca a execução do pipeline de sistemas externos, como clientes não Python. Esse ponto de extremidade permite a "repetibilidade gerenciada" em cenários de retreinamento e pontuação de lote.

Para invocar a execução do pipeline anterior, você precisa de um token de cabeçalho de autenticação Azure Active Directory, conforme descrito em referência de [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ou obter mais detalhes na [autenticação no Azure Machine Learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto de extremidade de pipeline com versão
Você pode criar um ponto de extremidade de pipeline com vários pipelines publicados por trás dele. Isso pode ser usado como um pipeline publicado, mas fornece um ponto de extremidade REST fixo conforme você itera e atualiza seus pipelines de ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Enviar um trabalho para um ponto de extremidade de pipeline
Você pode enviar um trabalho para a versão padrão de um ponto de extremidade de pipeline:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Você também pode enviar um trabalho para uma versão específica:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser feito usando a API REST:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Usar pipelines publicados no estúdio

Você também pode executar um pipeline publicado no estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Exiba seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **pontos de extremidade**.

1. Na parte superior, selecione **pontos de extremidade do pipeline**.
 ![lista de pipelines publicados do Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um pipeline específico para executar, consumir ou examinar os resultados das execuções anteriores do ponto de extremidade do pipeline.


### <a name="disable-a-published-pipeline"></a>Desabilitar um pipeline publicado

Para ocultar um pipeline da lista de pipelines publicados, você o desabilita, no estúdio ou no SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Você pode habilitá-lo novamente com `p.enable()`. Para obter mais informações, consulte referência de [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .


## <a name="caching--reuse"></a>Cache & reutilização  

Para otimizar e personalizar o comportamento de seus pipelines, você pode fazer algumas coisas em cache e reutilização. Por exemplo, você pode optar por:
+ **Desative a reutilização padrão da saída de execução de etapa** definindo `allow_reuse=False` durante a [definição da etapa](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). A reutilização é a chave ao usar pipelines em um ambiente de colaboração, já que a eliminação de execuções desnecessárias oferece agilidade. No entanto, você pode recusar a reutilização.
+ **Forçar a regeneração de saída para todas as etapas em uma execução** com `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Por padrão, `allow_reuse` para etapas está habilitada e o `source_directory` especificado na definição da etapa é com hash. Portanto, se o script de uma determinada etapa permanecer o mesmo (`script_name`, entradas e os parâmetros) e nada mais no` source_directory` tiver sido alterado, a saída de uma etapa anterior será reutilizada, o trabalho não será enviado para a computação e os resultados da execução anterior estarão imediatamente disponíveis para a próxima etapa.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Próximos passos

- Use [esses Jupyter Notebooks no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais pipelines de machine learning.
- Consulte a ajuda de referência do SDK para o pacote [azureml-pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Consulte a [instruções](how-to-debug-pipelines.md) para obter dicas sobre como depurar e solucionar problemas de pipelines.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
