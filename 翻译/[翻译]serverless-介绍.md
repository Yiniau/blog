# introduction

---

在云计算发展近10年之后，我们处于技术转型的十字路口上。除了对“`serverless`”这个术语的混淆和误解，serverless计算也是一个热门话题。serverless开始被誉为云计算的顶峰。

> After a decade of cloud computing, we are at the crossroads of a paradigm shift in technology. Barring the confusion and the misconception of the term "serverless", serverless computing is the talk of the town. Serverless is being heralded as the pinnacle of cloud computing.

serverless是实现云计算的一种新途径，而AWS Lambda通过serveless的计算平台推出了路径。 它提供了一个事件驱动的，基于功能的，按需付费的自动缩放无服务器计算平台。解放开发者不断思考基础设施和管理手段。它将重点放在以灵活的和迭代的方式建设和运输产品。

> Serverless is a new way to approach cloud computing and AWS Lambda trailblazed the path with its serverless compute platform. It provided an event-driven, functions based, pay-per-execution, auto-scaling serverless computing platform. It is liberating the developers from constantly thinking about infrastructure and the means to manage them. It is set to bring the focus back on building and shipping products in an agile and iterative manner.

从应用程序开发的角度来看，serverless计算将功能作为开发的单位。基于事件驱动的架构，它正在创建一个事件能够跨越产品边界无缝流动的生态系统，迎来了组合和共享的新时代。异构系统和不同的服务将基于通用事件规范相互交流，形成大规模分布式系统的基础。服务及其功能可以被发现，共享，并最终跨越基础设施或供应商的边界。

> From an application development perspective, serverless computing makes functions as the unit of development. Based on event-driven architecture, it is creating an ecosystem where events will flow seamlessly across product boundaries, ushering a new era of composability and sharing. Heterogenous systems and disparate services will be talking to each other based on universal event specs, forming the basis for a large-scale, distributed system. The services and their functions could be discovered, shared and eventually run across infrastructure or provider boundaries.

无服务计算的出现并不是为了替代其他的计算平台，而是为了增加他们。这将有助于我们重新思考如何构建未来的应用程序。这同样是一个破坏性的技术，它将会改变我们编程的方式。

> Serverless computing is not here to replace every other computing platform out there. It is here to augment them. It will help us re-think how we build applications for tomorrow. It is a disruptive technology
that will change how we code.

## The Evolution 演变

回到六十年代，在全球网络上提供计算能力的概念还在设想中。愿景是使每个人都能够互联互通，并访问全球的任何网站、应用和数据。在九十年代，互联网越来越受欢迎，网络技术开始成熟。它创造了托管这些网站的需要。互联网服务供应商蓬勃发展景观，托管提供商排队基础设施，租用共享资源以满足需求。不久之后，规模变得更大，分布式托管策略就产生了全球数据中心。传统的数据中心利用有限的弹性和资源池来抽象主机环境。通过添加更多硬件来实现缩放。消费者管理应用程序堆栈，操作系统，数据，存储，网络和硬件。

> Back in the sixties, the concept of delivering computing capabilities over a global network was being envisioned. The vision was to have everyone interconnected and access applications & data, from any site, across the globe. In the nineties, with the internet gaining more popularity, the web technologies started maturing. It created a need for hosting these websites. ISPs mushroomed the landscape and hosting providers lined up infrastructure, renting shared resources to satisfy the need. Soon enough, a larger scale and distributed hosting strategy gave rise to global data-centers. Traditional data-centers abstracted the hosting environment with limited elasticity and resource pooling. Scaling was achieved by adding more hardware. Consumers managed the application stack, OS, data, storage, networking and the hardware.


