---
title: Implantar um site do Node.js estático no Azure usando o Visual Studio Code
description: Parte 1 do tutorial, introdução e pré-requisitos.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 45dfb1f32d98385b2cb944340b4601de804e133f
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685908"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Implantar um site estático no Azure usando o Visual Studio Code

Neste tutorial, você vai criar e implantar um site estático no Azure usando o [Armazenamento do Azure](https://docs.microsoft.com/azure/storage). Um site estático é composto por HTML, CSS, JavaScript e outros arquivos estáticos, como imagens ou fontes. Um site estático normalmente é um aplicativo de página única (ou [SPA](https://en.wikipedia.org/wiki/Single-page_application)) escrito com Angular ou React. No entanto, você cria o aplicativo, hospeda e serve esses arquivos diretamente do *armazenamento*, em vez de usando um servidor Web. Hospedar no armazenamento é mais simples e mais barato do que manter um servidor Web.

> [!NOTE]
> Se você tiver seu próprio código de servidor, como um aplicativo Node.js/Expresso, siga o [tutorial do Serviço de Aplicativo](tutorial-vscode-azure-app-service-node-01.md), em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão de Armazenamento do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
- [Node.js e npm](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js. (Esse requisito é usado apenas para gerar um projeto de exemplo. Você não precisará instalar o Node.js se já tiver o código do aplicativo.)

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurestorage">Instalar a extensão do Armazenamento do Azure</a>

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Instalei os pré-requisitos](tutorial-vscode-static-website-node-02.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)