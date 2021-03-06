## advanced example

Use Deployment to install shell-operator. with several hooks.


### Run 

Build shell-operator image with custom scripts:

```
$ docker build -t "registry.mycompany.com/shell-operator:advanced" .
$ docker push registry.mycompany.com/shell-operator:advanced
```

Edit image in shell-operator-pod.yaml and apply manifests:

```
$ kubectl create ns example-advanced
$ kubectl -n example-advanced apply -f shell-operator-rbac.yaml  
$ kubectl -n example-advanced apply -f shell-operator-deploy.yaml
```

Create ns to trigger onKubernetesEvent:

```
$ kubectl create ns foobar
```

See in logs that startup hooks are run in order and all other hooks are triggered:

```
$ kubectl -n example-advanced logs deploy/shell-operator
...
2019-04-05T12:10:33Z INFO     : TASK_RUN HookRun@ON_STARTUP namespace-hook.sh
007-onstartup-2 hook is triggered
...
2019-04-05T12:10:33Z INFO     : TASK_RUN HookRun@ON_STARTUP namespace-hook.sh
namespace-hook is triggered on startup.
...
2019-04-05T12:10:33Z INFO     : TASK_RUN HookRun@ON_STARTUP 001-onstartup-10/shell-hook.sh
001-onstartup-10 hook is triggered
...
2019-04-05T12:10:36Z INFO     : TASK_RUN HookRun@SCHEDULE 003-schedule/schedule-hook.sh
Message from Schedule hook!
...
2019-04-05T12:14:52Z INFO     : TASK_RUN HookRun@KUBE_EVENTS namespace-hook.sh
Namespace qweqwe was created
...
```

### cleanup

```
$ kubectl delete ns/example-advanced
$ docker rmi registry.mycompany.com/shell-operator:advanced
```