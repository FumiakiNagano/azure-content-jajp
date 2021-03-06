<properties
    pageTitle="ARM テンプレートを使用して Service Bus 名前空間を作成する | Microsoft Azure"
    description="Azure Resource Manager テンプレートを使用して Service Bus 名前空間を作成します。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# ARM テンプレートを使用して Service Bus 名前空間を作成する

この記事では、Azure Resource Manager (ARM) テンプレートを使用し、Standard/Basic の SKU で "Messaging" タイプの Service Bus 名前空間を作成する方法について説明します。また、デプロイの実行用に指定するパラメーターについても取り上げます。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、GitHub の [Service Bus 名前空間テンプレート][]を参照してください。

>[AZURE.NOTE] 次の ARM テンプレートをダウンロードしてデプロイすることができます。
>
>-    [Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-event-hub.md)
>-    [キューを含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-queue.md)
>-    [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-topic.md)
>-    [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-auth-rule.md)
>
>最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」で「Service Bus」を検索してください。

## デプロイの対象

このテンプレートでは、[Basic または Standard](https://azure.microsoft.com/pricing/details/service-bus/) の SKU で Service Bus 名前空間をデプロイします。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-arm-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

テンプレートに含まれるそれぞれのパラメーターについて説明します。

### serviceBusNamespaceName

作成する Service Bus 名前空間の名前。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusSKU

作成する Service Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) の名前。

```
"serviceBusSKU": {
"type": "string",
"allowedValues": ["Basic","Standard"],
"defaultValue": "Standard"
}
```

テンプレートでは、このパラメーターに指定できる値 (Basic または Standard) を定義します。値が指定されない場合は既定値 (Standard) が割り当てられます。

Standard 階層には 1 か月あたり 10 ドルの基本料金があり、1 か月間に 1,250 万操作を追加コストなしで実行できます。Basic 階層では、100 万回の処理につき $0.05 の費用がかかります。

Service Bus の料金の詳細については、「[Service Bus の料金と課金][]」を参照してください。

### serviceBusApiVersion

テンプレートの Service Bus API バージョン。

```
"serviceBusApiVersion": {

"type": "string"

}
```

## デプロイ対象のリソース

### Service Bus 名前空間

**Messaging** タイプの標準的な Service Bus 名前空間を作成します。

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## 次のステップ

ARM を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
- [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
- [Service Bus リソースを Service Bus Explorer で管理する](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Service Bus 名前空間テンプレート]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/
  [Service Bus の料金と課金]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0420_2016-->