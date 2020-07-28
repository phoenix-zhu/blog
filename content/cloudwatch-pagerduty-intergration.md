---
title: "CloudWatch和Pagerduty集成的报警名字问题"
date: 2020-07-28T10:07:33+08:00
lastmod: 2020-07-28T10:07:33+08:00
description: ""
tags: ["CloudWatch", "Pagerduty", "报警"]
categories: ["tech", "cloud"]
chronologies: ["2020"]
author: ""
---

CloudWatch和Pagerduty集成是一个非常常见的操作，但是默认情况下，两者集成之后CloudWatch发送给Pagerduty的消息大概长这样。
```
{
  "client": "AWS Console",
  "client_url": "https://console.aws.amazon.com/cloudwatch/home?region=ap-southeast-1#c=CloudWatch&s=Alarms&alarm=Response Time Abnormal",
  "description": "GreaterThanUpperThreshold",
  "event_type": "trigger",
  "incident_key": "Response Time Abnormal",
  "service_key": "08cfd976xxxxxxxd03b2753",
  "details": {
    "Trigger": {
      ...
      "ComparisonOperator": "GreaterThanUpperThreshold"
    },
    "OldStateValue": "OK",
    "NewStateValue": "ALARM",
    "NewStateReason": "Thresholds Crossed: 5 out of the last 5 datapoints were greater than the upper thresholds. ....",
    "AlarmName": "Response Time Abnormal",
    "AlarmDescription": "Response Time higher than usual",
    "AlarmArn": "arn:aws:cloudwatch:ap-southeast-1:123456789:alarm:Response Time Abnormal",
    "AWSAccountId": "123456789"
  }
}
```
按照Pagerduty的定义，它会把`description`里面的内容当作报警的描述，收到这样的报警消息，这我根本不知道发生了什么好吧？

考虑再三之后，我还是向AWS发了一个技术支持的工单，友善的询问他们为什么description选用的是ComparisonOperator这么一个并没有太多意义的内容，是否可以修改成其他更有意义的内容。然后回复工单的小哥很擅长的打了一个太极，告诉我说Pagerduty的网站上有一个关于[如何处理CloudWatch重复消息的方法](https://support.pagerduty.com/docs/aws-cloudwatch-integration-guide#how-can-i-change-how-events-from-cloudwatch-are-deduplicated-into-pagerduty)。

我……

虽然这个回复根本不是我想要的，但是我还是怀着害死猫的心情点开了这个链接，去了里面说的地方，结果竟然让我发现了意想不到的东西，上面文档里面说的那个“Correlate events by”下面还有一个选项叫做“Derive name from”。哦，这个有点意思的东西里面除了“Default”之外还有“AlarmName”和“AlarmDescription”可以选择。赶紧测试了一下，终于解决了长久以来困扰我的问题。

猛然间，我觉得AWS的技术支持小哥用心良苦，他是要把找到最终答案这样有意义和激励性的事情留给我亲自去完成，而不是直接告诉我，这才是真正的良师啊。于是，我回邮件告诉了他真正的解决方式。
