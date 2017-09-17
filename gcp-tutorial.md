# GCP-tutorial

現状の私のステータス

- さいきんやっとこAWS分かってきはじめて油断していた
- とあるところで移行始まってて、もはやGCP当たり前と聞きショックを受ける
- 勉強開始、基礎知識なし、本はさっき買った
- Redisもkvsってことだけ知ってる程度で知識なし
- DockerはDockerfileを数珠繋ぎして起動まではわかる、Docker自体のメリットは1%も理解できてない
- kubernetesをk8sと略しているのを見た　かっこいいので踏襲する

## REDIS

### redis-master

まずredis-masterについてのいろいろをやってる<br>
ディレクトリ見ると、<br>
xxxx-controller.yml<br>
xxxx-service.yml<br>
というのが組になっているんだなという具合<br>
なんとなーく、controllerが起動の設定、serviceが起動された何か(pod?)の設定、ってかんじに見える

```
$ kubectl create -f redis-master-service.yaml
service "redis-master" created

$ kubectl create -f redis-master-controller.yaml
replicationcontroller "redis-master" created
```

空打ちしたらヘルプ出てきたんで貼っとく
```
Examples:
  # Create a pod using the data in pod.json.
  kubectl create -f ./pod.json
  
  # Create a pod based on the JSON passed into stdin.
  cat pod.json | kubectl create -f -
  
  # Edit the data in docker-registry.yaml in JSON using the v1 API format then create the resource using the edited
data.
  kubectl create -f docker-registry.yaml --edit --output-version=v1 -o json
```

```
$ kubectl get pods
NAME                 READY     STATUS    RESTARTS   AGE
redis-master-qz5h9   1/1       Running   0          1m
```

いまつくったredis-masterのことかな

```
$ kubectl get rc
NAME           DESIRED   CURRENT   READY     AGE
redis-master   1         1         1         2m
```

```
$ kubectl get services
NAME           CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
kubernetes     10.15.240.1    <none>        443/TCP    30m
redis-master   10.15.254.75   <none>        6379/TCP   3m
```

kubernetesが、コンソール繋いで叩いてるとこなのかも

### redis-slave

スレーブも作る

```
$ kubectl create -f all-in-one/redis-slave.yaml
service "redis-slave" created
replicationcontroller "redis-slave" created
```

ポッドが増えた<br>
ポッドというのはk8sにとっての単位のことのようだ

```
$ kubectl get pods
NAME                 READY     STATUS    RESTARTS   AGE
redis-master-qz5h9   1/1       Running   0          4m
redis-slave-l8lb4    1/1       Running   0          45s
redis-slave-sb4bs    1/1       Running   0          45s
```

あれslaveが2個ある<br>
redis-slave-controller.yamlに、<br>
replicas: 2<br>
という記述があったので、あれを買えると何台も出てくるんだろう

```
kubectl get rc
NAME           DESIRED   CURRENT   READY     AGE
redis-master   1         1         1         7m
redis-slave    2         2         2         3m
```

こっちは二行で終わりだ　podとrcでは数える単位が違うのだな
rcは設定の数なのかな、グループとか

* replicationcontrollers (aka 'rc') 
* pods (aka 'po')  

だと

```
コマンドの例があった
Examples:
  # List all pods in ps output format.
  kubectl get pods
  
  # List all pods in ps output format with more information (such as node name).
  kubectl get pods -o wide
  
  # List a single replication controller with specified NAME in ps output format.
  kubectl get replicationcontroller web
  
  # List a single pod in JSON output format.
  kubectl get -o json pod web-pod-13je7
  
  # List a pod identified by type and name specified in "pod.yaml" in JSON output format.
  kubectl get -f pod.yaml -o json
  
  # Return only the phase value of the specified pod.
  kubectl get -o template pod/web-pod-13je7 --template={{.status.phase}}
  
  # List all replication controllers and services together in ps output format.
  kubectl get rc,services
  
  # List one or more resources by their type and names.
  kubectl get rc/web service/frontend pods/web-pod-13je7
  
  # List all resources with different types.
  kubectl get all
```

```
$ kubectl get services
NAME           CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes     10.15.240.1     <none>        443/TCP    40m
redis-master   10.15.254.75    <none>        6379/TCP   13m
redis-slave    10.15.253.134   <none>        6379/TCP   9m
```

バラバラでもいいけど、まとめて打てる　こっちのがいいね
```
$ kubectl get pods,rc,services
```

## ゲストブック フロントエンドのデプロイ




