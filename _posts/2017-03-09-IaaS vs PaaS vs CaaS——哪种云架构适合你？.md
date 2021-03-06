---
title: 'IaaS vs PaaS vs CaaS——哪种云架构适合你'
layout: post
tags:
    - DevOps
    - IaaS
    - PaaS
    - CaaS
    - 云架构
    - AWS EC2
    - Docker
    - lxc
---
【编者的话】本文介绍了Web托管服务进化的历史，从专用服务器到虚拟化到IaaS、PaaS和最新的CaaS；说明了每种方式的优点和缺点。着重阐述了构建PaaS应用的各个组件，以及在选择CaaS提供商时应该考虑的问题。最后从成本和功能性的角度论述了PaaS用户应该迁移到CaaS平台。  

Web托管服务的世界正在快速变化，你很容易就被众多的选项所迷惑并开始质疑自己的选择是不是最适合自己的业务需要的。在这篇博文中，我将剖析几种常见的Web托管服务方式的不同点：
  
* 基础设施即服务（IaaS）
* 平台即服务（PaaS）
* 容器即服务（CaaS）

读完这篇博文，你应该可以清楚的理解：

* 这些名词到底指什么
* 这为什么对你很重要
* 对于你的使用场景，哪种方式最适合

当然没有一种方式能够适合所有的Web基础设施的场景，你的特殊场景或许需要特殊的考虑。  

## 从头开始——专用服务器

Web托管的本质还是在一个数据中心里布满了服务器、交换机、路由器、存储阵列和其它网络组件。我们所讨论的每一个选项不管它是PaaS、IaaS还是CaaS，它底层使用的基础设施都是相同的，即在一个大屋子里的很多服务器。 他们只是上面增加了很多抽象层使得管理变得更简单，和使用自动化的方式处理以前很慢或者需要手动步骤的任务。
专用服务器，或者叫做“裸机”有它的优点和缺点。

**优点**  

* 性能——你在直接使用一个服务器，没有任何抽象层例如虚拟化带来的性能开销。
* 可靠性——没有抽象层和虚拟化，出错的可能性会降低。
* 资源利用率——使用专用服务器，你的进程不需要跟其它的虚拟机或者进程竞争资源例如CPU、内存和带宽。

**缺点**  

* 管理复杂性——你无法容易地克隆专用服务器来产生更多的实例，专用服务器没有AMI（译者注：Amazon Machine Image）或者镜像的概念。想扩展你的基础设施到多个服务器上？我希望你用配置管理，或者使用可信赖的老朋友rsync。
* 成本——使用专用服务器，在大多数情况下你需要预先支付服务器的硬件价格，然后再付租用场地的成本，或者你从一个服务提供商那里租赁它们。无论哪种方式，你都没法在不使用时终止服务器的租赁来节省成本，因此你需要更加谨慎的对待你的财务计划。
* 你所有的进程和应用都运行在专用服务器上，因此运行在相同的操作系统上。为了可扩展性的目的，你往往希望服务器只运行单一的进程，例如Web服务或者数据库服务。在一个服务器上运行所有的应用使得操作系统的调优变得更困难。

## 使事情变得更容易——虚拟化

尽管专用服务器有很多优点，但是它的缺点在大多数情况下会远远超过其优点。公司为了能够迅速进入市场赢得竞争，对于部署的速度提出了更高的要求，虚拟化就很自然的成为了数据中心进化中的下一个步骤。  

#### 什么是虚拟化?

简而言之，虚拟化就是将物理服务器分成更小的虚拟机，这些虚拟机只能访问物理机的部分资源。  

例如你有一个8CPU核以及16GB内存的物理服务器，则可以将它分成8个虚拟机，每个虚拟机有1个CPU和2GB内存。  
你或许已经听说过一些虚拟化技术如Xen, KVM, VMware和Hyper-V，其实很有更多。  

**优点**  

* 你可以在需要更多虚拟机实例或者想要虚拟机共享时进行虚拟机的克隆。
* 你可以备份虚拟机镜像作为安全保护或者灾备。

**缺点**  

* 使用虚拟化意味着额外的性能开销，可能造成性能下降。
* 虚拟机镜像在不同的服务提供商和虚拟化技术之间基本上是不可移植的。
* 管理虚拟机依然是手工步骤，需要管理的时间和专业知识。

## 进化——虚拟化变成IaaS

