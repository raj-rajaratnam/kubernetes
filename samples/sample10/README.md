## Sample10: Ballerina package with kubernetes annotations

- This sample runs [foodstore](../sample3) as a package.   
- Following files will be generated from this sample.
    ``` 
    $> docker image
    pizza: latest 
    burger: latest  
    
    $> tree
      └── target
          ├── Ballerina.lock
          ├── burger.balx
          ├── kubernetes
          │   ├── burger
          │   │   ├── burger_deployment.yaml
          │   │   ├── burger_ingress.yaml
          │   │   ├── burger_secret.yaml
          │   │   ├── burger_svc.yaml
          │   │   └── docker
          │   │       └── Dockerfile
          │   └── pizza
          │       ├── docker
          │       │   └── Dockerfile
          │       ├── pizza_deployment.yaml
          │       ├── pizza_ingress.yaml
          │       └── pizza_svc.yaml
          └── pizza.balx
  
    ```
### How to run:

1. Initialize ballerina project.
```bash
sample10$> ballerina init
Ballerina project initialized
```

1. Compile the  food_api_pkg file. Command to run kubernetes artifacts will be printed on success:
```bash
$> ballerina build 
@kubernetes:Service                      - complete 1/1
@kubernetes:Ingress                      - complete 1/1
@kubernetes:Secret                       - complete 1/1
@kubernetes:Deployment                   - complete 1/1
@kubernetes:Docker                       - complete 3/3 

Run following command to deploy kubernetes artifacts: 
kubectl apply -f /Users/anuruddha/workspace/ballerinax/kubernetes/samples/sample10/target/kubernetes/burger

@kubernetes:Service                      - complete 1/1
@kubernetes:Ingress                      - complete 1/1
@kubernetes:Deployment                   - complete 1/1
@kubernetes:Docker                       - complete 3/3 

Run following command to deploy kubernetes artifacts: 
kubectl apply -f /Users/anuruddha/workspace/ballerinax/kubernetes/samples/sample10/target/kubernetes/pizza
```

2. food_api_pkg.balx, Dockerfile, docker image and kubernetes artifacts will be generated: 
```bash
$> tree
.
└── target
  ├── Ballerina.lock
  ├── burger.balx
  ├── kubernetes
  │   ├── burger
  │   │   ├── burger_deployment.yaml
  │   │   ├── burger_ingress.yaml
  │   │   ├── burger_secret.yaml
  │   │   ├── burger_svc.yaml
  │   │   └── docker
  │   │       └── Dockerfile
  │   └── pizza
  │       ├── docker
  │       │   └── Dockerfile
  │       ├── pizza_deployment.yaml
  │       ├── pizza_ingress.yaml
  │       └── pizza_svc.yaml
  └── pizza.balx
  

```

3. Verify the docker image is created:
```bash
$> docker images
REPOSITORY       TAG                 IMAGE ID            CREATED             SIZE
pizza            latest              7eb49de027a7        12 minutes ago      135MB
burger           latest              7b8bec8eedc6        13 minutes ago      135MB
```

5. Run kubectl command to deploy artifacts (Use the command printed on screen in step 1):
```bash
$ kubectl apply -f /Users/anuruddha/workspace/ballerinax/kubernetes/samples/sample10/target/kubernetes/pizza/
deployment.extensions "foodstore" created
ingress.extensions "pizzaep-ingress" created
service "pizzaep-svc" created

$ kubectl apply -f /Users/anuruddha/workspace/ballerinax/kubernetes/samples/sample10/target/kubernetes/burger
deployment.extensions "burger-deployment" created
ingress.extensions "burgerep-ingress" created
```

6. Verify kubernetes deployment,service,secrets and ingress is deployed:
```bash
$> kubectl get pods
NAME                       READY     STATUS    RESTARTS   AGE
burger-deployment-85448f5797-mspzc   1/1       Running   0          4m
foodstore-7f779cdc9c-65wpr           1/1       Running   0          4m
foodstore-7f779cdc9c-lb54g           1/1       Running   0          4m
foodstore-7f779cdc9c-nhcl2           1/1       Running   0          4m


$> kubectl get svc
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
burgerep-svc   ClusterIP   10.107.127.86   <none>        9096/TCP   45s
pizzaep-svc    ClusterIP   10.96.214.133   <none>        9099/TCP   45s

$> kubectl get ingress
NAME                HOSTS        ADDRESS   PORTS     AGE
burgerapi-ingress   burger.com             80, 443   1m
pizzaapi-ingress    pizza.com              80, 443   1m

```

7. Access the hello world service with curl command:

- **Using ingress:**
Add /etc/hosts entry to match hostname. 
_(127.0.0.1 is only applicable to docker for mac users. Other users should map the hostname with correct ip address 
from `kubectl get ingress` command.)_

```bash
$> curl http://pizza.com/pizzastore/pizza/menu
Pizza menu

$>curl https://burger.com/menu -k
Burger menu
```

8. Undeploy sample:
```bash
$> kubectl delete -f target/kubernetes/pizza
$> kubectl delete -f target/kubernetes/burger

```
