## Step Function

### [AWS状态语言](https://states-language.net/spec.html#example)

[状态机的输入输出处理](https://docs.amazonaws.cn/step-functions/latest/dg/concepts-input-output-filtering.html)

### 使用路径将状态输入作为参数传递

使用[路径](https://docs.amazonaws.cn/step-functions/latest/dg/amazon-states-language-input-output-processing.html#amazon-states-language-paths)将部分状态输入传递给参数。路径是以 $ 开头的字符串，用于标识 JSON 文本内的组成部分。Step Functions 路径使用 JsonPath 语法。

要指定参数使用路径引用输入中的 JSON 节点，请使用 .$ 作为参数名称的结尾。例如，如果在名为 message 的节点中的状态输入中有文本，则可以通过使用路径引用该输入 JSON，将其传递给参数。

```json
使用以下状态输入：

{
  "comment": "A message in the state input",
  "input": {
    "message": "foo",
    "otherInfo": "bar"
  },
  "data": "example"
}

可以使用以下命令将消息 foo 作为一个参数传递：

"Parameters": {"Message.$": "$.input.message"},
```

### Path

[ResultPath](https://docs.amazonaws.cn/step-functions/latest/dg/input-output-resultpath.html)

### S3事件触发step function

[启动状态机执行以响应 Amazon S3 事件](https://docs.amazonaws.cn/step-functions/latest/dg/tutorial-cloudwatch-events-s3.html)

* 创建input S3 和 log S3
* AWS CloudTrail 中创建跟踪
* CloudWatch Event 规则

```json
{
  "source": [
    "aws.s3"
  ],
  "detail-type": [
    "AWS API Call via CloudTrail"
  ],
  "detail": {
    "eventSource": [
      "s3.amazonaws.com"
    ],
    "eventName": [
      "PutObject"
    ],
    "requestParameters": {
      "bucketName": [
        "mcsimu-input"
      ]
    }
  }
}
```

## RDS

### 对公网隐藏RDS入口

思路：

* 通过控制组管理入站流量
* 区分私有/公有子网

## k8s


