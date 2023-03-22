
# Компоненты

## Кластер `Cluster`, узлы `Nodes`, модули `Pods`, `Workload`
Для начала работы с Kubernetes необходимо создать *Кластер*.

**Кластер** (англ. `Cluster`) представляет собой *множество* *Узлов*.

Каждый **Узел** *представляет собой рабочую машину* (англ. `worker machine`), являющуюся *частью Кластера* и *запускающую контейнерные приложения* (англ. `containerized applications`). *Узлы* в *Кластере* *не являются частью публичного интернета*, то есть они *не общедоступны*.

Cluster: A set of Nodes that run containerized applications managed by Kubernetes. For this example, and in most common Kubernetes deployments, nodes in the cluster are not part of the public internet.

*Кластер* состоит из *множества* **Узлов** (англ. `Nodes`), *каждый* из которых 

На рабочих *Узлах размещаются* **Модули** (англ. `Pods`), являющиеся *компонентами приложения*, компонентами Workload.  
**Workload** (рус. `рабочая нагрузка`) - это приложение, запущенное в Kubernetes.

## Контрольный план

**Контрольный план** (англ. `Control plane`)
> The container orchestration layer that exposes the API and interfaces to define, deploy, and manage the lifecycle of containers.




## Ingress
