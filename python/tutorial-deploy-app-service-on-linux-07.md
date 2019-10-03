---
title: Limpar recursos após implantar no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code
description: Etapa 7 do tutorial, limpando os recursos do Azure
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: a86207477deb4652ef1d98c1130329bc3e0c91fc
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019604"
---
# <a name="clean-up-resources"></a>Limpar recursos

[Etapa anterior: stream de logs](tutorial-deploy-app-service-on-linux-06.md)

O Serviço de Aplicativo que você criou inclui um plano do Serviço de Aplicativo de backup que pode incorrer em custos. Para limpar os recursos, clique com o botão direito do mouse no Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Excluir**.

Você também pode visitar o [portal do Azure](https://portal.azure.com), selecionar **Grupos de recursos** no painel de navegação esquerdo, selecionar o grupo de recursos criado no processo deste tutorial e, em seguida, usar o comando **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passo da implantação de código Python no Serviço de Aplicativo no Linux!

Conforme observado anteriormente, você pode aprender mais sobre a extensão do Serviço de Aplicativo visitando seu repositório do GitHub, [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice). Relatos de problemas e contribuições também são bem-vindos.

Para saber mais sobre os serviços do Azure que você pode usar no Python, incluindo armazenamento de dados com IA e os serviços do Machine Learning, visite o [Centro de Desenvolvedores de Python do Azure](https://docs.microsoft.com/python/azure/?view=azure-python).

Também há outras extensões do Azure para VS Code que podem ser úteis. Basta pesquisar "Azure" no explorador de Extensões:

![Extensões do Azure para VS Code](media/deploy-containers/azure-extensions.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do ARM (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Terminei](https://docs.microsoft.com/python/azure/?view=azure-python) 

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=07-clean-up-resources)