随着云技术的发展，亚马逊网络服务(AWS)，Microsoft Azure，Google Compute Engine(GCE)，Joyent和OpenStack等基础架构即服务(IaaS)平台也日益普及。他们将基础架构组件抽象为自助服务模型，以通过API访问和管理计算(虚拟机或裸机)，对象存储，块存储和网络服务，并根据消费量计费。虚拟化数据中心抽象了基础设施。通过分配更多的计算(VM)和其他基础设施资源来实现缩放。消费者管理应用程序堆栈，数据和操作系统，而IaaS提供商管理虚拟化，服务器，硬盘驱动器，存储和网络。

> As cloud technologies evolved, Infrastructure-as-a-Service (IaaS) platforms like Amazon Web Services (AWS), Microsoft Azure, Google Compute Engine (GCE), Joyent, and OpenStack became prevalent. They abstracted away infrastructure components into self-service models for accessing and managing compute (VMs or bare-metal), object storage, block storage, and networking services via APIs, and billed based on consumption. Virtualized data-centers abstracted the underlying infrastructure. Scaling was achieved by allocating more compute (VMs) and other infrastructure resources. Consumers managed the application stack, data, and OS, while the IaaS provider managed the virtualization, servers, hard drives, storage, and networking.

平台即服务(PaaS)，在IaaS组件之上增加了另一层抽象，提供了一个统一的计算平台和自助式门户部署应用程序。PaaS软件平台如AWS Elastic Beanstalk，Windows Azure，Heroku，Force.com，Google App Engine，Pivotal CF，Apprenda，RedHat Openshift都是一些流行的例子。作为核心原则，PaaS以可扩展性，高可用性和多租户为基础，抽象了基础架构服务的管理。 消费者管理应用程序堆栈和数据，而PaaS提供商管理操作系统，虚拟化，服务器，存储和网络。

> Platform-as-a-Service (PaaS), added another layer of abstraction on top of the IaaS components, by providing a unified computing platform with a self-service portal to deploy applications. PaaS software platforms like AWS Elastic Beanstalk, Windows Azure, Heroku, Force.com, Google App Engine, Pivotal CF, Apprenda, RedHat Openshift are some popular examples. PaaS abstracted the management of infrastructure services, with scalability, high-availability, and multi-tenancy as it's core tenets. Consumers managed the application stack and data, while the PaaS provider managed the OS, virtualization, servers, storage, and networking.

容器化通过使用更少的资源和更快的引导时间使其轻量化来扩展虚拟化解决方案，它提供了一个便捷式的操作系统运行时，并为应用程序及其依赖提供了轻量级的分发和封装机制。PaaS平台使用容器来管理和编排应用程序。容器化通过封装和隔离可重用的功能组件作为服务来促进微服务架构模式。像Docker数据中心，Amazon ECS，Google Cloud Platform和Microsoft Azure这样的容器服务提供商也是一些流行的例子。容器运行时引擎抽象出操作系统。消费者管理应用程序堆栈和数据，而容器服务提供商管理容器引擎，主机操作系统，服务器，存储和网络。

> Containerization extended the virtualization solution by making it lightweight using fewer resources and faster boot times. It provided a portable runtime across OSs and a lightweight distribution & packaging mechanism for applications along with its dependencies. PaaS platforms use containers to manage and orchestrate applications. Containerization promoted the micro-services architecture pattern, by encapsulating & isolating reusable functional components as services. Container service providers like Docker Data Center, Amazon ECS, Google Cloud Platform, and Microsoft Azure are some popular examples. Container runtime engines abstracted the OS. Consumers manage the application stack and data, while the container service provider manages the container engine, host OS, servers, storage, and networking.

在过去的几年中，AWS Lambda使得serverless计算平台可以向公众提供，从而带来了另一轮抽象。功能即服务（FaaS）范例在短暂的容器中提供包含在“功能”中的自包含的，无状态的代码块，可以运行或触发。 所有这些都没有管理代码所需的基础架构或语言运行时。 部署的单位是功能。 Microsft Azure功能，Google Cloud功能，IBM Openwhisk，Webtask.io和Iron.io是serverless提供程序的几个例子。serverless计算抽象了语言运行时。 消费者以功能为单位管理应用程序代码，而serverless云提供商管理执行环境和其他一切。

