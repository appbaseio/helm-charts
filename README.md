# helm-charts

Helm charts for appbase.io, an API gateway for a supercharged Elasticsearch experience.
Via this Helm Chart you can localize Appbaseio on your kubernetes engine.

## Requirements
You should have your **Kubernetes** cluster installed and configured and then you should install [helm]("https://helm.sh/docs/intro/install/")

As Arc is an API gateway for your Elasticsearch, make sure that you already have an Elasticsearch cluster with it's basic credentials.

If you don't have an Elasticsearch cluster, you can use this [guide]("https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html")

## Why Helm Charts:

Helm helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.

Charts are easy to create, version, share, and publish — so start using Helm and stop the copy-and-paste.

Here we get benefit of Helm Charts to package Arc (which is an API Gateway that sits between a client and an ElasticSearch cluster) and install it in seconds.

## How to install Appbaseio Helm Chart

1. Run `helm repo add appbase https://opensource.appbase.io/helm-charts/`

2. Run `helm repo update`

3. Run `helm install appbaseio appbase/appbaseio  --set <variables>`

Make sure that you set below variables which are mandatory:

- `elasticsearch.scheme`, `elasticsearch.username`, `elasticsearch.password`, `elasticsearch.host` allow configuring the upstream Elasticsearch Cluster's values.

- `appbase.id` is the `APPBASE_ID` (subscription key) that is obtained via https://dash.appbase.io/install

- `appbase.username`, `appbase.password` allows configuring the Basic Auth HTTP for the appbase.io gateway. Default values are `admin` for both.


## Configure the cluster with Values

According to Helm chart [values]("https://helm.sh/docs/chart_template_guide/values_files/"), you can customize the cluster in the way you want by set variables during the install.
We categorized variables in order to ease it's readability, for example `elasticsearch.clusterURL`  means clusterURL is a subset of elasticsearch but while setting a vaiable we should follo it's indentation. 
Here are the variables you can set for your cluster:
|  Name |Default Value   | Kind  |  Description |
|---|---|---|---|
| elasticsearch.scheme  | "http"  | String  |  Supports: `http` (default) or `https` as valid values. If you set this variable to `https`, it tries to create a secure connection to your elasticsearch cluster but you should have configured your TLS certificate. |
| elasticsearch.port  | 9200  | Integer  |  This is the port of your elasticsearch cluster, if you already configured TLS certificate and set scheme as `https`, you should set this to use `443` |
| elasticsearch.username  | ""  | String  |  This is your elasticsearch cluster's username, this variable is mandatory if you have authentication setup on your elasticsearch cluster |
| elasticsearch.password  | ""  | String  |  This is your elasticsearch cluster's username, this variable is mandatory if you have authentication setup on your elasticsearch cluster |
| elasticsearch.host  | "0.0.0.0"  | String  |  This is your elasticsearch domain/IP which shouldn't be filled with any kind of prefixes, also if your elasticsearch is in your kubernetes cluster, you can use its service name here|
|  appbase.name | arc  |  String | This is the name of the Arc service (appbase.io API gateway for elasticsearch) which you can use to access your application via service name, there will be a kubernetes service with this name in default namespace  |
| appbase.image |  appbaseio/arc | String  |  This is the image Appbase.io provides as gateway for your elasticsearch, if you have your local repository, you can push Arc image into that then change the URL here. |
| appbase.tag |  "" | String  | This is Arc image tag which is currently set to a stable tag but if you want to use a specific image, can mention by setting this variable |
| appbase.port | 8000  | Integer  | The port that used for Arc service |
| appbase.id  |  "" |  String |  This is **APPBASE_ID** that you can get from [Appbase.io]("https://arc-dashboard.appbase.io/install") |
| appbase.username | admin  |  String |  This is the username you choose for your Appbaseio |
| appbase.password  | admin  |  String | This is the password you choose for your Appbaseio  |
| appbase.domain |  "" |  String |  If you are installing helm chart on your production and want to assigne a domain to it, set this variable to your domain, make sure that your loadBalancer.serviceType to be empty ("") |
| cert.name | ""  | String  | You can add your certificate here by configuring below values. name is the name of secret file containing your certificate information, if you have your own secret file, you can only fill the name value and leave the other empty  |
| cert.tlsCrt  |  "" | String  | "tlsCrt" is your "tls.crt". It should be passed as a base64 encoded |
| cert.tlsKey | ""  | String  | "tlsKey" is "tls.key". It should be passed as a base64 encoded  |
| loadBalancer.serviceType  | ""  | String  | If you're using Kubernetes locally and as you won't have external IP, Can be "NodePort" but if it's your production kubernetes, you can leave it empty which means serviceType is : "LoadBalancer" |

