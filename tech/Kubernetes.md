
# Компоненты

## Кластер `Cluster`, узлы `Nodes`, модули `Pods`
При использовании Kubernetes,вы создаёте **Кластер** (англ. `Cluster`).

*Кластер* состоит из *множества* **Узлов** (англ. `Nodes`), *каждый* из которых *является рабочей машиной* (англ. `worker machine`), *запускающей контейнерные приложения* (англ. `containerized applications`).

На рабочих *Узлах размещаются* **Модули** (англ. `Pods`), являющиеся *компонентами приложения*, компонентами Workload.  
**Workload** - это приложение, запущенное в Kubernetes.

**Контрольный план** (англ. `Control plane`)
> The container orchestration layer that exposes the API and interfaces to define, deploy, and manage the lifecycle of containers.




## Ingress
