---
title: Ler dados capturados dos hubs de eventos do Azure do aplicativo Python | Microsoft Docs
description: Este artigo mostra como escrever código Python para capturar dados que são enviados para um hub de eventos e ler os dados de evento capturados de um armazenamento do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293531"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Capturar dados de hubs de eventos no armazenamento do Azure e lê-los usando o Python 
Você pode usar configurar um hub de eventos para que os dados enviados a um hub de eventos sejam capturados em um armazenamento do Azure ou Azure Data Lake Storage. Este artigo mostra como usar o código do Python de gravação para enviar eventos para um hub de eventos e ler os dados capturados de um armazenamento de BLOBs do Azure. Para obter mais informações sobre esse recurso, consulte [visão geral do recurso de captura de hubs de eventos](event-hubs-capture-overview.md).

Este exemplo usa o [SDK para Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso de Captura. O programa sender.py envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O hub de eventos está configurado para usar o recurso de Captura a fim de gravar esses dados no armazenamento de Blobs em lotes. O aplicativo capturereader.py lê esses blobs e cria um arquivo de acréscimo por dispositivo. Em seguida, o aplicativo grava os dados em arquivos .csv.

> [!IMPORTANT]
> Este guia de início rápido usa a versão 5 do SDK do Python de hubs de eventos do Azure. Para um início rápido que usa a versão 1 antiga do SDK do Python, consulte [Este artigo](event-hubs-capture-python.md). Se você estiver usando a versão 1 do SDK, recomendamos que migre seu código para a versão mais recente. Para obter detalhes, consulte o [Guia de migração](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

Neste início rápido, você: 

> [!div class="checklist"]
> * Criar uma conta de armazenamento de Blobs do Azure e um contêiner no portal do Azure.
> * Crie um namespace de hubs de eventos usando o portal do Azure.
> * Crie um hub de eventos com o recurso de captura habilitado e conecte-o à sua conta de armazenamento.
> * Enviar dados para o hub de eventos usando um script Python.
> * Ler e processar arquivos da Captura dos Hubs de Eventos usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2,7 e 3,5 ou posterior, com `pip` instalado e atualizado.
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- [Crie um namespace de hubs de eventos e um hub de eventos no namespace](event-hubs-create.md). Anote o nome do namespace de hubs de eventos, o nome do hub de eventos e a chave de acesso primária para o namespace. Obtenha a chave de acesso seguindo as instruções do artigo: [obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave padrão é: **RootManageSharedAccessKey**. Você não precisa da cadeia de conexão para o tutorial. Você só precisa da chave primária. 
- Siga estas etapas para criar uma **conta de armazenamento do Azure** e um **contêiner de blob**:
    1. [Crie uma conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Crie um contêiner de blob no armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        Anote a **cadeia de conexão** e o **nome do contêiner**. Você os usará posteriormente no código. 
- Habilite o recurso de **captura** para o Hub de eventos seguindo as instruções de: [habilitar a captura de hubs de eventos usando o portal do Azure](event-hubs-capture-enable-through-portal.md). Selecione a conta de armazenamento e o contêiner de BLOB que você criou na etapa anterior. Você também pode habilitar o recurso ao criar um hub de eventos. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script Python para enviar eventos a seu Hub de Eventos
Nesta seção, você cria um script Python que envia eventos 200 (10 dispositivos * 20 eventos) para um hub de eventos. Esses eventos são a leitura ambiental de exemplo enviada no formato JSON. 

1. Abra seu editor favorito do Python, como o [Visual Studio Code][Visual Studio Code].
2. Crie um script chamado **sender.py**. 
3. Cole o código a seguir em sender.py. Consulte os comentários de código para obter detalhes. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Substitua os seguintes valores nos scripts:
    1. Substitua `EVENT HUBS NAMESPACE CONNECTION STRING` pela cadeia de conexão para seu namespace de hubs de eventos.
    2. Substitua `EVENT HUB NAME` pelo nome do hub de eventos. 
5. Execute o script para enviar eventos para o Hub de eventos. 
6. No portal do Azure, você pode verificar se o Hub de eventos recebeu as mensagens. Alterne para a exibição **mensagens** na seção **métricas** . Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ele mostre que as mensagens foram recebidas. 

    [![verificar se o Hub de eventos recebeu as mensagens](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Criar um script Python para ler os arquivos da Captura
Neste exemplo, os dados capturados são armazenados no armazenamento de BLOBs do Azure. O script nesta seção lê os arquivos de dados de captura do seu armazenamento do Azure e gera arquivos CSV para que você possa abrir e exibir o conteúdo com facilidade. Você verá 10 arquivos no diretório de trabalho atual do aplicativo. Esses arquivos conterão as leituras ambientais dos 10 dispositivos. 

1. No seu editor de Python, crie um script chamado **capturereader.py**. Esse script lê os arquivos capturados e cria um arquivo por dispositivo para gravar os dados somente para esse dispositivo.
2. Cole o código a seguir em capturereader.py. Consulte os comentários de código para obter detalhes. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Substitua `<AZURE STORAGE CONNECTION STRING>` pela cadeia de conexão para sua conta de armazenamento do Azure. O nome do contêiner criado neste tutorial é: **Capture**. Se você usou um nome diferente para o contêiner, substitua `capture` pelo nome do contêiner na conta de armazenamento. 

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra um prompt de comando com o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Altere o diretório para o qual você salvou sender.py e capturereader.py e execute este comando:
   
   ```
   python sender.py
   ```
   
   Esse comando inicia um novo processo de Python para executar o remetente.
3. Aguarde alguns minutos para que a captura seja executada. Em seguida, digite o seguinte comando na janela de comando original:
   
   ```
   python capturereader.py
   ```

   Esse processador de captura usa o diretório local para baixar todos os blobs do contêiner/da conta de armazenamento. Ele processa todos os que não estão vazios e grava os resultados como arquivos .csv no diretório local.

## <a name="next-steps"></a>Próximos passos
Confira exemplos de Python no GitHub [aqui](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
