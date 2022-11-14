---
layout: post
read_time: true
show_date: true
title: "Kubernetes /stats/summary 源码解析"
date: 2022-11-11
img: posts/20221111/kubernetes-stats-summary.png
tags: [kubernetes]
author: Mengjiao Liu
description: "kubelet 在节点、卷、Pod 和容器级别收集统计信息， 并在 Summary API 中提供它们的统计信息供用户阅读霍调用。"
---

# 前情提要
本文基于 Kubernetes 1.26 版本中 kubelet 部分的源码。当时是遇到一个 [cAdvisor 覆盖了 CRI stats 数据的 issue](https://github.com/kubernetes/kubernetes/issues/107172) ，
在修复此问题的同时，对这部分代码进行了研究，于是提交完 PR 后，就写篇文章进行总结。
（在撰写本文时，kubernetes v1.26 版本还未发布（即将），所以 此时Kubernetes 集群示例使用的是 v1.25.3, 代码部分解析直接使用的是 master 分支，也就是即将发布的 1.26 版本）

这是撰写本文时 kubernetes git 仓库 commit，可以使用下面的命令切到当时的 commit， 当然，不切也可以，Kubernetes 社区非常活跃，所以下文列出的文件行号可能会变化，但是函数名称一般不会变，请灵活搜索~
```shell
git checkout 8e48df135318026d5f8a972a96a2ff665889568a
```

`/stats/summary` 在 Kubernetes 中由 [SIG instrumentation](https://github.com/kubernetes/community/blob/master/sig-instrumentation/README.md) 负责，感兴趣的可以了解下。
非常欢迎新的贡献者共同参与~

# Summary API
kubelet 在节点、卷、Pod 和容器级别收集统计信息， 并在 Summary API 中提供它们的统计信息供消费者阅读。

我们可以直接获取其数据，看下它长什么样子，这样可以比较直观的了解它到底提供了什么数据，也便于后面理解源码。

现在让我们开始吧！

## 获取 /stats/summary API 数据
获取数据，可以使用以下两种方法：
- kubectl
- curl
在使用 Kubernetes 的过程中，大家对 kubectl 命令肯定是最熟悉的。我们就以 kubectl 命令为例。
使用 curl 命令获取此端点数据也是可以的，需要先拿到 apiserver 的证书，这里不再赘述。有兴趣的小伙伴可以自行搜索。

首先需要获取节点名称：
```shell
kubectl get nodes
NAME         STATUS   ROLES                  AGE    VERSION
cluster-1-25-3-control-plane   Ready    control-plane   14d   v1.25.3
```
现在节点名称为：cluster-1-25-3-control-plane
```bash
NODE_NAME=cluster-1-25-3-control-plane
kubectl get --raw /api/v1/nodes/$NODE_NAME/proxy/stats/summary
```
得到如下格式的输出（原数据太长，这里只展示部分）：
```json
{
 "node": {
  "nodeName": "cluster-1-25-3-control-plane",
  "systemContainers": [
   {
    "name": "kubelet",
    "startTime": "2022-10-27T10:23:47Z",
    "cpu": {
     "time": "2022-11-11T07:58:13Z",
     "usageNanoCores": 24126473,
     "usageCoreNanoSeconds": 32503705524270
    },
    "memory": {
     "time": "2022-11-11T07:58:13Z",
     "usageBytes": 52457472,
     "workingSetBytes": 52432896,
     "rssBytes": 51089408,
     "pageFaults": 19121096,
     "majorPageFaults": 0
    }
   },
   {
    "name": "pods",
    "startTime": "2022-10-27T10:23:33Z",
    "cpu": {
     "time": "2022-11-11T07:58:06Z",
     "usageNanoCores": 96740740,
     "usageCoreNanoSeconds": 128623648470673
    },
    "memory": {
     "time": "2022-11-11T07:58:06Z",
     "availableBytes": 15866990592,
     "usageBytes": 822579200,
     "workingSetBytes": 772874240,
     "rssBytes": 431116288,
     "pageFaults": 0,
     "majorPageFaults": 0
    }
   }
  ],
  "startTime": "2022-10-18T02:50:29Z",
  "cpu": {
   "time": "2022-11-11T07:58:06Z",
   "usageNanoCores": 129428788,
   "usageCoreNanoSeconds": 171267526291305
  },
  "memory": {
   "time": "2022-11-11T07:58:06Z",
   "availableBytes": 15189405696,
   "usageBytes": 2151161856,
   "workingSetBytes": 1450459136,
   "rssBytes": 568283136,
   "pageFaults": 2795985,
   "majorPageFaults": 12
  },
  "network": {
   "time": "2022-11-11T07:58:06Z",
   "name": "eth0",
   "rxBytes": 236611673,
   "rxErrors": 0,
   "txBytes": 14528902,
   "txErrors": 0,
   "interfaces": [
    {
     "name": "eth0",
     "rxBytes": 236611673,
     "rxErrors": 0,
     "txBytes": 14528902,
     "txErrors": 0
    }
   ]
  },
  "fs": {
   "time": "2022-11-11T07:58:06Z",
   "availableBytes": 15873736704,
   "capacityBytes": 205918412800,
   "usedBytes": 190044676096,
   "inodesFree": 31007474,
   "inodes": 32462592,
   "inodesUsed": 1455118
  },
  "runtime": {
   "imageFs": {
    "time": "2022-11-11T07:58:12Z",
    "availableBytes": 15873736704,
    "capacityBytes": 205918412800,
    "usedBytes": 843317248,
    "inodesFree": 31007474,
    "inodes": 32462592,
    "inodesUsed": 13541
   }
  },
  "rlimit": {
   "time": "2022-11-11T07:58:14Z",
   "maxpid": 126764,
   "curproc": 591
  }
 },
 "pods": [
  {
   "podRef": {
    "name": "kube-controller-manager-cluster-1-25-3-control-plane",
    "namespace": "kube-system",
    "uid": "588ef5a332ad15499df81b7e59d72b14"
   },
   "startTime": "2022-10-28T02:08:39Z",
   "containers": [
    {
     "name": "kube-controller-manager",
     "startTime": "2022-11-02T03:50:41Z",
     "cpu": {
      "time": "2022-11-11T07:58:13Z",
      "usageNanoCores": 20507082,
      "usageCoreNanoSeconds": 16645906408682
     },
     "memory": {
      "time": "2022-11-11T07:58:13Z",
      "usageBytes": 56401920,
      "workingSetBytes": 54874112,
      "rssBytes": 48599040,
      "pageFaults": 35325,
      "majorPageFaults": 7
     },
     "rootfs": {
      "time": "2022-11-11T07:58:12Z",
      "availableBytes": 15873736704,
      "capacityBytes": 205918412800,
      "usedBytes": 8192,
      "inodesFree": 31007474,
      "inodes": 32462592,
      "inodesUsed": 22
     },
     "logs": {
      "time": "2022-11-11T07:58:14Z",
      "availableBytes": 15873736704,
      "capacityBytes": 205918412800,
      "usedBytes": 16437248,
      "inodesFree": 31007474,
      "inodes": 32462592,
      "inodesUsed": 5
     }
    }
   ],
   "cpu": {
    "time": "2022-11-11T07:58:01Z",
    "usageNanoCores": 20499853,
    "usageCoreNanoSeconds": 25552286513427
   },
   "memory": {
    "time": "2022-11-11T07:58:01Z",
    "usageBytes": 60678144,
    "workingSetBytes": 54992896,
    "rssBytes": 48640000,
    "pageFaults": 0,
    "majorPageFaults": 0
   },
   "ephemeral-storage": {
    "time": "2022-11-11T07:58:14Z",
    "availableBytes": 15873736704,
    "capacityBytes": 205918412800,
    "usedBytes": 16449536,
    "inodesFree": 31007474,
    "inodes": 32462592,
    "inodesUsed": 28
   },
   "process_stats": {
    "process_count": 0
   }
  },
  ...
 ]
}
```

# 探索代码
现在我们已经知道数据最终的形态是什么样子了，大家有了初步印象，现在开始正式的代码探索吧~

溯源从先从 /stats/summary 这个 api 进行代码溯源，会看到 statsProvider 赋值给kubelet，
kubelet 启动 summary api，summary api 又使用 handle 方法实现，详情请看下面的具体代码。

## kubelet 初始化 summary Provider 流程
```go
// kubelet 启动
// cmd/kubelet/kubelet.go:34
func main() {
	command := app.NewKubeletCommand()
	code := cli.Run(command)
	os.Exit(code)
}

↓↓↓
// cmd/kubelet/app/server.go:264
// run the kubelet
return Run(ctx, kubeletServer, kubeletDeps, utilfeature.DefaultFeatureGate)

↓↓↓
// cmd/kubelet/app/server.go:419
if err := run(ctx, s, kubeDeps, featureGate); err != nil {
		return fmt.Errorf("failed to run Kubelet: %w", err)
	}

↓↓↓
// cmd/kubelet/app/server.go:783
if err := RunKubelet(s, kubeDeps, s.RunOnce); err != nil {
		return err
	}

↓↓↓
// 此处最终 kubelet 实例 得到 statsProvider，在 1185 行 的 startKubelet 启动 kubelet server（包含 /stats/summary api）
// 点进去createAndInitKubelet 函数，可以看到 kubelet.NewMainKubelet() 函数，在这个函数内可以获取到 kubelet 最终选择的 StatsProvider
// cmd/kubelet/app/server.go:1157
k, err := createAndInitKubelet(kubeServer,
		kubeDeps,
		hostname,
		hostnameOverridden,
		nodeName,
		nodeIPs)
	if err != nil {
		return fmt.Errorf("failed to create kubelet: %w", err)
	}


↓↓↓
// 
// cmd/kubelet/app/server.go:1207
k, err = kubelet.NewMainKubelet(&kubeServer.KubeletConfiguration,
		kubeDeps,
		&kubeServer.ContainerRuntimeOptions,
		hostname,
		hostnameOverridden,
		nodeName,
		nodeIPs,
		kubeServer.ProviderID,
		kubeServer.CloudProvider,
		kubeServer.CertDirectory,
		kubeServer.RootDirectory,
		kubeServer.ImageCredentialProviderConfigFile,
		kubeServer.ImageCredentialProviderBinDir,
		kubeServer.RegisterNode,
		kubeServer.RegisterWithTaints,
		kubeServer.AllowedUnsafeSysctls,
		kubeServer.ExperimentalMounterPath,
		kubeServer.KernelMemcgNotification,
		kubeServer.ExperimentalNodeAllocatableIgnoreEvictionThreshold,
		kubeServer.MinimumGCAge,
		kubeServer.MaxPerPodContainerCount,
		kubeServer.MaxContainerCount,
		kubeServer.MasterServiceNamespace,
		kubeServer.RegisterSchedulable,
		kubeServer.KeepTerminatedPodVolumes,
		kubeServer.NodeLabels,
		kubeServer.NodeStatusMaxImages,
		kubeServer.KubeletFlags.SeccompDefault || kubeServer.KubeletConfiguration.SeccompDefault)
	if err != nil {
		return nil, err
	}
...

// 此处给 kubelet 赋值 statsProvider，选择 cAdvisor 还是 CRI 作为 StatsProvider
// 如果 runtime 是 CRI-O，那么使用 CadvisorStatsProvider
// 否则使用 CRIStatsProvider （因为现在  从CRI获取所有的 stats还没有完全支持，所以其实使用CRIStatsProvider 就是使用 CRI 和 cadvisor 混杂数据的模式。具体可看 KEP）
// 如果kubelet 又启用了 PodAndContainerStatsFromCRI 特性门控，那么就不使用cAdvisor stats 填充 CRI 不存在的数据
// pkg/kubelet/kubelet.go:697
if kubeDeps.useLegacyCadvisorStats {
		klet.StatsProvider = stats.NewCadvisorStatsProvider(
			klet.cadvisor,
			klet.resourceAnalyzer,
			klet.podManager,
			klet.runtimeCache,
			klet.containerRuntime,
			klet.statusManager,
			hostStatsProvider)
	} else {
		klet.StatsProvider = stats.NewCRIStatsProvider(
			klet.cadvisor,
			klet.resourceAnalyzer,
			klet.podManager,
			klet.runtimeCache,
			kubeDeps.RemoteRuntimeService,
			kubeDeps.RemoteImageService,
			hostStatsProvider,
			utilfeature.DefaultFeatureGate.Enabled(features.PodAndContainerStatsFromCRI))
	}


↓↓↓
// cmd/kubelet/app/server.go:1185
// k 变量包含 kubelet stats provider 参数
startKubelet(k, podCfg, &kubeServer.KubeletConfiguration, kubeDeps, kubeServer.EnableServer)
		klog.InfoS("Started kubelet")
```

## `/stats/summary` api 启动过程
```go
// k 变量包含 kubelet stats provider 参数
// 在1157 行运行createAndInitKubelet函数后 kubelet已经得到 statsProvider
// cmd/kubelet/app/server.go:1185
startKubelet(k, podCfg, &kubeServer.KubeletConfiguration, kubeDeps, kubeServer.EnableServer)

↓↓↓
// cmd/kubelet/app/server.go:1195
// start the kubelet server
	if enableServer {
		go k.ListenAndServe(kubeCfg, kubeDeps.TLSOptions, kubeDeps.Auth, kubeDeps.TracerProvider)
	}

↓↓↓
// pkg/kubelet/kubelet.go:2513
server.ListenAndServeKubeletServer(kl, kl.resourceAnalyzer, kubeCfg, tlsOptions, auth, tp)

↓↓↓
// pkg/kubelet/server/server.go:162
handler := NewServer(host, resourceAnalyzer, auth, tp, kubeCfg)

↓↓↓
// pkg/kubelet/server/server.go:279
server.InstallDefaultHandlers()

↓↓↓
// pkg/kubelet/server/server.go:382
s.addMetricsBucketMatcher("stats")
	s.restfulCont.Add(stats.CreateHandlers(statsPath, s.host, s.resourceAnalyzer))

↓↓↓
// pkg/kubelet/server/stats/handler.go:122
endpoints := []struct {
		path    string
		handler restful.RouteFunction
	}{
		{"/summary", h.handleSummary},
	}

↓↓↓
// pkg/kubelet/server/stats/handler.go:143
// Handles stats summary requests to /stats/summary
// If "only_cpu_and_memory" GET param is true then only cpu and memory is returned in response.
func (h *handler) handleSummary(request *restful.Request, response *restful.Response) {
	ctx := request.Request.Context()
	onlyCPUAndMemory := false
	err := request.Request.ParseForm()
	if err != nil {
		handleError(response, "/stats/summary", fmt.Errorf("parse form failed: %w", err))
		return
	}
	if onlyCluAndMemoryParam, found := request.Request.Form["only_cpu_and_memory"]; found &&
		len(onlyCluAndMemoryParam) == 1 && onlyCluAndMemoryParam[0] == "true" {
		onlyCPUAndMemory = true
	}
	var summary *statsapi.Summary
	if onlyCPUAndMemory {
    //在此处summaryProvider调用会得到 cpu 和 memory 统计数据
		summary, err = h.summaryProvider.GetCPUAndMemoryStats(ctx)
	} else {
		// external calls to the summary API use cached stats
		forceStatsUpdate := false
    // 在此处summaryProvider调用会得到所有统计数据
		summary, err = h.summaryProvider.Get(ctx, forceStatsUpdate)
	}
	if err != nil {
		handleError(response, "/stats/summary", err)
	} else {
		writeResponse(response, summary)
	}
}
```
## `/stats/summary` 具体实现
Kubernetes 现在 `/stats/summary` 端点的数据默认是这两种混杂的，未来的话，社区是计划全部从 CRI 获取 stats 数据，
以避免对 cAdvisor 的依赖。
这部分的设计详情，请参看：[cAdvisor-less, CRI-full Container and Pod Stats KEP](https://github.com/kubernetes/enhancements/tree/master/keps/sig-node/2371-cri-pod-container-stats#cadvisor-less-cri-full-container-and-pod-stats)

从 `GetCPUAndMemoryStats`（这个函数数据仅包含 CPU 和内存的，和列出全部数据的逻辑 Get 函数是一样的，依此为例）顺着向下，我们其实会看到现在是有 2 个 实现：cAdvisor实现 和 CRI 实现。下文以 CRI 实现为例（这也是社区的倾向，Cadvisor 代码实现是类似的，不再赘述）。
```go
// pkg/kubelet/server/stats/summary.go:32
// SummaryProvider provides summaries of the stats from Kubelet.
type SummaryProvider interface {
	// Get provides a new Summary with the stats from Kubelet,
	// and will update some stats if updateStats is true
	Get(ctx context.Context, updateStats bool) (*statsapi.Summary, error)
	// GetCPUAndMemoryStats provides a new Summary with the CPU and memory stats from Kubelet,
	GetCPUAndMemoryStats(ctx context.Context) (*statsapi.Summary, error)
}


↓↓↓
// 在这里会得到 nodeStats 和 podStats
// pkg/kubelet/server/stats/summary.go:122
func (sp *summaryProviderImpl) GetCPUAndMemoryStats(ctx context.Context) (*statsapi.Summary, error) {
	// TODO(timstclair): Consider returning a best-effort response if any of
	// the following errors occur.
	node, err := sp.provider.GetNode()
	if err != nil {
		return nil, fmt.Errorf("failed to get node info: %v", err)
	}
	nodeConfig := sp.provider.GetNodeConfig()
	rootStats, err := sp.provider.GetCgroupCPUAndMemoryStats("/", false)
	if err != nil {
		return nil, fmt.Errorf("failed to get root cgroup stats: %v", err)
	}

  // 得到 podStats
	podStats, err := sp.provider.ListPodCPUAndMemoryStats(ctx)
	if err != nil {
		return nil, fmt.Errorf("failed to list pod stats: %v", err)
	}

	nodeStats := statsapi.NodeStats{
		NodeName:         node.Name,
		CPU:              rootStats.CPU,
		Memory:           rootStats.Memory,
		StartTime:        rootStats.StartTime,
		SystemContainers: sp.GetSystemContainersCPUAndMemoryStats(nodeConfig, podStats, false),
	}
	summary := statsapi.Summary{
		Node: nodeStats,
		Pods: podStats,
	}
	return &summary, nil
}

↓↓↓↓↓↓
// Provider 接口会定义所有的 stats 处理方法，其中 pod stats 是调用 kubelet StatsProvider结构体（pkg/kubelet/kubelet.go:1232）的方法来实现的，可看下面的 pkg/kubelet/kubelet.go:1262 行
// pkg/kubelet/server/stats/handler.go:39
// Provider hosts methods required by stats handlers.
type Provider interface {
	// The following stats are provided by either CRI or cAdvisor.
	//
	// ListPodStats returns the stats of all the containers managed by pods.
	ListPodStats(ctx context.Context) ([]statsapi.PodStats, error)
	// ListPodStatsAndUpdateCPUNanoCoreUsage updates the cpu nano core usage for
	// the containers and returns the stats for all the pod-managed containers.
	ListPodCPUAndMemoryStats(ctx context.Context) ([]statsapi.PodStats, error)
	// ListPodStatsAndUpdateCPUNanoCoreUsage returns the stats of all the
	// containers managed by pods and force update the cpu usageNanoCores.
	// This is a workaround for CRI runtimes that do not integrate with
	// cadvisor. See https://github.com/kubernetes/kubernetes/issues/72788
	// for more details.
  ...
}

↓↓↓
// stats provider
// Provider provides the stats of the node and the pod-managed containers.
type Provider struct {
	cadvisor     cadvisor.Interface
	podManager   kubepod.Manager
	runtimeCache kubecontainer.RuntimeCache
	containerStatsProvider
	rlimitStatsProvider
}

// containerStatsProvider is an interface that provides the stats of the
// containers managed by pods.
type containerStatsProvider interface {
	ListPodStats(ctx context.Context) ([]statsapi.PodStats, error)
	ListPodStatsAndUpdateCPUNanoCoreUsage(ctx context.Context) ([]statsapi.PodStats, error)
	ListPodCPUAndMemoryStats(ctx context.Context) ([]statsapi.PodStats, error)
	ImageFsStats(ctx context.Context) (*statsapi.FsStats, error)
	ImageFsDevice(ctx context.Context) (string, error)
}


↓↓↓
//pkg/kubelet/kubelet.go:1262
// ListPodCPUAndMemoryStats is delegated to StatsProvider, which implements stats.Provider interface
func (kl *Kubelet) ListPodCPUAndMemoryStats(ctx context.Context) ([]statsapi.PodStats, error) {
	return kl.StatsProvider.ListPodCPUAndMemoryStats(ctx)
}


↓↓↓
分为 cadvisorStatsProvider 和criStatsProvider 两种实现

// criStatsProvider 具体实现
// 此部分代码是 cAdvisor stats 实现，跟下文 CRI 实现类似，或者说 CRI 实现现在包含 cAdvisor 的数据，这部分代码就不再详细列出
// 感兴趣的同学可以自行查看
// pkg/kubelet/stats/cadvisor_stats_provider.go:178
// ListPodCPUAndMemoryStats returns the cpu and memory stats of all the pod-managed containers.
func (p *cadvisorStatsProvider) ListPodCPUAndMemoryStats(_ context.Context) ([]statsapi.PodStats, error) {
...
}

↓↓↓
// CRI 具体实现
// 这一段基本就是找到 pod 管理的容器，移除掉终止的，将其从 CRI 获取的数据重新整合为符合`/stats/summary` 的数据格式
// 如果从不能从 CRI获取到 stats 数据，那就使用 cAdvisor 数据填充。
// pkg/kubelet/stats/cri_stats_provider.go:270
// ListPodCPUAndMemoryStats returns the CPU and Memory stats of all the pod-managed containers.
func (p *criStatsProvider) ListPodCPUAndMemoryStats(ctx context.Context) ([]statsapi.PodStats, error) {
	// sandboxIDToPodStats is a temporary map from sandbox ID to its pod stats.
	sandboxIDToPodStats := make(map[string]*statsapi.PodStats)
	containerMap, podSandboxMap, err := p.getPodAndContainerMaps(ctx)
	if err != nil {
		return nil, fmt.Errorf("failed to get pod or container map: %v", err)
	}

	result := make([]statsapi.PodStats, 0, len(podSandboxMap))
	if p.podAndContainerStatsFromCRI {
		criSandboxStats, err := p.runtimeService.ListPodSandboxStats(ctx, &runtimeapi.PodSandboxStatsFilter{})
		// Call succeeded
		if err == nil {
			for _, criSandboxStat := range criSandboxStats {
				podSandbox, found := podSandboxMap[criSandboxStat.Attributes.Id]
				if !found {
					continue
				}
				ps := buildPodStats(podSandbox)
				addCRIPodCPUStats(ps, criSandboxStat)
				addCRIPodMemoryStats(ps, criSandboxStat)
				result = append(result, *ps)
			}
			return result, err
		}
		// Call failed, why?
		s, ok := status.FromError(err)
		// Legitimate failure, rather than the CRI implementation does not support ListPodSandboxStats.
		if !ok || s.Code() != codes.Unimplemented {
			return nil, err
		}
		// CRI implementation doesn't support ListPodSandboxStats, warn and fallback.
		klog.ErrorS(err,
			"CRI implementation must be updated to support ListPodSandboxStats if PodAndContainerStatsFromCRI feature gate is enabled. Falling back to populating with cAdvisor; this call will fail in the future.",
		)
	}

	resp, err := p.runtimeService.ListContainerStats(ctx, &runtimeapi.ContainerStatsFilter{})
	if err != nil {
		return nil, fmt.Errorf("failed to list all container stats: %v", err)
	}

	allInfos, err := getCadvisorContainerInfo(p.cadvisor)
	if err != nil {
		return nil, fmt.Errorf("failed to fetch cadvisor stats: %v", err)
	}
	caInfos, allInfos := getCRICadvisorStats(allInfos)

	for _, stats := range resp {
		containerID := stats.Attributes.Id
		container, found := containerMap[containerID]
		if !found {
			continue
		}

		podSandboxID := container.PodSandboxId
		podSandbox, found := podSandboxMap[podSandboxID]
		if !found {
			continue
		}

		// Creates the stats of the pod (if not created yet) which the
		// container belongs to.
		ps, found := sandboxIDToPodStats[podSandboxID]
		if !found {
			ps = buildPodStats(podSandbox)
			sandboxIDToPodStats[podSandboxID] = ps
		}

		// 这部分实际效果是 cAdvisor stats 覆盖了 CRI CPU 和 Memory Stats，是个 bug，并不符合 KEP 设计。
		// Issue： https://github.com/kubernetes/kubernetes/issues/107172
		// 修复 PR： https://github.com/kubernetes/kubernetes/pull/110897  但是社区怕影响使用，暂未合并
		// Fill available CPU and memory stats for full set of required pod stats
		cs := p.makeContainerCPUAndMemoryStats(stats, container)
		p.addPodCPUMemoryStats(ps, types.UID(podSandbox.Metadata.Uid), allInfos, cs)

		// If cadvisor stats is available for the container, use it to populate
		// container stats
		caStats, caFound := caInfos[containerID]
		if !caFound {
			klog.V(4).InfoS("Unable to find cadvisor stats for container", "containerID", containerID)
		} else {
			p.addCadvisorContainerCPUAndMemoryStats(cs, &caStats)
		}
		ps.Containers = append(ps.Containers, *cs)
	}
	// cleanup outdated caches.
	p.cleanupOutdatedCaches()

	for _, s := range sandboxIDToPodStats {
		result = append(result, *s)
	}
	return result, nil
}
```

# 结束语
本篇文章中，我们主要了解了 Kubelet 是怎么实现 `/stats/summary` 端点的，以对外提供的监控 API 中统计类数据，
并了解到容器监控的部分主要由 Stats Provider 定义。现在默认是使用 cAdvisor 和 CRI 混杂模式获取统计数据。
