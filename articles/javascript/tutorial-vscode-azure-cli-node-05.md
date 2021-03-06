---
title: Transmitir logs do Serviço de Aplicativo do Azure
description: Parte 5 do tutorial, exibir logs
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 7a962f7e6dc7cdb572f78e40b25e1ff98f102ea3
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88217900"
---
# <a name="stream-logs-from-app-service"></a>Transmitir logs do Serviço de Aplicativo

[Etapa anterior: Implantar o aplicativo](tutorial-vscode-azure-cli-node-04.md)

Nesta etapa, você exibe (ou "acompanha") os logs do Serviço de Aplicativo em execução. Todas as chamadas para `console.log` no código do site são exibidas no terminal.

1. Execute o comando a seguir para iniciar o registro em log, substituindo `<your_app_name>` pelo nome de seu Serviço de Aplicativo:

    ```azurecli
    az webapp log tail --name <your_app_name>
    ```

1. Após alguns segundos, deverá ser exibida uma mensagem na saída indicando que você está conectado ao serviço de streaming de log.

    <pre>
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Atualize a página algumas vezes no navegador para gerar uma saída adicional:

    <pre>
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    </pre>

1. Pressione **Ctrl**+**C** para encerrar a sessão de log.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-azure-cli-node-06.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
