---

copyright:
  years: 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}


# 关于 {{site.data.keyword.deliverypipeline}}
{: #deliverypipeline_about}
上次更新时间：2016 年 8 月 29 日
{: .last-updated}

IBM&reg; Bluemix&reg; {{site.data.keyword.deliverypipeline}} 服务也称为管道，可自动连续部署 Bluemix 项目。在管道中，阶段序列可检索输入并运行作业（例如，构建、测试和部署）。
{:shortdesc}

以下各节描述管道背后的概念性详细信息。

## 阶段
{: #deliverypipeline_stages}

在构建、部署和测试代码时，阶段可用于组织输入和作业。阶段从源控制存储库接受输入，或者在其他阶段中构建作业。当您创建第一个阶段时，会在**输入**选项卡上为您设置缺省设置。

阶段输入会传递到其包含的作业中，每一个作业会提供一个运行所在的明确容器。阶段中的作业彼此之间无法传递工件。

您可以定义可在所有作业中使用的阶段环境属性。例如，您可以定义 `TEST_URL` 属性，以传递单个 URL，以在单个阶段中部署和测试作业。部署作业将部署至该 URL，而测试作业将在该 URL 测试正在运行的应用程序。

缺省情况下，在阶段中，每次更改传递至项目的源控制存储库时，都会自动触发构建和部署操作。阶段和作业串行运行；它们为您的工作实现流控制。例如，您可以将测试阶段置于部署阶段之前。如果测试阶段中的测试失败，则部署阶段不会运行。

您可能想要对特定阶段进行更严格的控制。如果在某个阶段输入时发生了更改，而您不想在每次发生更改时该阶段都运行，那么您可以禁用该功能。在**输入**选项卡的“阶段触发器”部分中，单击**仅当手动运行此阶段时才运行作业**。

![“输入”选项卡](./images/input_tab_only_execute.png)

## 作业
{: #deliverypipeline_jobs}

作业是阶段中的执行单元。阶段可以包含多个作业，阶段中的作业可以按顺序运行。缺省情况下，如果某个作业失败，则阶段中的后续作业不会运行。

![阶段中的构建和测试作业](./images/jobs.png)

在独立工作目录中运行的作业，这些工作目录位于针对每一个管道运行所创建的 Docker 容器中。在作业运行之前，其工作目录中会填充在阶段级别定义的输入。例如，您可能具有包含测试作业和部署作业的阶段。如果您安装一个作业的依赖关系，那么它们无法用于另一个作业。但是，如果您在阶段输入中使用依赖关系，那么它们可用于这两个作业。

除了简单类型构建作业之外，当您配置作业时，可以包括内含构建、测试或部署命令的 UNIX shell 脚本。因为作业在特定容器中运行，所以一个作业的操作无法影响其他作业的运行环境，即使这些作业属于相同的阶段也是如此。

作业运行之后，即会废弃针对其创建的容器。可以持久存储作业运行的结果，但是不会持久存储其运行的环境。

**注**：作业可以运行长达 60 分钟。当作业超过此限制时即会失败。如果作业即将超过此限制，请将其分成多个作业。例如，如果作业执行三个任务，那么您可以将其分成三个作业：每个任务一个作业。

要了解如何将作业添加到阶段，请参阅[添加作业到阶段](./build_deploy.html#deliverypipeline_add_job)。

### 构建作业

构建作业可对您的项目进行编译，以为部署做好准备。它们会生成可发送到构建归档目录的工件，但是缺省情况下，工件会置于项目的根目录中。

从构建作业接受输入的作业必须参考创建它们的相同目录中的构建工件。例如，如果构建作业将构建工件归档至 `output` 目录，那么部署脚本会参考 `output` 目录而非项目根目录，以部署编译过的项目。

**注**：如果您针对构建作业选择**简单**构建器类型，那么您会跳过构建过程。在该情况下，不会编译您的代码，而是会将其按原样发送到部署阶段。要构建并部署，请选择**简单**以外的构建器类型。

#### 构建脚本的环境属性
您可以在构建作业的构建 shell 命令中包括环境属性。这些属性提供作业执行环境相关信息的访问权。有关更多信息，请参阅 [{{site.data.keyword.deliverypipeline}} 服务的环境属性和资源](./deploy_var.html)。

### 部署作业

部署作业可将项目作为应用程序上传至 Bluemix，且可通过 URL 进行访问。部署项目之后，可以在 Bluemix“仪表板”上查找已部署的应用程序。您可以将构建和部署作业配置为不同的阶段，或者将它们添加到相同的阶段以自动运行。

部署作业可以部署新的应用程序或更新现有的应用程序。即使您最初使用其他方法（如 Cloud Foundry 命令行界面或 Web IDE 中的运行栏）部署了应用程序，您也可以使用部署作业更新该应用程序。要更新应用程序，请在部署作业中，使用该应用程序的名称。

可以部署到一个或多个区域和服务。例如，您可以设置 {{site.data.keyword.deliverypipeline}} 服务，以便在一个区域中测试使用了 IBM Containers 的开发工件，并将其部署到多个区域中的生产环境。有关更多信息，请参阅[区域](../../overview/index.html#ov_intro__reg)。

#### 部署脚本的环境属性

您可以在部署作业的部署脚本中包括环境属性。这些属性提供作业执行环境相关信息的访问权。有关更多信息，请参阅 [{{site.data.keyword.deliverypipeline}} 服务的环境属性和资源](./deploy_var.html)。

### 测试作业
如果您想要要求满足条件，请在构建和部署作业之前或之后，包括测试作业。您可以定制测试作业，按您的需要定制为简单或复杂。例如，您可能会发出 cURL 命令并期望获得特定响应。您还可能使用第三方测试服务（如 Sauce Labs），运行一组单元测试或触发功能测试。

如果测试产生 JUnit XML 格式的结果文件，那么基于结果文件的报告将显示在每个测试结果页面的**测试**选项卡上。如果测试失败，那么作业也会失败。

#### 测试脚本的环境属性

您可以在测试作业的脚本中包括环境属性。这些属性提供作业运行环境相关信息的访问权。有关更多信息，请参阅 [{{site.data.keyword.deliverypipeline}} 服务的环境属性和资源](./deploy_var.html)。

## 清单文件
{: #deliverypipeline_manifest}

清单文件名为 `manifest.yml`，存储在项目的根目录中，用于控制如何将项目部署到 Bluemix。有关创建项目清单文件的信息，请参阅[应用程序清单的 Bluemix 文档](https://www.ng.bluemix.net/docs/manageapps/deployingapps.html#appmanifest)。要与 Bluemix 集成，项目的根目录中必须包含清单文件。但是，无需基于该文件中的信息进行部署。

在管道中，您可以使用 `cf push` 命令自变量，指定清单文件可以执行的任何事项。`cf push` 命令自变量在具有多个部署目标的项目中非常有用。如果多个部署作业全部尝试使用项目清单文件中指定的路径，那么会发生冲突。

要避免冲突，您可以使用 `cf push` 后接主机名自变量 `-n` 和路径名，来指定路径。通过修改各个阶段的部署脚本，可以避免部署到多个目标时发生的路径冲突。

若要使用 `cf push` 命令自变量，请打开部署作业的配置设置，并修改**部署脚本**字段。有关更多信息，请参阅 [Cloud Foundry 推送文档](http://docs.cloudfoundry.org/devguide/installcf/whats-new-v6.html#push)。

## 管道示例
{: #deliverypipeline_example}

简单管道可能包含三个阶段：

1. 构建阶段，对应用程序进行编译并运行构建过程。
2. 测试阶段，部署应用程序的实例，并对其运行测试。
3. 生产阶段，部署已测试应用程序的生产实例。

此管道在以下概念图中显示：

![管道中阶段和作业的概念图](./images/diagram.jpg)

*三个阶段管道的概念模型*

阶段从存储库和构建作业接受输入，阶段中的作业按顺序彼此独立地运行。在管道示例中，阶段将按顺序运行，即使测试和生产阶段都将构建阶段的输出作为其输入也是如此。

<!--
[1]: https://www.ng.bluemix.net/docs/manageapps/deployingapps.html#appmanifest
[2]: https://www.ng.bluemix.net/docs/#services/DeliveryPipeline/index.html#getstartwithCD
[3]: http://docs.cloudfoundry.org/devguide/installcf/whats-new-v6.html#push
[4]: https://console.ng.bluemix.net/?ace_base=true/#/pricing/cloudOEPaneId=pricing
[5]: ./images/open_logs.png
[6]: #manifests
[7]: ./images/runbar-annotated-dark.png
[8]: ./images/input_tab_only_execute.png
[9]: ./images/deploy_to.png
[10]: ./images/view_logs_and_history.png
[11]: ./images/play_button.png
[12]: ./images/basicAnimate.gif
[13]: ./images/AddStage.png
[14]: ./images/AddJob.png
[15]: ./images/jobs.png
[16]: ./images/RunStage.png
[17]: https://www.ng.bluemix.net/docs/starters/container_pipeline.html#container_pipeline
[18]: ../../../tutorials/basicbuild
[19]: #add_stage
[20]: #add_job
[21]: ../deploy_ext
[22]: ./images/pipeline_settings_icon.png
[23]: https://www.ng.bluemix.net/docs/services/reqnsi.html#add_service
[24]: ../deploy_var
[25]: ./images/click_stage_run_number.png
[26]: ./images/diagram.jpg
-->
