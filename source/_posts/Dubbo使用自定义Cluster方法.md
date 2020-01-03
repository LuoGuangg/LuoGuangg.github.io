---
title: Dubbo使用自定义Cluster方法
date: 2020-01-03 11:43:06
tags: 
	- Dubbo

---

# 一、新增配置文件
    在“\src\main\resources\META-INF\dubbo\”目录下
    新增文件名“com.alibaba.dubbo.rpc.cluster.Cluster ”文件
    加入内容：myCluster=com.demo.text.MyCluster

# 二、编写MyCluster类继承Cluster
```java
package com.demo.filter;

import com.alibaba.dubbo.rpc.Invoker;
import com.alibaba.dubbo.rpc.RpcException;
import com.alibaba.dubbo.rpc.cluster.Cluster;
import com.alibaba.dubbo.rpc.cluster.Directory;
import com.alibaba.dubbo.rpc.cluster.support.BroadcastClusterInvoker;
import com.alibaba.dubbo.rpc.cluster.support.MergeableClusterInvoker;

/**
 * @version V1.0
 * @author: LuoGuang
 * 包名路径需与“com.alibaba.dubbo.rpc.cluster.Cluster”文件配置下内容路径相同
 */
public class MyCluster  implements Cluster {

    @Override
    public <T> Invoker<T> join(Directory<T> directory) throws RpcException {
        return new MyBroadcastClusterInvoker<T>(directory);
    }

}
```
# 三、编写MyBroadcastClusterInvoker自定义策略类
```java
package com.demo.filter;

import com.alibaba.dubbo.common.logger.Logger;
import com.alibaba.dubbo.common.logger.LoggerFactory;
import com.alibaba.dubbo.common.utils.StringUtils;
import com.alibaba.dubbo.rpc.*;
import com.alibaba.dubbo.rpc.cluster.Directory;
import com.alibaba.dubbo.rpc.cluster.LoadBalance;
import com.alibaba.dubbo.rpc.cluster.support.AbstractClusterInvoker;
import com.alibaba.dubbo.rpc.cluster.support.BroadcastClusterInvoker;

import java.util.List;

/**
 * @version V1.0
 * @author: LuoGuang
 * 实现自定义Cluster类逻辑
 */
public class MyBroadcastClusterInvoker <T> extends AbstractClusterInvoker<T> {

    private static final Logger logger = LoggerFactory.getLogger(MyBroadcastClusterInvoker.class);

    public MyBroadcastClusterInvoker(Directory<T> directory) {
        super(directory);
    }

    @Override
    @SuppressWarnings({"unchecked", "rawtypes"})
    public Result doInvoke(final Invocation invocation, List<Invoker<T>> invokers, LoadBalance loadbalance) throws RpcException {
        // 1.查看是否设置了指定ip
        String ip = (String) RpcContext.getContext().get("ip");
        if (StringUtils.isBlank(ip)) {
            throw new RuntimeException("ip is blank ");
        }
        checkInvokers(invokers, invocation);
        RpcContext.getContext().setInvokers((List) invokers);
        // 3.根据指定ip获取对应invoker
        Invoker<T> invoked = invokers.stream().filter(invoker -> invoker.getUrl().toString().contains(ip)).findFirst().orElseThrow(RpcException::new);
        Result result = invoked.invoke(invocation);
        return result;
    }

}

```