> In the last few years, AWS Lambda made the serverless computing platform accessible to the masses, thus bringing another round of abstraction in play. The Functions-as-a-service (FaaS) paradigm promotes self-contained, stateless chunks of code packaged into 'functions', that can be run or triggered, in ephemeral containers. All done, without managing the underlying infrastructure or the language runtime needed by the code. The unit of deployment is functions. Microsft Azure Functions, Google Cloud Functions, IBM Openwhisk, Webtask.io, and Iron.io are few examples of serverless providers. Serverless computing abstracted the language runtime. Consumers manage the application code in units of functions, while the serverless cloud provider manages the execution environment and everything else under it.

## What Is Serverless?

就像无线网络还是会有电缆一样，无服务器架构仍然在某个地方有服务器。那么什么是serverless呢，作为一个开发人员，你不必担心或想到这些服务器。你可以专注于代码。

> Just like wireless internet has wires somewhere, serverless architectures still have servers somewhere. What makes something serverless is, that as a developer you don’t have to worry or think about those servers. You can just focus on code.

There are 4 core tenets you should know about:

1. **Zero Administration(零管理)**:
  这是serverless最令人兴奋的事情。而像VM和容器之类的以前的抽象仍然拥有很多相同的服务器配置和管理属性，而无服务器是一个完全不同的体验。当你准备好部署代码时，你不必事先提供任何东西，或者后来管理任何东西。没有一个舰队的概念，一个实例，甚至一个操作系统的概念。 一切都在云端运行，提供商管理您的缩放。

  > Zero Administration: This is the most exciting thing about serverless. Whereas previous abstractions like VMs and containers still shared a lot of the same configuration and administration properties of servers, serverless is a completely different experience. When you're ready to deploy code, you don't have to provision anything beforehand, or manage anything afterward. There is no concept of a fleet, an instance, or even an operating system. Everything runs in the cloud and the provider manages scaling for you.

2. **Pay-per-execution(按需付费)**
  这是通常鼓励开发人员第一次尝试serverless。拥有完整的资源利用率，而不需要为空闲时间支付一分钱是非常有吸引力的。单独这个原则就能节约超过90％的成本与VM和不可估量的开发人员的满意度，因为不必为不使用的资源付费。

  > Pay-per-execution: This is what typically incentivizes developers to try serverless for the first time. It’s alluring to have complete resource utilization without paying a cent for idle time. This tenet alone results in over 90% cost-savings over a cloud VM and immeasurable developer satisfaction in knowing that you never have to pay for resources that you don’t use.

3. **Function as unit of deployment(以功能为部署单元)**
  serverless以非常小的独立的代码(功能)组成，它们耦合松散并且互相协作 - 这也被称为微服务架构。主要优点？包含系统的部分。它们可以独立开发和部署，更少的阻断剂和更大的开发者自主性及生产力。

  > Function as unit of deployment: Serverless architectures are composed of very small, independent bits of code (functions) that are loosely coupled and collaborative—also known as a microservice architecture. The main advantage? Pieces of the system are contained. They can be developed and deployed independently. The result is fewer blockers and far greater developer autonomy and productivity.

4. **Event-Driven(事件驱动)**
  serverless的这个方面是目前最欠缺的，但在长期来看，这是最重要的。无服务器功能是无状态的，并且基本上是休眠的，直到它们对事件作出反应。事件是使他们活动，并为他们提供数据/上下文以完成任务。事件驱动的体系结构并不是什么新鲜事物，但是serverless计算的兴起对它们产生了重新兴趣，因为无服务器架构是按事件驱动的。

  > Event-Driven: This aspect of serverless is the most under-the-radar right now, but is shaping up to be the most important in the long-term. Serverless functions are stateless, and essentially dormant, until they have an event to react to. The event is what brings them to life and provides them with the data/context to do their job. Event-driven architectures are nothing new, but the rise of serverless compute has renewed interest in them because serverless architectures are by definition event-driven.
