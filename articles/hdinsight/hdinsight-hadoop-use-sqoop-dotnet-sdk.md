<properties
	pageTitle="HDInsight での Hadoop Sqoop の使用 | Microsoft Azure"
	description="HDInsight .NET SDK を使用して、Hadoop クラスターと Azure SQL Database 間で Sqoop のインポートおよびエクスポートを実行する方法について説明します。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="jgao"/>

#HDInsight で .NET SDK for Hadoop を使用した Sqoop ジョブの実行

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight .NET SDK を使用して、HDInsight クラスターと Azure SQL Database または SQL Server データベース間でインポートとエクスポートを実行する Sqoop ジョブを HDInsight で実行する方法について説明します。

> [AZURE.NOTE] この記事の手順は、Windows ベースまたは Linux ベースの HDInsight クラスターで使用できます。ただし、これらの手順は Windows クライアントでのみ機能します。他の方法を選択するには、この記事の上部にあるタブ セレクターをクリックしてください。

###前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **HDInsight の Hadoop クラスター**。「[クラスターと SQL Database を作成します](hdinsight-use-sqoop.md#create-cluster-and-sql-database)」をご覧ください。

## .NET SDK を使用した Sqoop の実行

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、C# コンソール アプリケーションを作成し、このチュートリアルで作成した SQL Database テーブルに hivesampletable をエクスポートします。

次の例では、ユーザーの対話型認証を使用します。非対話型認証を使用する場合は、「[非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md)」をご覧ください。


**Sqoop ジョブを送信するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. Visual Studio パッケージ マネージャー、コンソールから、次の Nuget コマンドを実行し、パッケージをインポートします。

		Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
        
3. Program.cs ファイルで次のコードを使用します。

        using System;
        using System.Collections.Generic;
        using System.Security;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
                private const string ResourceGroupName = "<Your Resource Group Name>";

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");

                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();

                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };

                    if (username != null && password != null)
                        account.Id = username;

                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;

                    return new TokenCloudCredentials(accessToken);
                }

                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), creds.Token);
                }


                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";

                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";

                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;

                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string>{"/user/oozie/share/lib/sqoop/sqljdbc41.jar"}, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };

                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. **F5** キーを押して、プログラムを実行します。

##次のステップ

ここでは Sqoop の使用方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Oozie の使用](hdinsight-use-oozie.md): Oozie ワークフローで Sqoop アクションを使用します。
- [HDInsight を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data.md): Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL データベースにデータをエクスポートします。
- [HDInsight へのデータのアップロード](hdinsight-upload-data.md): HDInsight/Azure BLOB ストレージにデータをアップロードするその他の方法を説明します。

<!---HONumber=AcomDC_0413_2016-->