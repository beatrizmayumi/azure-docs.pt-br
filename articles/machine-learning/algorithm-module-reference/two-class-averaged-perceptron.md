---
title: 'Regressão de floresta de decisão: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo perceptron médio de duas classes no Azure Machine Learning para criar um modelo de aprendizado de máquina com base no algoritmo de média de perceptron.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 62e2168c674693ded31bd94206e2e13c82c67b68
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546377"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo perceptron médio de duas classes

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo médio de perceptron.  
  
Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um conjunto de informações *marcado*, que inclui uma coluna de rótulo. Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar o modelo](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre os modelos de média de perceptron

O *Método Average perceptron* é uma versão inicial e simples de uma rede neural. Nessa abordagem, as entradas são classificadas em várias saídas possíveis com base em uma função linear e, em seguida, combinadas com um conjunto de pesos que são derivados do vetor de recurso — por isso o nome "perceptron".

Os modelos perceptron mais simples são adequados ao aprendizado de padrões separáveis linearmente, enquanto as redes neurais (especialmente redes neurais profundas) podem modelar limites de classe mais complexos. No entanto, os perceptrons são mais rápidos e conforme eles processem casos em série, podem ser usados com treinamento contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar o perceptron médio de duas classes

1.  Adicione o módulo **perceptron médio de duas classes** ao seu pipeline.  

2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, forneça um conjunto específico de valores como argumentos.
  
3.  Para **taxa de aprendizagem**, especifique um valor para a *taxa de aprendizado*. Os valores de taxa de aprendizagem controlam o tamanho da etapa usada em estocástico Grad descendente cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa menor, você testa o modelo com mais frequência, com o risco que você pode ficar preso em um limite local. Aumentando a etapa, você pode convergir mais rapidamente, com o risco mínimo de errar o alvo verdadeiro.
  
4.  Para **número máximo de iterações**, digite o número de vezes que você deseja que o algoritmo examine os dados de treinamento.  
  
     Parando no início geralmente fornece melhor generalização. Aumentar o número de iterações melhora o ajuste, com o risco de superajuste.
  
5.  Para **semente de número aleatório**, opcionalmente, digite um valor inteiro para usar como a semente. Usar uma semente é recomendado se você quiser garantir reprodução do pipeline entre execuções.  
  
1.  Conecte um conjunto de e um dos módulos de treinamento:
  
    -   Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](train-model.md) .




## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 