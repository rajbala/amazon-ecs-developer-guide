# Service definition parameters<a name="service_definition_parameters"></a>

A service definition defines how to run your Amazon ECS service\. The following parameters can be specified in a service definition\.

## Launch type<a name="sd-launchtype"></a>

`launchType`  
Type: String  
Valid values: `EC2` \| `FARGATE` \| `EXTERNAL`  
Required: No  
The launch type on which to run your service\. If a launch type is not specified, the default `capacityProviderStrategy` is used by default\. For more information, see [Amazon ECS launch types](launch_types.md)\.  
If a `launchType` is specified, the `capacityProviderStrategy` parameter must be omitted\.

## Capacity provider strategy<a name="sd-capacityproviderstrategy"></a>

`capacityProviderStrategy`  
Type: Array of objects  
Required: No  
The capacity provider strategy to use for the service\.  
A capacity provider strategy consists of one or more capacity providers along with the `base` and `weight` to assign to them\. A capacity provider must be associated with the cluster to be used in a capacity provider strategy\. The PutClusterCapacityProviders API is used to associate a capacity provider with a cluster\. Only capacity providers with an `ACTIVE` or `UPDATING` status can be used\.  
If a `capacityProviderStrategy` is specified, the `launchType` parameter must be omitted\. If no `capacityProviderStrategy` or `launchType` is specified, the `defaultCapacityProviderStrategy` for the cluster is used\.  
If you want to specify a capacity provider that uses an Auto Scaling group, the capacity provider must already be created\. New capacity providers can be created with the CreateCapacityProvider API operation\.  
To use an AWS Fargate capacity provider, specify either the `FARGATE` or `FARGATE_SPOT` capacity providers\. The AWS Fargate capacity providers are available to all accounts and only need to be associated with a cluster to be used\.  
The PutClusterCapacityProviders API operation is used to update the list of available capacity providers for a cluster after the cluster is created\.    
`capacityProvider`  <a name="capacityProvider"></a>
Type: String  
Required: Yes  
The short name or full Amazon Resource Name \(ARN\) of the capacity provider\.  
`weight`  <a name="weight"></a>
Type: Integer  
Valid range: Integers between 0 and 1,000\.  
Required: No  
The weight value designates the relative percentage of the total number of tasks launched that use the specified capacity provider\.  
For example, assume that you have a strategy that contains two capacity providers and both have a weight of one\. When the base is satisfied, the tasks split evenly across the two capacity providers\. Using that same logic, assume that you specify a weight of 1 for *capacityProviderA* and a weight of 4 for *capacityProviderB*\. Then, for every one task that is run using *capacityProviderA*, four tasks use *capacityProviderB*\.  
`base`  <a name="base"></a>
Type: Integer  
Valid range: Integers between 0 and 100,000\.  
Required: No  
The base value designates how many tasks, at a minimum, to run on the specified capacity provider\. Only one capacity provider in a capacity provider strategy can have a base defined\.

## Task definition<a name="sd-taskdefinition"></a>

`taskDefinition`  
Type: String  
Required: No  
The `family` and `revision` \(`family:revision`\) or full Amazon Resource Name \(ARN\) of the task definition to run in your service\. If a `revision` isn't specified, the latest `ACTIVE` revision of the specified family is used\.  
A task definition must be specified when using the rolling update \(`ECS`\) deployment controller\.

## Platform operating system<a name="platform-os"></a>

`platformFamily`  
Type: string  
Required: Conditional  
Default: Linux  
This parameter is required for Amazon ECS services hosted on Fargate\.  
This parameter is ignored for Amazon ECS services hosted onAmazon EC2\.  
The operating system on the containers that runs the service\. The valid values are `LINUX`, `WINDOWS_SERVER_2019_FULL`, `WINDOWS_SERVER_2019_CORE`, `WINDOWS_SERVER_2022_FULL`, and `WINDOWS_SERVER_2022_CORE`\.  
The `platformFamily` value for every task that you specify for the service must match the service `platformFamily` value\. For example, if you set the `platformFamily` to `WINDOWS_SERVER_2019_FULL`, the `platformFamily` value for all the tasks must be `WINDOWS_SERVER_2019_FULL`\.

