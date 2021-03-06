---
title: Usar o Habitat para implantar rapidamente seu aplicativo no Azure
description: Saiba como implantar consistentemente seu aplicativo para máquinas virtuais do Microsoft Azure e contêineres
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar, habitat
ms.date: 05/15/2018
ms.topic: article
ms.custom: devx-track-chef
ms.openlocfilehash: d2a834c631986b70a13c95f1403e84e82886a5f2
ms.sourcegitcommit: 95fdc444c424f4a7d7d53437837e9532a0b897e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662937"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usar o Habitat para implantar rapidamente seu aplicativo no Azure

[Habitat](https://www.habitat.sh/) é um sistema de runtime e empacotamento de aplicativo que empacota o aplicativo e sua automação em conjunto, como a unidade de implantação. Isso cria portabilidade máxima para o aplicativo, permitindo que ele seja implantado em máquinas virtuais, contêineres, diretamente em hardware ou em PaaS, sem reescrita nem reempacotamento.

Este artigo descreve os principais benefícios de usar o Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernizar e mover aplicativos herdados

Libere os aplicativos herdados de sistemas operacionais mais antigos e middleware reempacotando-os com o Habitat. O artefato resultante é portátil e muda de plataforma facilmente para uma infraestrutura mais recente, assim como máquinas virtuais ou contêineres em execução na nuvem.

## <a name="accelerate-container-adoption"></a>Acelere a adoção do contêiner

Representando com precisão as dependências de runtime, o Habitat resolve a implantação contínua de aplicativos complexos e orientados a microsserviços. Vá além da simples implantação azul/verde de componentes individuais e projete um comportamento de implantação sofisticado, sem gerar fluxos de orquestração complexos.

## <a name="run-any-application-anywhere"></a>Execute qualquer aplicativo em qualquer lugar

Com o Habitat, os aplicativos podem ser executados sem modificações em qualquer ambiente de runtime. Isso inclui tudo, de bare-metal e máquinas virtuais a contêineres (como o Docker), sistemas de gerenciamento de cluster (como o Mesosphere ou o Kubernetes) e sistemas de PaaS (como o VMware Tanzu Application Service, antigo Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrar o fluxo de trabalho do Chef DevOps

O projeto do Habitat é um de um projeto de software livre da Chef Software, com uma forte comunidade de suporte. O Habitat aproveita a experiência do Chef em automação de infraestrutura para colocar os recursos de automação sem precedentes em aplicativos. A Chef oferece suporte comercial para o Habitat e cria uma integração perfeita entre o Habitat e o Chef Automate, para automatizar totalmente o ciclo de versão do aplicativo do desenvolvimento à implantação.

## <a name="next-steps"></a>Próximas etapas

* [Experimente o Habitat](https://www.habitat.sh/learn/)
