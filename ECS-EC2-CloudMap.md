### ECS - EC2 - Cloud Map

```mermaid
flowchart LR
    subgraph ECS["ECS - Elastic Container Service"]
        Service(Services)
        Cluster(Cluster)
        Task(Task)
        subgraph ECR["ECR - Elastic Container Registry"]
            Repository(Repository)
            Image(Images)
            Repository -- has --> Image
        end
        subgraph LaunchType[Launch Type]
            Fargate(Fargate)
            EC2a(EC2)
        end

        Cluster --> |groups| Service
        Task -- pulls image from --> ECR
        Service --using--> LaunchType
        LaunchType -->|runs instances of a| Task
    end

    subgraph Storage["Elastic File System"]
        EFS(EFS)
    end

    subgraph EC2["EC2 Components"]
        subgraph LoadBalancing["Load Balancing Components"]
            ALB(Application Load Balancer)
            TG(Target Group)
            L(Listener)
            LR(Listener Rule)

            ALB -- has --> L
            LR -- for --> L
            LR -- routes to --> TG
            TG -- has --> HealthCheck
        end
        subgraph Network["Network and Security"]
            SG(Security Group)
            %%SG -- has --> Inbound
            %%SG -- has --> Outbound
        end
        ALB -- belongs to --> SG
    end

    subgraph CloudMap["Cloud Map - Service Discovery"]
        CMN(Namespace)
        CMS(Service)
        CMI(Service Instance)
        CMN --> CMS
        CMS --> CMI
    end

    subgraph Route53["Route 53"]
        HostedZone(Hosted Zone)
        Record(Record)
        HostedZone --> Record
    end

    %% Cross-subgraph connections
    Task --> |registers to| CMS
    Task --> |registers to| TG
    Task --> |mounts| EFS
    Task --> |belongs to| SG

    CMN -- creates --> HostedZone
    CMI -- creates --> Record
```