## Platform version<a name="sd-platformversion"></a>

`platformVersion`  
Type: String  
Required: No  
The platform version on which your tasks in the service are running\. A platform version is only specified for tasks using the Fargate launch type\. If one is not specified, the latest version \(`LATEST`\) is used by default\.  
AWS Fargate platform versions are used to refer to a specific runtime environment for the Fargate task infrastructure\. When specifying the `LATEST` platform version when running a task or creating a service, you get the most current platform version available for your tasks\. When you scale up your service, those tasks receive the platform version that was specified on the service's current deployment\. For more information, see [AWS Fargate platform versions](platform_versions.md)\.  
Platform versions are not specified for tasks using the EC2 launch type\.

## Cluster<a name="sd-cluster"></a>

`cluster`  
Type: String  
Required: No  
The short name or full Amazon Resource Name \(ARN\) of the cluster on which to run your service\. If you do not specify a cluster, the `default` cluster is assumed\.

## Service name<a name="sd-servicename"></a>

`serviceName`  
Type: String  
Required: Yes  
The name of your service\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\. Service names must be unique within a cluster, but you can have similarly named services in multiple clusters within a Region or across multiple Regions\.

## Scheduling strategy<a name="sd-schedulingstrategy"></a>

`schedulingStrategy`  
Type: String  
Valid values: `REPLICA` \| `DAEMON`  
Required: No  
The scheduling strategy to use\. If no scheduling strategy is specified, the `REPLICA` strategy is used\. For more information, see [Service scheduler concepts](ecs_services.md#service_scheduler)\.  
There are two service scheduler strategies available:  
+ `REPLICA`—The replica scheduling strategy places and maintains the desired number of tasks across your cluster\. By default, the service scheduler spreads tasks across Availability Zones\. You can use task placement strategies and constraints to customize task placement decisions\. For more information, see [Replica](ecs_services.md#service_scheduler_replica)\.
+ `DAEMON`—The daemon scheduling strategy deploys exactly one task on each active container instance that meets all of the task placement constraints that you specify in your cluster\. The service scheduler evaluates the task placement constraints for running tasks and will stop tasks that do not meet the placement constraints\. When using this strategy, there is no need to specify a desired number of tasks, a task placement strategy, or use Service Auto Scaling policies\. For more information, see [Daemon](ecs_services.md#service_scheduler_daemon)\.
**Note**  
Fargate tasks do not support the `DAEMON` scheduling strategy\.

## Desired count<a name="sd-desiredcount"></a>

`desiredCount`  
Type: Integer  
Required: No  
The number of instantiations of the specified task definition to place and keep running on your cluster\.  
This parameter is required if the `REPLICA` scheduling strategy is used\. If the service uses the `DAEMON` scheduling strategy, this parameter is optional\.

## Deployment configuration<a name="sd-deploymentconfiguration"></a>

`deploymentConfiguration`  
Type: Object  
Required: No  
Optional deployment parameters that control how many tasks run during the deployment and the ordering of stopping and starting tasks\.    
`maximumPercent`  <a name="maximumPercent"></a>
Type: Integer  
Required: No  
If a service is using the rolling update \(`ECS`\) deployment type, the `maximumPercent` parameter represents an upper limit on the number of your service's tasks that are allowed in the `RUNNING`, `STOPPING`, or `PENDING` state during a deployment\. It is expressed as a percentage of the `desiredCount` that is rounded down to the nearest integer\. You can use this parameter to define the deployment batch size\. For example, if your service is using the `REPLICA` service scheduler and has a `desiredCount` of four tasks and a `maximumPercent` value of 200%, the scheduler might start four new tasks before stopping the four older tasks\. This is provided that the cluster resources required to do this are available\. The default `maximumPercent` value for a service using the `REPLICA` service scheduler is 200%\.  
If your service is using the `DAEMON` service scheduler type, the `maximumPercent` should remain at 100%\. This is the default value\.  
The maximum number of tasks during a deployment is the `desiredCount` multiplied by the `maximumPercent`/100, rounded down to the nearest integer value\.  
If a service is using either the blue/green \(`CODE_DEPLOY`\) or `EXTERNAL` deployment types and tasks that use the EC2 launch type, the **maximum percent** value is set to the default value and is used to define the upper limit on the number of the tasks in the service that remain in the `RUNNING` state while the container instances are in the `DRAINING` state\. If the tasks in the service use the Fargate launch type, the maximum percent value isn't used, although it's returned when describing your service\.  
`minimumHealthyPercent`  <a name="minimumHealthyPercent"></a>
Type: Integer  
Required: No  
If a service is using the rolling update \(`ECS`\) deployment type, the `minimumHealthyPercent` represents a lower limit on the number of your service's tasks that must remain in the `RUNNING` state during a deployment\. This is expressed as a percentage of the `desiredCount` that is rounded up to the nearest integer\. You can use this parameter to deploy without using additional cluster capacity\. For example, if your service has a `desiredCount` of four tasks and a `minimumHealthyPercent` of 50%, the service scheduler might stop two existing tasks to free up cluster capacity before starting two new tasks\.   
For services that *do not* use a load balancer, consider the following:  
+ A service is considered healthy if all essential containers within the tasks in the service pass their health checks\.
+ If a task has no essential containers with a health check defined, the service scheduler waits for 40 seconds after a task reaches a `RUNNING` state before the task is counted towards the minimum healthy percent total\.
+ If a task has one or more essential containers with a health check defined, the service scheduler waits for the task to reach a healthy status before counting it towards the minimum healthy percent total\. A task is considered healthy when all essential containers within the task have passed their health checks\. The amount of time the service scheduler can wait for is determined by the container health check settings\. For more information, see [Health check](task_definition_parameters.md#container_definition_healthcheck)\. 
For services that *do* use a load balancer, consider the following:  
+ If a task has no essential containers with a health check defined, the service scheduler waits for the load balancer target group health check to return a healthy status before counting the task towards the minimum healthy percent total\.
+ If a task has an essential container with a health check defined, the service scheduler waits for both the task to reach a healthy status and the load balancer target group health check to return a healthy status before counting the task towards the minimum healthy percent total\.
The default value for a replica service for `minimumHealthyPercent` is 100%\. The default `minimumHealthyPercent` value for a service using the `DAEMON` service schedule is 0% for the AWS CLI, the AWS SDKs, and the APIs and 50% for the AWS Management Console\.  
The minimum number of healthy tasks during a deployment is the `desiredCount` multiplied by the `minimumHealthyPercent`/100, rounded up to the nearest integer value\.  
If a service is using either the blue/green \(`CODE_DEPLOY`\) or `EXTERNAL` deployment types and is running tasks that use the EC2 launch type, the **minimum healthy percent** value is set to the default value and is used to define the lower limit on the number of the tasks in the service that remain in the `RUNNING` state while the container instances are in the `DRAINING` state\. If a service is using either the blue/green \(`CODE_DEPLOY`\) or `EXTERNAL` deployment types and is running tasks that use the Fargate launch type, the minimum healthy percent value is not used, although it is returned when describing your service\.

## Deployment controller<a name="sd-deploymentcontroller"></a>

`deploymentController`  
Type: Object  
Required: No  
The deployment controller to use for the service\. If no deployment controller is specified, the `ECS` controller is used\. For more information, see [Amazon ECS Deployment types](deployment-types.md)\.    
`type`  
Type: String  
Valid values: `ECS` \| `CODE_DEPLOY` \| `EXTERNAL`  
Required: yes  
The deployment controller type to use\. There are three deployment controller types available:    
`ECS`  
The rolling update \(`ECS`\) deployment type involves replacing the current running version of the container with the latest version\. The number of containers Amazon ECS adds or removes from the service during a rolling update is controlled by adjusting the minimum and maximum number of healthy tasks allowed during a service deployment, as specified in the [deploymentConfiguration](#deploymentConfiguration)\.  
`CODE_DEPLOY`  
The blue/green \(`CODE_DEPLOY`\) deployment type uses the blue/green deployment model powered by CodeDeploy, which allows you to verify a new deployment of a service before sending production traffic to it\.  
`EXTERNAL`  
Use the external deployment type when you want to use any third\-party deployment controller for full control over the deployment process for an Amazon ECS service\.

## Task placement<a name="sd-taskplacement"></a>

`placementConstraints`  
Type: Array of objects  
Required: No  
An array of placement constraint objects to use for tasks in your service\. You can specify a maximum of 10 constraints per task\. This limit includes constraints in the task definition and those specified at run time\. If you use the Fargate launch type, task placement constraints aren't supported\.    
`type`  
Type: String  
Required: No  
The type of constraint\. Use `distinctInstance` to ensure that each task in a particular group is running on a different container instance\. Use `memberOf` to restrict the selection to a group of valid candidates\. The value `distinctInstance` is not supported in task definitions\.  
`expression`  
Type: String  
Required: No  
A cluster query language expression to apply to the constraint\. You can't specify an expression if the constraint type is `distinctInstance`\. For more information, see [Cluster query language](cluster-query-language.md)\.

`placementStrategy`  
Type: Array of objects  
Required: No  
The placement strategy objects to use for tasks in your service\. You can specify a maximum of four strategy rules per service\.    
`type`  
Type: String  
Valid values: `random` \| `spread` \| `binpack`  
Required: No  
The type of placement strategy\. The `random` placement strategy randomly places tasks on available candidates\. The `spread` placement strategy spreads placement across available candidates evenly based on the `field` parameter\. The `binpack` strategy places tasks on available candidates that have the least available amount of the resource that's specified with the `field` parameter\. For example, if you binpack on memory, a task is placed on the instance with the least amount of remaining memory but still enough to run the task\.  
`field`  
Type: String  
Required: No  
The field to apply the placement strategy against\. For the `spread` placement strategy, valid values are `instanceId` \(or `host`, which has the same effect\), or any platform or custom attribute that's applied to a container instance, such as `attribute:ecs.availability-zone`\. For the `binpack` placement strategy, valid values are `cpu` and `memory`\. For the `random` placement strategy, this field is not used\.

## Tags<a name="sd-tags"></a>

`tags`  
Type: Array of objects  
Required: No  
The metadata that you apply to the service to help you categorize and organize them\. Each tag consists of a key and an optional value, both of which you define\. When a service is deleted, the tags are deleted as well\. A maximum of 50 tags can be applied to the service\. For more information, see [Tagging your Amazon ECS resources](ecs-using-tags.md)\.    
`key`  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Required: No  
One part of a key\-value pair that make up a tag\. A key is a general label that acts like a category for more specific tag values\.  
`value`  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 256\.  
Required: No  
The optional part of a key\-value pair that make up a tag\. A value acts as a descriptor within a tag category \(key\)\.

`enableECSManagedTags`  
Type: Boolean  
Valid values: `true` \| `false`  
Required: No  
Specifies whether to use Amazon ECS managed tags for the tasks in the service\. If no value is specified, the default value is `false`\. For more information, see [Tagging your resources for billing](ecs-using-tags.md#tag-resources-for-billing)\.

`propagateTags`  
Type: String  
Valid values: `TASK_DEFINITION` \| `SERVICE`  
Required: No  
Specifies whether to copy the tags from the task definition or the service to the tasks in the service\. If no value is specified, the tags are not copied\. Tags can only be copied to the tasks within the service during service creation\. To add tags to a task after service creation or task creation, use the `TagResource` API action\.

## Network configuration<a name="sd-networkconfiguration"></a>

`networkConfiguration`  
Type: Object  
Required: No  
The network configuration for the service\. This parameter is required for task definitions that use the `awsvpc` network mode to receive their own Elastic Network Interface, and it isn't supported for other network modes\. If using the Fargate launch type, the `awsvpc` network mode is required\. For more information, see [Amazon ECS task networking](task-networking.md)\.\.    
`awsvpcConfiguration`  
Type: Object  
Required: No  
An object representing the subnets and security groups for a task or service\.    
`subnets`  
Type: Array of strings  
Required: Yes  
The subnets that are associated with the task or service\. There is a limit of 16 subnets that can be specified according to `awsvpcConfiguration`\.  
`securityGroups`  
Type: Array of strings  
Required: No  
The security groups associated with the task or service\. If you don't specify a security group, the default security group for the VPC is used\. There's a limit of five security groups that can be specified based on `awsvpcConfiguration`\.  
`assignPublicIP`  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
Whether the task's elastic network interface receives a public IP address\. If no value is specified, the default value of `DISABLED` is used\.

`healthCheckGracePeriodSeconds`  
Type: Integer  
Required: No  
The period of time, in seconds, that the Amazon ECS service scheduler should ignore unhealthy Elastic Load Balancing target health checks, container health checks, and Route 53 health checks after a task enters a `RUNNING` state\. This is only valid if your service is configured to use a load balancer\. If your service has a load balancer defined and you do not specify a health check grace period value, the default value of `0` is used\.  
If your service's tasks take a while to start and respond to health checks, you can specify a health check grace period of up to 2,147,483,647 seconds during which the ECS service scheduler ignores the health check status\. This grace period can prevent the ECS service scheduler from marking tasks as unhealthy and stopping them before they have time to come up\.  
If you do not use an Elastic Load Balancing, we recommend that you use the `startPeriod` in the task definition health check parameters\. For more information, see [Health check](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definition_healthcheck)\.

`loadBalancers`  
Type: Array of objects  
Required: No  
A load balancer object representing the load balancers to use with your service\. For services that use an Application Load Balancer or Network Load Balancer, there's a limit of five target groups that you can attach to a service\.  
After you create a service, the load balancer configuration can't be changed from the AWS Management Console\. You can use the AWS Copilot, AWS CloudFormation, AWS CLI or SDK to modify the load balancer configuration for the `ECS` rolling deployment controller only, not AWS CodeDeploy blue/green or external\. When you add, update, or remove a load balancer configuration, Amazon ECS starts a new deployment with the updated Elastic Load Balancing configuration\. This causes tasks to register to and deregister from load balancers\. We recommend that you verify this on a test environment before you update the Elastic Load Balancing configuration\. For information about how to modify the configuration, see [UpdateService](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_UpdateService.html) in the *Amazon Elastic Container Service API Reference*\.  
For Application Load Balancers and Network Load Balancers, this object must contain the load balancer target group ARN, the container name \(as it appears in a container definition\), and the container port to access from the load balancer\. When a task from this service is placed on a container instance, the container instance and port combination is registered as a target in the target group specified\.    
`targetGroupArn`  
Type: String  
Required: No  
The full Amazon Resource Name \(ARN\) of the Elastic Load Balancing target group that's associated with a service\.  
A target group ARN is only specified when using an Application Load Balancer or Network Load Balancer\.  
`loadBalancerName`  
Type: String  
Required: No  
The name of the load balancer to associate with the service\.  
If you're using an Application Load Balancer or a Network Load Balancer, omit the load balancer name parameter\.  
`containerName`  
Type: String  
Required: No  
The name of the container \(as it appears in a container definition\) to associate with the load balancer\.  
`containerPort`  
Type: Integer  
Required: No  
The port on the container to associate with the load balancer\. This port must correspond to a `containerPort` in the task definition used by tasks in the service\. For tasks that use the EC2 launch type, the container instance must allow inbound traffic on the `hostPort` of the port mapping\.

`role`  
Type: String  
Required: No  
The short name or full ARN of the IAM role that allows Amazon ECS to make calls to your load balancer on your behalf\. This parameter is only permitted if you are using a load balancer with a single target group for your service, and your task definition does not use the `awsvpc` network mode\. If you specify the `role` parameter, you must also specify a load balancer object with the `loadBalancers` parameter\.  
If your specified role has a path other than `/`, then you must either specify the full role ARN \(this is recommended\) or prefix the role name with the path\. For example, if a role with the name `bar` has a path of `/foo/` then you would specify `/foo/bar` as the role name\. For more information, see [Friendly Names and Paths](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-friendly-names) in the *IAM User Guide*\.  
If your account has already created the Amazon ECS service\-linked role, that role is used by default for your service unless you specify a role here\. The service\-linked role is required if your task definition uses the awsvpc network mode, in which case you should not specify a role here\. For more information, see [Using service\-linked roles for Amazon ECS](using-service-linked-roles.md)\.

`serviceConnectConfiguration`  
Type: Object  
Required: No  
The configuration for this service to discover and connect to services, and be discovered by, and connected from, other services within a namespace\.  
For more information, see [Service Connect ](service-connect.md)\.    
`enabled`  
Type: Boolean  
Required: Yes  
Specifies whether to use Service Connect with this service\.   
`namespace`  
Type: String  
Required: No  
The short name or full Amazon Resource Name \(ARN\) of the AWS Cloud Map namespace for use with Service Connect\. The namespace must be in the same AWS Region as the Amazon ECS service and cluster\. The type of namespace doesn't affect Service Connect\. For more information about AWS Cloud Map, see [Working with Services](https://docs.aws.amazon.com/cloud-map/latest/dg/working-with-services.html) in the *AWS Cloud Map Developer Guide*\.  
`services`  
Type: Array of objects  
Required: No  
An array of Service Connect service objects\. These are names and aliases \(also known as endpoints\) that are used by other Amazon ECS services to connect to this service\.  
This field isn't required for a "client" Amazon ECS service that's a member of a namespace only to connect to other services within the namespace\. An example is frontend application that accepts incoming requests from either a load balancer that's attached to the service or by other means\.  
An object selects a port from the task definition, assigns a name for the AWS Cloud Map service, and an array of aliases \(also known as endpoints\) and ports for client applications to refer to this service\.    
`portName`  
Type: String  
Required: Yes  
The `portName` must match the `name` of one of the `portMappings` from all of the containers in the task definition of this Amazon ECS service\.  
`discoveryName`  
Type: String  
Required: No  
The `discoveryName` is the name of the new AWS Cloud Map service that Amazon ECS creates for this Amazon ECS service\. This must be unique within the AWS Cloud Map namespace\.  
If this field isn't specified, `portName` is used\.  
`clientAliases`  
Type: Array of objects  
Required: No  
The list of client aliases for this service connect service\. You use these to assign names that can be used by client applications\. The maximum number of client aliases that you can have in this list is 1\.  
Each alias \("endpoint"\) is a DNS name and port number that other Amazon ECS services \("clients"\) can use to connect to this service\.  
Each name and port combination must be unique within the namespace\.  
These names are configured within each task of the client service, not in AWS Cloud Map\. DNS requests to resolve these names don't leave the task, and don't count toward the quota of DNS requests per second per elastic network interface\.    
`port`  
Type: Integer  
Required: Yes  
The listening port number for the service connect proxy\. This port is available inside of all of the tasks within the same namespace\.  
To avoid changing your applications in client Amazon ECS services, set this to the same port that the client application uses by default\.  
`dnsName`  
Type: String  
Required: No  
The `dnsName` is the name that you use in the applications of client tasks to connect to this service\. The name must be a valid DNS label\.  
The default value is the `discoveryName.namespace` if this field is not specified\. If the `discoveryName` isn't specified, the `portName` from the task definition is used\.  
To avoid changing your applications in client Amazon ECS services, set this to the same name that the client application uses by default\. For example, a few common names are `database`, `db`, or the lowercase name of a database, such as `mysql` or `redis`\.  
`ingressPortOverride`  
Type: Integer  
Required: No  
\(Optional\) The port number for the Service Connect proxy to listen on\.  
Use the value of this field to bypass the proxy for traffic on the port number that's specified in the named `portMapping` in the task definition of this application, and then use it in your Amazon VPC security groups to allow traffic into the proxy for this Amazon ECS service\.  
In `awsvpc` mode, the default value is the container port number that's specified in the named `portMapping` in the task definition of this application\. In `bridge` mode, the default value is the dynamic ephemeral port of the Service Connect proxy\.  
`logConfiguration`  
Type: [LogConfiguration](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_LogConfiguration.html) Object  
Required: No  
This defines where the Service Connect proxy logs are published\. Use the logs for debugging during unexpected events\. This configuration sets the `logConfiguration` parameter in the Service Connect proxy container in each task in this Amazon ECS service\. The proxy container isn't specified in the task definition\.  
We recommend that you use the same log configuration as the application containers of the task definition for this Amazon ECS service\. For FireLens, this is the log configuration of the application container\. It's not the FireLens log router container that uses the `fluent-bit` or `fluentd ` container image\.

`serviceRegistries`  
Type: Array of objects  
Required: No  
The details of the service discovery configuration for your service\. For more information, see [Service discovery](service-discovery.md)\.    
`registryArn`  
Type: String  
Required: No  
The Amazon Resource Name \(ARN\)of the service registry\. The currently supported service registry is AWS Cloud Map\. For more information, see [Working with Services](https://docs.aws.amazon.com/cloud-map/latest/dg/working-with-services.html) in the *AWS Cloud Map Developer Guide*\.  
`port`  
Type: Integer  
Required: No  
The port value that's used if your service discovery service specified an SRV record\. This field is required if both the `awsvpc` network mode and SRV records are used\.  
`containerName`  
Type: String  
Required: No  
The container name value to be used for your service discovery service\. This value is specified in the task definition\. If the task definition that your service task specifies uses the `bridge` or `host` network mode, you must specify a `containerName` and `containerPort` combination from the task definition\. If the task definition that your service task specifies uses the `awsvpc` network mode and a type SRV DNS record is used, you must specify either a `containerName` and `containerPort` combination or a `port` value, but not both\.  
`containerPort`  
Type: Integer  
Required: No  
The port value to be used for your service discovery service\. This value is specified in the task definition\. If the task definition your service task specifies uses the `bridge` or `host` network mode, you must specify a `containerName` and `containerPort` combination from the task definition\. If the task definition your service task specifies uses the `awsvpc` network mode and a type SRV DNS record is used, you must specify either a `containerName` and `containerPort` combination or a `port` value, but not both\.

## Client token<a name="sd-clienttoken"></a>

`clientToken`  
Type: String  
Required: No  
The unique, case\-sensitive identifier that you provide to ensure the idempotency of the request\. It can be up to 32 ASCII characters long\.

## Service definition template<a name="sd-template"></a>

The following shows the JSON representation of an Amazon ECS service definition\.

```
{
    "cluster": "",
    "serviceName": "",
    "taskDefinition": "",
    "loadBalancers": [
        {
            "targetGroupArn": "",
            "loadBalancerName": "",
            "containerName": "",
            "containerPort": 0
        }
    ],
    "serviceRegistries": [
        {
            "registryArn": "",
            "port": 0,
            "containerName": "",
            "containerPort": 0
        }
    ],
    "desiredCount": 0,
    "clientToken": "",
    "launchType": "FARGATE",
    "capacityProviderStrategy": [
        {
            "capacityProvider": "",
            "weight": 0,
            "base": 0
        }
    ],
    "platformVersion": "",
    "platformFamily": "",
    "role": "",
    "deploymentConfiguration": {
        "maximumPercent": 0,
        "minimumHealthyPercent": 0
    },
    "placementConstraints": [
        {
            "type": "distinctInstance",
            "expression": ""
        }
    ],
    "placementStrategy": [
        {
            "type": "spread",
            "field": ""
        }
    ],
    "networkConfiguration": {
        "awsvpcConfiguration": {
            "subnets": [
                ""
            ],
            "securityGroups": [
                ""
            ],
            "assignPublicIp": "ENABLED"
        }
    },
    "healthCheckGracePeriodSeconds": 0,
    "schedulingStrategy": "REPLICA",
    "deploymentController": {
        "type": "CODE_DEPLOY"
    },
    "tags": [
        {
            "key": "",
            "value": ""
        }
    ],
    "enableECSManagedTags": true,
    "propagateTags": "SERVICE"
}
```

You can create this service definition template using the following AWS CLI command\.

```
aws ecs create-service --generate-cli-skeleton
```