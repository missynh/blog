---
title: LTS浅谈 
date: 2017-07-22  
tags: lts
---

由于近期的项目中需要使用的定时任务，所以总结一下我对lts的理解。可能有些理解偏差和不到位的地方，希望各位大神指导
#### 什么是LTS

LTS(light-task-scheduler)主要用于解决分布式任务调度问题，支持实时任务，定时任务和Cron任务。
github地址: https://github.com/ltsopensource/light-task-scheduler 

该框架主要有四个节点：

JobClient：主要负责提交任务, 并接收任务执行反馈结果。
JobTracker：负责接收并分配任务，任务调度。
TaskTracker：负责执行任务，执行完反馈给JobTracker。
Monitor：（管理后台）主要负责节点管理，任务队列管理，监控管理等。

<!--more-->
LTS支持任务类型：

实时任务：提交了之后立即就要执行的任务。
定时任务：在指定时间点执行的任务，譬如 今天3点执行（单次）。
Cron任务：CronExpression，和quartz类似（但是不是使用quartz实现的）譬如 0 0/1 * * * ?

管理后台默认用户名密码admin

#### JobTracker部署

clone项目的源码，运行根目录下的sh build.sh或build.cmd脚本，会在dist目录下生成lts-{version}-bin文件夹。 
或者直接解压dist下面原有的lts-1.6.8-beta1-bin.zip，也能实现同样效果，本人使用了第二种方法。

修改生成的或解压的文件夹中conf/zoo文件夹下的配置文件，修改为实际使用ZooKeeper和MySQL的配置

在生成的或解压的文件夹中执行 sh jobtracker.sh zoo start 即可启动JobTracker

#### JobClient使用

Spring XML方式启动

``` bash
<bean id="jobClient" class="com.github.ltsopensource.spring.JobClientFactoryBean" init-method="start">
        <property name="clusterName" value="test_cluster" />
        <property name="registryAddress" value="zookeeper://127.0.0.1:2181" />
        <property name="nodeGroup" value="test_jobClient" />
        <property name="jobCompletedHandler">
            <bean class="com.isesol.example.task.job.JobCompletedHandlerImpl" />
        </property>
        <property name="configs">
            <props>
                <prop key="job.fail.store">leveldb</prop>
            </props>
        </property>
        <property name="dataPath" value="/tmp" />
    </bean>
```

#### TaskTracker使用

自己编写一个任务执行类，实现JobRunner接口，在run方法中实现自己的逻辑即可

``` bash
public class UpdateCapacityManageService extends JobRunner {

    @Override
    public Result run(JobContext jobContext) throws Throwable {

        log.info("定时任务-->start");

        try {
            //自己的业务代码
        } catch (Exception e) {

            log.error("定时任务失败！", e);
            return new Result(Action.EXECUTE_FAILED, "Schedule Job error!");
        }

        log.info("定时任务-->end");
        return new Result(Action.EXECUTE_SUCCESS, "Schedule Job success!");
    }
}
```

Spring XML方式启动

``` bash
<bean id="taskTracker" class="com.github.ltsopensource.spring.TaskTrackerAnnotationFactoryBean" init-method="start">
    <property name="jobRunnerClass" value="com.isesol.example.task.MyJobRunner"/>
    <property name="bizLoggerLevel" value="INFO"/>
    <property name="clusterName" value="test_cluster"/>
    <property name="registryAddress" value="zookeeper://127.0.0.1:2181"/>
    <property name="nodeGroup" value="test_trade_TaskTracker"/>
    <property name="workThreads" value="20"/>
    <property name="configs">
        <props>
            <prop key="job.fail.store">leveldb</prop>
        </props>
    </property>
</bean>
```

#### 一个TaskTracker执行多种任务

一般一个系统中往往不止一个任务，需要使用LTS对多个任务进行调度。
创建一个集中的任务调度器，该bean在启动时将IOC容器中所有JobRunner取出放入一个MAP中，当该JOB被LTS调用时根据参数判断具体调度那个任务

``` bash
public class JobRunnerDispatcher implements JobRunner {

    private static final ConcurrentHashMap<String/*type*/, JobRunner>
            JOB_RUNNER_MAP = new ConcurrentHashMap<String, JobRunner>();

    static {
        JOB_RUNNER_MAP.put("aType", new JobRunnerA()); // 也可以从Spring中拿
        JOB_RUNNER_MAP.put("bType", new JobRunnerB());
    }

    @Override
    public Result run(JobContext jobContext) throws Throwable {
        Job job = jobContext.getJob();
        String type = job.getParam("type");
        return JOB_RUNNER_MAP.get(type).run(job);
    }
}
```

