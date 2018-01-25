## prometheus集群资源类型
> 1、prometheus自己的监控数据；<br>
> 这里比较重要的是需要prometheus去相互监控，去报各个监控节点随时可用<br>
> 2、kubernetes-node 即 kubelet 的监控数据；<br>
> kubelet 采集的数据量较大，需要过滤掉部分不可用的指标，采用params 参数过滤<br>
> 3、kube-state-metircs 收集的数据;<br>
> 这个也是收集的量比较大的一部分，需要考虑过滤<br>
> 4、nginx node-export;<br>
> 指标方面目前来看，只需要简单的几个指标，部分指标是完全可以优化掉的<br>
> 5、宿主机 node-export;<br>
> 收集系统资源的相关数据，目前来看，系统监控已经接入公司统一监控平台，问题不大<br>
> 6、master apiserver 的数据;<br>
> 需要监控master 的可用性<br>
> 7、elk 的监控指标；<br>
> 需要监控es 集群的状态<br>
> 8、lvs、etcd、push-sip、pushgateway；<br>
> (promethues 的一个比较严重的问题：  一旦prometheus的内存使用量达到法制会停止抓取数据！这个停止抓取的时间，至少是分钟级的，甚至有时候会无法恢复，【需要考虑对promethues 做完全的拆分，细粒度的】；)
## prometheus改进的问题
> 1、promethues 自己的监控性检测；<br>
> 2、配置文件如何对不使用的指标做过滤；<br>
> 3、大规模集群如何去管理；<br>
> 4、大规模监控的高可用方案调研，官方通过federation来实现；<br>
> 5、集群规模较大，排查故障的难度加大；<br>
> 6、prometheus 一旦挂掉，无法判断某台机器的健康性；<br>
> HELP prometheus_local_storage_rushed_mode 1 if the storage is in rushed mode, 0 otherwise.<br>
> 如果存储处于紧急模式，状态为0<br>
> HELP prometheus_local_storage_inconsistencies_total A counter incremented each time an inconsistency in the local storage is detected. If this is greater zero, restart the server as soon as possible.<br>
> 每当检测到本地数据中的不一致时，就会递增，如果这个数据远远大于0,重启服务<br>
## prometheus控制调度器设计目标( 整个集群的调度依赖于k8s，但是其可用性不依赖于k8s)
> 1、根据目标配置文件自动将监控项拆分成多个配置文件<br>
> 2、根据已经拆分好的配置文件，自动启动相应实例的prometheus<br>
> 3、新加监控节点时，监控节点根据当前集群的负载情况，调度到负载最低的节点，必要时新加节点进行扩容<br>
> 4、控制调度器的高可用-----采用leader-election 方式<br>
## 规范
> 1、使用标准化<br>
> 对外提供统一的监控面板，如果有需要提供统一的查询接口以及规范，提供对外的统一的接入接口，规范监控数据的保存周期；<br>
> 2、运维标准化<br>
> 统一集群管理，统一配置管理，配置集群的自我的监控告警；<br>
## 效率
> 1、集群构建<br>
> 2、集群扩容<br>