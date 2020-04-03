---
title: 'Etapa 5: Implantar um aplicativo Web do Python no Serviço de Aplicativo do Azure no Linux usando o VS Code'
description: Etapa 5 do tutorial, implantando o código do aplicativo Web
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 3f17fe3190857d1cc4faca84c9319e1c514cea4c
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441201"
---
# <a name="5-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>5: Implantar o aplicativo Web Python no Serviço de Aplicativo do Azure no Linux

[Etapa anterior: configurar um arquivo de inicialização personalizado](tutorial-deploy-app-service-on-linux-04.md)

Use este procedimento para implantar o aplicativo Python em um Serviço de Aplicativo do Azure.

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo** e selecione a seta para cima azul:

   ![Implantar seu aplicativo Web no Serviço de Aplicativo no gerenciador do Serviço de Aplicativo](media/deploy-azure/deploy-web-app-to-app-service-in-app-service-explorer.png)

    Como alternativa, você pode clicar com o botão direito do mouse no nome do Serviço de Aplicativo e selecionar o comando **Implantar no Aplicativo Web**.

1. Nos prompts seguintes, forneça os seguintes detalhes:

    - Para "Selecionar a pasta a implantar", selecione a pasta do aplicativo atual.
    - Para "Selecionar Aplicativo Web", escolha o Serviço de Aplicativo criado na etapa anterior.

1. Enquanto o processo de implantação estiver em andamento, você poderá exibir o progresso na janela **Saída** do VS Code.

    ![Progresso da implantação na janela de saída do VS Code](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. Quando a implantação for concluída após alguns minutos (dependendo de quantas dependências precisam ser instaladas), a mensagem abaixo será exibida. Selecione o botão **Procurar Site** para exibir o site em execução.

    ![Implantação concluída com o botão Procurar no site](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![Aplicativo executado com êxito no Serviço de Aplicativo](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. Para verificar se os arquivos estão implantados, expanda o Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e, em seguida, expanda **Arquivos**:

    ![Verificando os arquivos de implantação no explorador do Serviço de Aplicativo](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    O Serviço de Aplicativo cria um ambiente virtual com suas dependências na pasta *antenv*. Se expandir esse nó, você poderá verificar que os pacotes nomeados em *requirements.txt* estão instalados em *antenv/lib/python3.7/site-packages*.

> [!div class="nextstepaction"]
> [Implantei meu aplicativo – prossiga para a etapa 6 >>>](tutorial-deploy-app-service-on-linux-06.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)