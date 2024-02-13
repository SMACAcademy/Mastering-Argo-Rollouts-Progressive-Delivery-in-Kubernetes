This demo application is a simple demonstration of the the Argo-Rollout with Canary stratregy. It uses the upstream simple nginx docker image (with different tags) for this purpose. 

# Prerequisites.

1. Argo-Rollouts should be deployed into your cluster.
2. kubectl cli installed on your machine.
3. helm 3 must be installed on your local machine.
4. git cli for cloning the repository.


**Steps :**

1. Clone this repository.
2. From your repository root, run the following command.  

 `kubectl create ns ic-demo`

`helm install demo-nginx -n ic-demo ./ic-demo`

This will create all the necessary k8s objects in `ic-demo` namespace.

3. Verify the deployment status by running the following.  

`helm list -n ic-demo`

4. Now, you can check the status of your rollouts using the following command. The `--watch` argument will give you the dynamic view of the rollout object. Keep this terminal opened in a seperate tab, we will need it for the remaining steps.  

`kubectl argo rollouts get rollout demo-nginx -n ic-demo --watch`

The `demo-nginx` name is the rollout object name, not the helm package name. Don't get confused.

5. If you look at the above command output, you could see the rollout object has been created with the given replicas (1). Since this is the first revision, there will not be any canary replicaset. Now you can update the image tag in `values.yaml` and run a `helm upgrade` to update the rollout object.

`helm upgrade demo-nginx -n ic-demo ./ic-demo`

Now watch the rollout object status which is previously opend in the other terminal.

`kubectl argo rollouts get rollout demo-nginx -n ic-demo --watch`

You should be able to see something like below. Check the stable and canary sections below. A new replica set has been created for the new canary pods.  

```
Name:            demo-nginx
Namespace:       ic-demo
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          1/2
  SetWeight:     5
  ActualWeight:  5
Images:          nginx:1.20.0 (stable)
                 nginx:latest (canary)
Replicas:
  Desired:       1
  Current:       2
  Updated:       1
  Ready:         2
  Available:     2

NAME                                    KIND        STATUS     AGE    INFO
⟳ demo-nginx                            Rollout     ॥ Paused   56m    
├──# revision:2                                                       
│  └──⧉ demo-nginx-69567c6bc8           ReplicaSet  ✔ Healthy  2m31s  canary
│     └──□ demo-nginx-69567c6bc8-65q82  Pod         ✔ Running  2m31s  ready:1/1
└──# revision:1                                                       
   └──⧉ demo-nginx-8544f9dbc8           ReplicaSet  ✔ Healthy  56m    stable
      └──□ demo-nginx-8544f9dbc8-hfjjh  Pod         ✔ Running  56m    ready:1/1
```

6. Verify the https requests lands only on the canary replicaset at this time, not on the stable one. For that you can tail both the above container logs using.

  `kubectl logs -f <pod-name> -n ic-demo`

7. Now access the app url using the exposed ingress url by passing the custom header values. 

`curl -H "canary: yep" -IL http://<your-host-name>/helloe/jsbdsdsd.html`

8. Now, you need to promote the rollout object after manual verification. 

`kubectl argo rollouts promote demo-nginx -n ic-demo`