---

copyright:
  years: 2014, 2016

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 使用大型文件 {: #large-files}
*上次更新时间：2016 年 10 月 19 日*
{: .last-updated}

在单个上传中，上传对象的最大大小限制为 5 GB。但是，如果将大于 5 GB 的对象分段为较小的对象，那么仍可以上传。一旦上传了分段对象，那么还需要清单文件来将这些分段合并成原始对象。这可通过两种方式实现：动态大对象 (DLO) 和静态大对象 (SLO)。
{: shortdesc}

### 动态大对象： {: #dynamic}

处理 DLO 的方式有两种：
  * 使 Swift 客户机自动处理一切操作
  * 使用 Swift API 手动处理

#### 使用 Swift 客户机处理动态大对象

Swift 客户机使用 `-segment-size` 参数将对象分解成较小的部分。客户机会创建一个新容器，新容器的名称将使用上传文件的目标容器的名称，并添加一个分段号作为后缀 (`<container_name>_segments`)。各个分段会并行上传。上传完所有分段后，会将这些分段作为一个合并对象下载到使用原始文件名的清单文件。

1. 登录到 {{site.data.keyword.Bluemix_notm}} 并已准备好上传后，运行以下命令对文件分段。

    ```
swift upload <container_name> <file_name> --segment-size <size_in_bytes>
```
    {: pre}

#### 使用 Swift API 处理动态大对象

您可以手动对对象分段，使其大小小于 5 GB，然后通过 Swift API 进行上传。上传时，一定要先上传所有分段，然后再上传清单。如果还未上传完所有分段就下载了对象，那么下载的对象将不一致。可以通过完成以下步骤上传大型文件。

1. 按名称对分段排序，分段应该按此顺序合并才能构成原始对象。
2. 将分段上传到一个容器，该容器须不同于保存清单文件的容器。上传第 10 个分段之后将开始对上传进行调速，这会大大延长上传时间。为此，建议分段大小不要小于文件大小的十分之一。

    ```
    curl -i -X PUT --data-binary @segment1 -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_name>/<object_name>/000001
    curl -i -X PUT --data-binary @segment2 -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_name>/<object_name>/000002
    ```
    {: pre}
    
3. 上传空清单文件，文件头 `X-Object-Manifest` 设置为相应的 `<container>/prefix>` 值。

    ```
    curl -i -X PUT -H "X-Auth-Token: <token>" -H "X-Object-Manifest: <container_name>/<object_name>/" https://<object-storage_url>/<manifest_container_name>/<object_name>
    ```
    {: pre}
    
    **注**：清单文件必须为空。如果不为空，该文件的内容将被视为其中一个分段，并且将按已排序名称所指示的合并顺序排列。
4. 下载对象。结果您将收到整个对象。您可以添加或除去分段，而不必更新清单文件。分段只要前缀正确，就将一直是对象的组成部分。删除清单不会删除这些分段。

    ```
    curl -i -O -H "X-Auth-Token: <token>" https://<object-storage_url>/<manifest_container_name>/<object_name>
    ```
    {: pre}


### 静态大对象 {: #static}

静态大对象也使用分段和清单文件，但您可以进行更多控制。通过 SLO，分段不必位于同一容器中；每个分段可以存储在任意容器中，并且可以任意命名。但是，分段的大小必须至少为 1 MB。您无需设置清单文件的头，但在上传了正确的清单后，头“X-Static-Large-Object”会自动添加并设置为 true。
{: shortdesc}

清单文件是一种提供分段详细信息的 JSON 文档，必须在上传了所有分段之后上传。在清单中为每个分段提供的数据会与实际分段元数据进行比较。如果有内容不匹配，那么不会上传清单。

<table>
  <tr>
    <th> 属性</th>
    <th> 描述</th>
  </tr>
  <tr>
    <td> path</td>
    <td> 分段的位置和名称。指定为 container_name/object_name。</td>
  </tr>
  <tr>
    <td> etag</td>
    <td> 上传对象时由 PUT 请求提供。您还可以通过对对象执行 HEAD 来找到此属性。</td>
  </tr>
  <tr>
    <td> size_bytes</td>
    <td> 对象的大小（以字节为单位）。</td>
  </tr>
</table>

*表 1：清单文件中按合并顺序列出的 JSON 属性*

可以通过完成以下步骤上传大型文件：

1. 运行以下命令上传分段。上传第 10 个分段之后将开始对上传进行调速，这会大大延长上传时间。为此，建议分段大小不要小于文件大小的十分之一。

    ```
    curl -i -X PUT --data-binary @segment1 -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_one>/<segment>
    curl -i -X PUT --data-binary @segment2 -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_two>/<segment>
    curl -i -X PUT --data-binary @segment3 -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_one>/<segment>
    ```
    {: pre}
    
2. 构建清单：

    ```
    [
        {
            "path": "<container_one>/<segment>",
            "etag": "e0ed3b751eb8d4b2c648d2dd78576e36",
            "size_bytes": 801882690
        },
        {
            "path": "container_two/<segment>",
            "etag": "65a301e71c82cbd325a5efe5877e1a24",
            "size_bytes": 1048576
        },
        {
            "path": "<container_one>/<segment>",
            "etag": "aea8b7462d527ad5ed0cfc22ea161062",
            "size_bytes": 138257
        }
    ]
    ```
    {: pre}
    
3. 上传清单。为此，必须通过运行以下命令，将查询 `multipart-manifest=put` 添加到清单的名称：

    ```
    curl -i -X PUT --data-binary @object_name -H "X-Auth-Token: <token>" https://<object-storage_url>/container_two/<object_name>?multipart-manifest=put
    ```
    {: pre}
    
4. 下载对象。

    ```
    curl -O -X GET -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_two>/<object_name>
    ```
    {: pre}
    
下面是使用静态大对象时可能会需要的一些命令。

* 要下载清单文件的内容，必须将查询 `multipart-manifest=get` 添加到命令。收到的内容不会与上传的内容相同。

    ```
    curl -O -X GET -H "X-Auth-Token:<token>" https://<object-storage_url>/<container_two>/<object_name>?multipart-manifest=get
    ```
    {: pre}
    
* 要删除清单，请运行以下命令：

    ```
    curl -i -X DELETE -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_two>/<object_name>
    ```
    {: pre}
    
* 要删除清单及所有分段，请将查询 `multipart-manifest=delete` 添加到清单名称之后：

    ```
    curl -i -X DELETE -H "X-Auth-Token: <token>" https://<object-storage_url>/<container_two>/<object_name>?multipart-manifest=delete
    ```
    {: pre}

**注**：要将分段添加到对象或从对象中除去分段，您需要上传具有新分段列表的新清单文件。清单名称可以保持不变。