**Tips:**

- Some variables might be long to use in install command, so you can export it and then use it e.g.
    
     `export $(ES_ClusterURL=<your elasticsearch URL with basec authentication>)`

     then you can use it while installing helm:

     `helm install appbase --set elasticsearch.clusterURL=ES_ClusterURL`

## Kubernetes Distribution support:
You can check [this page]("https://helm.sh/docs/topics/kubernetes_distros/") to see what distros Helm is currently supporting
## Test on Minikube

1. Make sure your Minikube is installed, if not, use [this]("https://minikube.sigs.k8s.io/docs/start/") and if you don't have kubectl installed, use this [link]("https://kubernetes.io/docs/tasks/tools/")

2. Add Appbase helm repo and install it as it's said 

    Make sure that you set loadBalancer.serviceType=NodePort

3. After you install helm chart setting loadBalancer.serviceType=NodePort, you will see this result:
![image](https://user-images.githubusercontent.com/30385958/122102140-5bdb9e80-ce2a-11eb-960b-921c64a298e5.png)

Which you can use the command to get access to your Appbaseio service

    If you change arc.name, the command will be: minikube service --url <arc name>-nodeport

## How to use it by cloning the project

1. Clone this repositoy: 

`git clone git@github.com:appbaseio/helm-charts.git` 

and then head to `helm-charts` folder.


2. Now you should config helm chart to connect appbaseio to your elasticsearch: 

open **values.yaml** from `helm-charts/appbaseio/values.yaml` and fill the values you might need.
These are required values you must fill: 

- **Elasticsearch Domain**: This can be your Kubernetes service name or a valid domain which is reachble from your kubernetese cluster
- **Elasticsearch Username**: by default this username is `elastic` but if you have changed your ES_USERNAME, should change it here
- **Elasticsearch Password**: this is your decrypted password which you can leave empty here as we can change it while installing our helm chart. we will see that in following.
- **APPBASE_ID**: to get this, head to [Appbase]("https://arc-dashboard.appbase.io/install") and enter your email, You will receive an OTP on an entered email address. Enter OTP to verify the email address and then `APPBASE_ID` will be sent to our email.

- **APPBASE_USERNAME**: This will be used while using appbaseio so you can choose your desired username by filling the variable
- **APPBASE_PASSWORD**: This will be used while using appbaseio so you can choose your desired password by filling the variable

you can leave other settings as their default value but if you want to specify other settings like using TLS certificate for your Appbase cluster and so on, modify the related part.

**Note:**
- handle arc version in Chart.yaml as **appVersion**

3. We can set above values while running helm chart install command, the value will be replaced with what we have in values.yaml for example here we install appbase chart with setting the ES_PASSWORD 

- If you have a Kubernetes cluster, run below command :

`PASSWORD=$(kubectl get secret <your elasticsearch service name> -o go-template='{{.data.elastic | base64decode}}')`
    
    This Command will get ES_PASSWORD from ES secret, make sure to change your elasticsearch service name

Now it's time to install our chart: 

`helm install appbaseio appbaseio/ --values appbaseio/values.yaml --set elasticsearch.esPassword=$PASSWORD `

To get more information about set value in helm install, take a look at this [page]("https://helm.sh/docs/helm/helm_install/")

4. Wait until your pods are in "Running" stat (see their status by `kubectl get pods`) and the LoadBalancer service is up and have an external IP (use this command: `kubectl get svc --namespace ingress-nginx`) 

When everything is OK you can access your Appbase with LoadBalancer IP and it's Port then you can go to [Arc dashboard]("https://arc-dashboard.appbase.io/login") to handle your elasticsearch visually