你知道[51% 的人认为“云”会被天气影响吗？](http://www.businessinsider.com/people-think-stormy-weather-affects-cloud-computing-2012-8)你知道当人们谈论“云”时他们实际上是在讨论基础设施即服务吗？  

什么是基础设施即服务呢？  

* 通过API来管理硬件的虚拟化。
* 通过编程接口访问计算、存储和网络资源和配置。
* 当你需要是申请一个新的虚拟机，当你完成后终止虚拟机，你只需要为你使用的资源付费。
* 将数据中心资源视为公共设施。  

亚马逊在2006年发布了其Amazon Web Services（AWS）和EC2产品，成为了这个领域的先驱。  

#### 为什么这个进化如此重要?

在过去当你需要上线一个在线业务时，你不得不进行复杂的计划来保证你在数据中心内有足够的机架空间、足够的服务器和存储来处理业务的增长，足够的网络带宽来处理所有的用户流量。这种计划并不容易，尤其是对早期的业务来讲，你无法预测业务的未来和增长轨迹。  

1. 它给了开发者超能量：

	* 构思一个想法立即上线。
	* 如果成功了，可以容易的扩展规模。
	* 如果失败了，关闭虚拟机以节约成本。

2. 它使得数据中心自动化变得更强大： 
 
	* 全自动基础设施管理成为可能，出现了“基础设施即代码”的概念。
	* 弹性伸缩在过去是不可能实现的，但是现在web基础设施可以当业务变化时自动的扩展或者收缩。
	* 在服务器的自动化管理之后，数据中心的自动化也扩展到了存储、网络以及其它大量的需要专业知识的系统，在代码和API能够处理这些自动化之后，这给全球的开发者社区带来了新的机遇。  

下面我们将讨论PaaS的构成以及它如何跟Docker及容器相关联，接下来将讨论容器托管平台和容器即服务。  

我们已经讨论了IaaS的具体细节，让我们跳到PaaS的话题吧。如果你错过了第一部分的讨论，可以从这里找到.  

平台即服务（PaaS）使得开发人员可以很容易的部署和管理应用而隐藏掉所有的实现细节例如服务器、负载均衡器、DNS等等。他们或多或少的去除了对运维团队的需求，而是让开发人员去关注应用部署。有很多PaaS提供商，其中一个最流行的是Heroku。关于Heroku的一个有趣的事情是Heroku完全是运行在AWS之上并依赖AWS的。如果你不愿意你的应用运行在美国东部或者欧洲西部的AWS上的话，这或许是一个问题。没有IaaS，像Heroku这样的平台可能就不会存在了。PaaS提供商利用IaaS作为一个非常重要的组成部分来交付它们的服务。在减少从想法到部署的时间、自动化流程以及减少特殊的系统知识需求的趋势中，PaaS是合理的下一步，就像是从专用服务器到IaaS一样。  
  
## 构建PaaS是困难的

能够通过抽象而避免处理服务器相关的问题是不容易的，需要引入很多东西才能做到。  

在PaaS的概念之下有几个主要的模块：

* 编译系统——把你的代码编译成可以运行的应用，并且把它存储到某个地方作为后续使用。
* 应用管理数据库——管理Git版本、build版本和应用元数据。
* 集群调度器——将一组服务器作为一台大的服务器来管理，在服务器资源池的某个服务器上运行你的程序，并检查和替换失效的作业。
* 负载均衡——允许从Internet和内部程序来的业务负载被正确的路由到合适的位置。
* DNS——在你产生和修改应用时自动配置DNS条目。
* 或许是最重要的——某个形式的容器化例如  freebsd jails、solaris zones、或者linux containers，容器化使得某个的应用不会进入和影响到其它的应用。  

第一个和最后一个点是Docker迅速发展的非常重要的原因。Linux容器技术已经存在了一段时间了，但是只有某些大的公司或者PaaS提供商自动化了容器的使用，Docker使Linux容器的使用变得非常简单，也提供了标准化的镜像格式，它成为了PaaS中重要的组成部分。  

这非常强大，因为很多公司在处理爆发式的用户增长，来自于移动、社交和IoT的新的业务模式，以及期望从传统的长的版本发布周期转变到持续交付。他们利用基于软件的架构或者微服务。他们需要能够自动部署和测试新的代码，并根据业务的变化弹性伸缩。但问题是他们没有足够的资源和经验来交付到他们的开发团队，所以他们不得不将其外包给PaaS提供商。不幸的是，当你这么做的时候，你就放弃了对于这个黑盒子的很多控制，而是花很多钱并使得自己完全依赖于它。另外一个选项是，很多公司反复的重新发明轮子或者使用那些难于维护的、运行缓慢的和易于出错的工具。  

最好的情形是在自己的数据中心或者Cloud帐号中运行一个PaaS平台，你可以全权控制，你的团队可以直接参与应用的部署，因此可以形成一种真正的DevOps文化。Docker及其标准化的生态系统是这种愿景成为现实的重要一步。  

## 容器托管平台

什么是容器托管平台，或者叫容器即服务呢？这就是我在上面的章节中提到的另外一种构建在Docker基础上的Web托管方式。它是你用容器来部署复杂的多层次的应用所需要的自动化的编译系统，集群调度器，负载均衡，DNS自动化以及服务发现。这是PaaS的另外一部分内容，但是你不是用一个复杂的编译系统来编译你的应用，而是用Docker镜像。镜像可以非常容易的进行编译和本地测试，因此你在部署应用之前就确认应用可以正常工作。现在PaaS用户也可以得到相同的体验，但是不需要拥有自己的硬件或者云帐号的前提下节约大量的成本。他们不再受限于其服务提供商所支持的环境，可以通过将应用打包成Docker镜像的方式在任何地方运行。  

在这个过程中，利用诸如[ContainerShip Cloud](https://containership.io/)，你可以在任何地方，在任何云平台上甚至是[裸机](https://www.packet.net/blog/announcing-containership-integration/)上用相同的方式运行你的应用。你不再被卡在构建复杂的应用来解决非常困难的问题，或者卡在使用某个提供商的特殊的自动化部署工具上。  

但是选择一个正确的Docker容器托管平台并不是一件容易的事情，有很多的问题需要你去思考和注意。  

* 有大量的选项，因此选择其中一个进行研究就变得困难。好消息是如果你的应用已经容器化，你就可以轻松的测试其中的每一个。
* 你正在使用的服务提供商例如Triton或者ECS的某个解决方案或许使得你无法脱离这个提供商，这也使得Docker的最令人兴奋的可移植性变得不那么重要了。
* 许多CaaS方案在他们自己的硬件上运行所有的管理系统，你自己的服务器上只是运行某个代理程序来连接他们的API，因此如果他们的系统宕机后你的集群将无法工作。
* 允许你在防火墙后运行整个系统的方案是非常复杂的，需要一个团队去管理和升级。
* 能够容易的在防火墙后运行整个系统的方案会使得高可用成为低优先级的事情。
* 当你选择了一个CaaS提供商时，你就可以摆脱了你的服务托管提供商的锁定，但是你将陷入到CaaS提供商的锁定。你无法不使用他们的SaaS管理接口而在你自己的硬件上运行整个系统包括管理系统，API和UI。你一旦决定付钱，你最好可以一直付钱。

## 你应该从PaaS迁移到CaaS吗？

我的观点是好处远远大于缺点。我认为真正的问题是，如果你已经使用了某个PaaS提供商，你是否应该花时间学习Docker并使得你的整个团队认可这个想法？你是否应该花时间去学习一个新的容器托管平台？  

无可否认，是的。容器会一直在那里，PaaS提供商会转型成CaaS提供商，不要拒绝容器的浪潮,去拥抱它吧。 
 
* 你将会花很多钱付给你的PaaS提供商，或者大的Web托管商，但这并不是运行你的程序的正确的地方。  

例如，一个标准的2x Heroku dyno有不限量的CPU和1GB内存。如果你需要8个这样的实例，你就需要每个月付400美元。  

在DigitalOcean, 你可以用80美元/月（加上你的CaaS提供商的每服务器成本）租用一个4CPU和8GB内存的droplet来运行8个0.5CPU和1GB内存的8个Docker实例，你大约可以每个月节省300美元，这对很多公司来说是一笔不小的数目。  

* 你将会获得root权限来访问运行你的应用的服务器。

你将可以真正的从更深的层次理解你的应用运行的情况。 
 
* 你将会获得你的PaaS提供商无法提供的Docker的能力。

能够使得团队中的每个人可以在本地测试他的代码然后编译，并且知道它可以工作，这对一个团队来说是一个非常大的进步。  

你将会采用最好的工具来完成你的工作，而不是使用你的服务提供商所能提供的最好的工具。  

## 结论

感谢你阅读这篇从专用服务器到容器托管平台的背景知识的文章。希望这可以帮助到你回答一些问题和指出了一些好处能让你尝试一下容器。  

想要学习更多容器的知识以及如何在生产环境中使用容器吗？ 点击[此处](https://www.containership.io/)订阅我们的邮件列表吧。

另外，请参阅我们的[官方文档](https://docs.containership.io/docs)，给我们提出宝贵意见，或者联系我们！  

原文链接：[IaaS vs PaaS vs CaaS - Which Cloud Architecture is Right For You? Part 1](https://blog.containership.io/iaas-vs-paas-vs-caas-which-cloud-architecture-is-right-for-you-part-1-c7bf3c48c70c)、[IaaS vs PaaS vs CaaS - Which Cloud Architecture is Right For You? Part 2](https://blog.containership.io/iaas-vs-paas-vs-caas-which-cloud-architecture-is-right-for-you-part-2-a72623d7d001)