---
ms.openlocfilehash: a6e51b3fca9e2ae633a69b6372d7f622d2697812
ms.sourcegitcommit: c6642cae6fdb5e3025ed66fcd4ef89792c3b436a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405747"
---
| Amostra  | Descrição |
|---|---|
| **Criar máquinas virtuais** ||
| [Gerenciar máquinas virtuais][1] | Criar, modificar, iniciar, parar e excluir máquinas virtuais. |
| [Criar uma máquina virtual com base em uma imagem personalizada][2] | Criar uma imagem da máquina virtual e usá-la para criar novas máquinas virtuais. | 
| [Criar uma máquina virtual usando a VHD específica a partir de um instantâneo][3] | Crie um instantâneo a partir dos discos de dados e do sistema operacional da máquina virtual, crie discos gerenciados a partir de instantâneos e, em seguida, crie uma máquina virtual anexando os discos gerenciados. |  
| [Criar máquinas virtuais em paralelo na mesma rede][4] | Crie máquinas virtuais na mesma região e na mesma rede virtual com duas sub-redes em paralelo. |
| [Criar máquinas virtuais entre regiões em paralelo][5] | Crie e faça o balanceamento de carga de um conjunto de máquinas virtuais entre várias regiões do Azure. |
| **Máquinas virtuais de rede** || 
| [Gerenciar redes virtuais][6] | Configure uma rede virtual com duas sub-redes e restrinja o acesso delas à Internet. |
| **Criar conjuntos de dimensionamento** ||
| [Criar um conjunto de dimensionamento de máquinas virtuais com um balanceador de carga][7] | Crie um conjunto de dimensionamento da VM, configure um balanceador de carga e obtenha cadeias de conexão SSH para as VMs do conjunto de dimensionamento. |

[1]: ../java-sdk-manage-virtual-machines.md
[2]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-custom-image/
[3]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-specialized-disk-from-vhd/
[4]: https://github.com/Azure-Samples/compute-java-manage-virtual-machines-in-parallel/
[5]: ../java-sdk-virtual-machines-in-parallel.md
[6]: ../java-sdk-manage-virtual-networks.md
[7]: ../java-sdk-manage-vm-scalesets.md