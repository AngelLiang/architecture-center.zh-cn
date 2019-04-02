---
title: 选择机器学习技术
description: 比较生成、部署和管理机器学习模型所需的选项。 确定要为解决方案选择哪种 Microsoft 产品。
author: MikeWasson
ms.date: 03/06/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 1020e938a04c6a82e6cc831e6620ec17c9a10cc7
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503224"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Microsoft 的机器学习产品有哪些？

机器学习是一项数据科研技术，可以让计算机根据现有的数据来预测将来的行为、结果和趋势。 使用机器学习，计算机可以在不需显式编程的情况下进行学习。

机器学习解决方案以迭代方式，构建，并且具有不同的阶段：

- 准备数据
- 实验和训练模型
- 部署训练的模型
- 管理部署模型

Microsoft 提供的各种产品选项，以准备、 生成、 部署和管理机器学习模型。 本文将比较这些产品，并帮助你选择所需的产品，以便最有效地开发机器学习解决方案。

## <a name="cloud-based-options"></a>基于云的选项

为 Azure 云中的机器学习提供了以下选项。

| 云&nbsp;选项 | 作用 | 作用 |
|-|-|-|
| [Azure 机器学习服务](#azure-machine-learning-service) | 机器学习的托管的云服务  | 使用 Python 和 CLI 在 Azure 中训练、部署和管理模型 |
| [Azure 机器学习工作室](#azure-machine-learning-studio) | 拖动&ndash;和&ndash;机器学习的可视界面 | 使用预配置的算法生成、试验和部署模型 |

如果想要使用预建 AI 和机器学习模型， [Azure 认知服务](#azure-cognitive-services)，可轻松地将智能功能添加到你的应用程序。

## <a name="on-premises-options"></a>本地选项

为本地的机器学习提供了以下选项。 本地服务器也可以在云端的虚拟机中运行。

| 本地&nbsp;选项 | 作用 | 作用 |
|-|-|-|
| [SQL Server 机器学习服务](#sql-server-machine-learning-services) | SQL 中嵌入的分析引擎 | 在 SQL Server 内部生成和部署模型 |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | 用于预测分析的独立企业服务器 | 生成和部署预先处理的数据模型 |

## <a name="development-platforms-and-tools"></a>开发平台和工具

适用于机器学习以下开发平台和工具。

| 平台/工具 | 作用 | 作用 |
|-|-|-|
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | 预装了数据科学工具的虚拟机 | 开发在预配置的环境中的机器学习解决方案 |
| [Azure Databricks](#azure-databricks) | 基于 Spark 的分析平台 | 生成和部署模型与数据工作流 |
| [ML.NET](#mlnet) | 开放源代码的跨平台的机器学习 SDK | 开发.NET 应用程序的机器学习解决方案 |
| [Windows ML](#windows-ml) | Windows 10 机器学习平台 | 在 Windows 10 设备上评估已训练的模型 |

## <a name="azure-machine-learning-service"></a>Azure 机器学习服务

[Azure 机器学习服务](/azure/machine-learning/service/overview-what-is-azure-ml.md)是用于定型、 部署和管理大规模机器学习模型的完全托管的云服务。 它完全支持开源技术，可让你使用数以万计的开源 Python 包，例如 TensorFlow、PyTorch 和 scikit-learn。 它还提供丰富的工具，例如 [Azure Notebook](https://notebooks.azure.com/)、[Jupyter Notebook](http://jupyter.org) 或[用于 Visual Studio Code 的 Azure 机器学习](https://aka.ms/vscodetoolsforai)扩展，以方便探索和转换数据，然后训练和部署模型。 Azure 机器学习服务包含可以轻松、高效和准确地自动化模型生成和优化的功能。

Azure 机器学习服务用于定型、 部署和管理云规模的使用 Python 和 CLI 的机器学习模型。

试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

|||
|-|-|
|类型                   |基于云的机器学习解决方案|
|**支持的语言**    |Python|
|**机器学习阶段**|数据准备工作<br>模型定型<br>部署<br>管理|
|**主要优势**           |集中管理脚本和运行历史记录，轻松比较模型版本。<br/><br/>轻松部署和管理云中或边缘设备上的模型。|
|**注意事项**         |需要对模型管理模型有一定的了解。|

## <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室

[Azure 机器学习工作室](/azure/machine-learning/studio/)提供交互式的可视工作区，用于通过预生成的机器学习算法快速轻松地生成、测试和部署模型。 机器学习工作室将模型发布为可让自定义应用或 BI 工具（如 Excel）方便使用的 Web 服务。
无需任何编程 - 在交互式画布上连接数据集和分析模块，然后按几个鼠标将其部署，即可构造机器学习模型。

希望在不编写任何代码的情况下开发和部署模型时，可以使用机器学习工作室。

试用在付费或免费选项中提供的 [Azure 机器学习工作室](https://studio.azureml.net/?selectAccess=true&o=2&target=_blank)。

|||
|-|-|
|类型                   |基于云的、 拖放的机器学习解决方案|
|**支持的语言**    |Python、R|
|**机器学习阶段**|数据准备工作<br>模型定型<br>部署<br>管理|
|**主要优势**           |交互式可视化界面可让用户以极少量的代码实现机器学习建模。<br/><br/>内置 Jupyter Notebook 用于数据探索。<br/><br/>将训练的模型作为 Azure Web 服务直接部署。|
|**注意事项**         |可伸缩性有限。 训练数据集的最大大小为 10 GB。<br/><br/>只能联机使用。 不支持脱机开发环境。|

## <a name="azure-cognitive-services"></a>Azure 认知服务

[Azure 认知服务](/azure/cognitive-services/welcome)是一组 API，可用于生成使用自然通信方法的应用。 借助这些 API，只需编写几行代码，就能让应用看到、听到、讲出、理解和解释用户的需求。 将智能功能轻松添加到应用，例如：

- 情感和观点检测
- 视觉和语音识别
- 语言理解 (LUIS)
- 知识和搜索

使用认知服务可以开发跨设备和平台的应用。 API 不断改进，且易于设置。

|||
|-|-|
|类型                   |Api，可用于构建智能应用程序|
|**支持的语言**    |具体取决于该服务的许多选项|
|**机器学习阶段**|部署|
|**主要优势**           |将应用程序使用预先训练的模型中的机器学习功能。<br/><br/>不同的模型以使用视觉和语音的自然通信方法。|
|**注意事项**         |模型已预先训练，并不是可自定义。|

## <a name="sql-server-machine-learning-services"></a>SQL Server 机器学习服务

[SQL Server Microsoft 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)为 SQL Server 数据库中的关系数据添加了采用 R 和 Python 语言的统计分析、数据可视化和预测分析。 从 Microsoft R 和 Python 库包括高级的建模和机器学习算法，可以运行大规模并行地，SQL Server 中。

需要对 SQL Server 中的关系数据使用内置 AI 和预测分析时，可以使用 SQL Server 机器学习服务。

|||
|-|-|
|类型                   |本地关系数据的预测分析|
|**支持的语言**    |Python、R|
|**机器学习阶段**|数据准备工作<br>模型定型<br>部署|
|**主要优势**           |在数据库函数中封装预测逻辑可以轻松加入数据层逻辑。|
|**注意事项**         |采用 SQL Server 数据库作为应用程序的数据层。|

## <a name="microsoft-machine-learning-server"></a>Microsoft 机器学习服务器

[Microsoft 机器学习服务器](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)是一个企业服务器，用于托管和管理 R 与 Python 进程的并行与分布式工作负荷。 Microsoft Machine Learning Server 在 Linux、Windows、Hadoop 和 Apache Spark 上运行，也适用于 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/)。 它为使用 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 和 [MicrosoftML 包](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)生成的解决方案提供执行引擎，并扩展了开源 R 和 Python，支持高性能分析、统计分析、机器学习和巨型数据集。 此功能通过可以连同服务器一起安装的专属包提供。 对于开发，可以使用[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/vs/rtvs/)和[针对 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)等 IDE。

需要在服务器上使用 R 和 Python 生成与操作化模型时，或者需要在 Hadoop 或 Spark 群集上大规模分配 R 和 Python 训练工作负荷时，可以使用 Microsoft Machine Learning Server。

|||
|-|-|
|类型                   |用于预测分析的本地企业服务器|
|**支持的语言**    |Python、R|
|**机器学习阶段**|模型定型<br>部署|
|**主要优势**           |高度可伸缩性。|
|**注意事项**         |需要在企业中部署和管理 Machine Learning Server。|

## <a name="azure-data-science-virtual-machine"></a>Azure 数据科学虚拟机

[Azure Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) 是专为开展数据科学生成的 Microsoft Azure 云上的自定义虚拟机环境。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。

支持将 Data Science Virtual Machine 用作 Azure 机器学习服务的目标。
它提供适用于 Windows 和 Linux Ubuntu 的版本（Linux CentOS 不支持 Azure 机器学习服务）。
有关特定版本的信息以及版本功能的列表，请参阅 [Azure Data Science Virtual Machine 简介](/azure/machine-learning/data-science-virtual-machine/overview.md)。

需要在单个节点上运行或托管作业时，可以使用数据科学 VM。 或者，需要在单个计算机上远程提高处理能力时，也可以使用它。

|||
|-|-|
|类型                   |用于数据科学的自定义的虚拟机环境|
|**主要优势**           |减少安装、管理数据科学工具和框架及其故障排除的时间。<br/><br/>包含所有常用工具和框架的最新版本。<br/><br/>虚拟机选项包括高度可缩放的映像和 GPU 功能用于密集型数据建模。|
|**注意事项**         |脱机时无法访问虚拟机。<br/><br/>运行虚拟机会产生 Azure 费用，因此请注意，只在有需要时才运行。|

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。 Databricks 与 Azure 集成，以提供一键式安装程序、简化的工作流程以及交互式工作区，从而使数据科学家、数据工程师和业务分析员之间可以进行协作。
在基于 Web 的 Notebook 中使用 Python、R、Scala 和 SQL 代码可以查询、可视化数据以及为其建模。

想要在 Apache Spark 中协作生成机器学习解决方案时，可以使用 Databricks。

|||
|-|-|
|类型                   |基于 Apache Spark 的分析平台|
|**支持的语言**    |Python, R, Scala, SQL|
|**机器学习阶段**|数据查询<br>模型定型|

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) 是免费的开源跨平台机器学习框架，可用于生成自定义机器学习解决方案并将其集成到 .NET 应用程序中。

想要将机器学习解决方案集成到 .NET 应用程序时，可以使用 ML.NET。

|||
|-|-|
|类型                   |开放源代码框架，用于开发自定义机器学习应用程序|
|**支持的语言**    |.NET|

## <a name="windows-ml"></a>Windows ML

[Windows 机器学习](https://docs.microsoft.com/windows/uwp/machine-learning/)推理引擎，可使用定型的机器学习应用程序中的模型，评估 Windows 10 设备上本地的已训练的模型。

想要在 Windows 应用程序中使用训练的机器学习模型时，可以使用 Windows ML。

|||
|-|-|
|类型                   |对于 Windows 设备的已训练模型的推理引擎|
|**支持的语言**    |C#/ C + +、 JavaScript|

## <a name="next-steps"></a>后续步骤

- 若要了解 Microsoft 提供的所有人工智能 (AI) 开发产品，请参阅 [Microsoft AI 平台](https://www.microsoft.com/ai)
- 有关如何开发 AI 解决方案的培训内容，请参阅 [Microsoft AI 学校](https://aischool.microsoft.com/learning-paths)
