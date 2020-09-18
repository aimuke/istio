# delete k8s namespace

## kubernetes无法删除namespace 提示 Terminating <a id="articleContentId"></a>

最近配置traefik时，想重新部署jenkins，出现如下问题

```text
]# ./kubectl get namespaces --kubeconfig ./conf/kubeconfig
NAME              STATUS        AGE
default           Active        27h
istio-system      Terminating   20h
kube-node-lease   Active        27h
kube-public       Active        27h
kube-system       Active        27h

```

解决方法如下：

```text
# kubectl get namespace istio-system -o json > tmp.json
```

先运行kubectl get namespace istio-system -o json &gt; tmp.json，拿到当前namespace描述，然后打开tmp.json，删除其中的spec字段。

最后运行：

```text
]# curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8080/api/v1/namespaces/istio-system/finalize
{
  "kind": "Namespace",
  "apiVersion": "v1",
  "metadata": {
    "name": "istio-system",
    "selfLink": "/api/v1/namespaces/istio-system/finalize",
    "uid": "713e2935-41eb-4e8f-be52-3b43a7536b4f",
    "resourceVersion": "29523",
    "creationTimestamp": "2020-09-08T11:18:34Z",
    "deletionTimestamp": "2020-09-09T07:18:05Z",
    "managedFields": [
      {
        "manager": "kubectl",
        "operation": "Update",
        "apiVersion": "v1",
        "time": "2020-09-08T11:18:34Z",
        "fieldsType": "FieldsV1",
        "fieldsV1": {"f:status":{"f:phase":{}}}
      }
    ]
  },
  "spec": {

  },
  "status": {
    "phase": "Terminating"
  }
}
```

至此，命名空间已经删除

