---
title: 'Início Rápido: Traduzir fala em texto, Java (Windows, Linux) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: f7efecc88fe3c4400732d18a2eea39701269d89c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468409"
---
## <a name="prerequisites"></a>Prerequisites

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   ```Java
   package quickstart;

   import java.io.IOException;
   import java.util.concurrent.Future;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and service region (e.g., "westus").

           int exitCode = 1;
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(("YourSubscriptionKey",  "YourServiceRegion");
           assert(config != null);

           // Sets source and target languages.
           String fromLanguage = "en-US";
           String toLanguage = "de";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage(toLanguage);

           // Creates a translation recognizer using the default microphone audio input device.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           assert(recognizer != null);

           System.out.println("Say something...");

           // Starts translation, and returns after a single utterance is recognized. The end of a
           // single utterance is determined by listening for silence at the end or until a maximum of 15
           // seconds of audio is processed. The task returns the recognized text as well as the translation.
           // Note: Since recognizeOnceAsync() returns only a single utterance, it is suitable only for single
           // shot recognition like command or query.
           // For long-running multi-utterance recognition, use startContinuousRecognitionAsync() instead.
           Future<TranslationRecognitionResult> task = recognizer.recognizeOnceAsync();
           assert(task != null);

           TranslationRecognitionResult result = task.get();
           assert(result != null);

           if (result.getReason() == ResultReason.TranslatedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText());
               System.out.println("TRANSLATED into '" + toLanguage + "': " + result.getTranslations().get(toLanguage));
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.RecognizedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText() + "(text could not be translated)");
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.NoMatch) {
               System.out.println("NOMATCH: Speech could not be recognized.");
           }
           else if (result.getReason() == ResultReason.Canceled) {
               CancellationDetails cancellation = CancellationDetails.fromResult(result);
               System.out.println("CANCELED: Reason=" + cancellation.getReason());

               if (cancellation.getReason() == CancellationReason.Error) {
                   System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                   System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                   System.out.println("CANCELED: Did you update the subscription info?");
               }
           }

           recognizer.close();

           System.exit(exitCode);
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.

1. Fale uma frase ou expressão em inglês. O aplicativo transmite sua fala para os serviço de Fala, que a traduz e transcreve em texto (neste caso, para alemão). Em seguida, o serviço de Fala enviam o texto de volta para o aplicativo para exibição.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
