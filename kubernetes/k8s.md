# k8s必备神器client-go

## 简介

client-go是k8s官方出品的一项操作k8s的sdk包括kubectl与kubernetes API
使得外部程序跟k8s进行通信,最常用的是kubectl 官方UI以及kube Dashboard

## 三类client
client library主要包括三种：

- clientset 
- DynamicClient
- RestClient

Clientset 
Clientset 是我们最常用的 client，你可以在它里面找到 kubernetes 目前所有原生资源对应的 client。 获取方式一般是，指定 group 然后指定特定的 version，然后根据 resource 名字来获取到对应的 client。操作原生Pods，Nodes，Deployments等

Dynamic Client 
Dynamic client 是一种动态的 client，它能同时处理 kubernetes 所有的资源。并且同时，它也不同于 clientset，dynamic client 返回的对象是一个 map[string]interface{}，如果一个 controller 中需要控制所有的 API，可以使用dynamic client，目前它被用在了 garbage collector 和 namespace controller。
Dynamic Client 目前只支持 JSON 的序列化和反序列化。

RESTClient 
RESTClient 是 clientset 和 dynamic client 的基础，前面这两个 client 本质上都是 RESTClient，它提供了一些 RESTful 的函数如 Get()，Put()，Post()，Delete()。由 Codec 来提供序列化和反序列化的功能。

续...