<properties
 pageTitle="クラウドでの Linux HPC Pack クラスター オプション | Microsoft Azure"
 description="Microsoft HPC Pack を使用して Azure クラウドで Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Microsoft HPC Pack を使用して Azure で Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプション

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

HPC Pack で Windows HPC ワークロードを実行する場合は、「[Microsoft HPC Pack を使用して Azure で Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプション](virtual-machines-windows-hpcpack-cluster-options.md)」をご覧ください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Azure VM での HPC Pack クラスターの実行

### Azure テンプレート


* (Marketplace) [HPC Pack cluster for Linux workloads (Linux ワークロード用の HPC Pack クラスター)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (クイックスタート) [Create an HPC cluster with Linux compute nodes (Linux コンピューティング ノードがある HPC クラスターを作成する)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Azure VM イメージ

* [Windows Server 2012 R2 上の HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### PowerShell デプロイメント スクリプト

* [Create an HPC cluster with the HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した HPC クラスターの作成)](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Tutorials (チュートリアル)

* [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md)

* [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)



### クラスターの管理

* [Submit jobs to an HPC Pack cluster in Azure (Azure の HPC Pack クラスターにジョブを送信する)](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)



## MPI ワークロードのための RDMA のクラスターの作成

* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0406_2016-->