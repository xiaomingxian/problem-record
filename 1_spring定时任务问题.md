# 定时任务相关 springboot 定时任务默认是单线程 一个任务处于循环状态 会阻塞其他任务
<br/>改进1 不能使其无限循环 得加重试次数
<br/>改进2 不阻塞其他定时任务:
```
# 配置
@Configuration
@EnableAsync
public class ExecutorConfig {

    @Bean
    public Executor executor1() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setThreadNamePrefix("schedule1-");
        executor.setMaxPoolSize(20);
        executor.setCorePoolSize(15);
        executor.setQueueCapacity(0);
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        return executor;
    }
    @Bean
    public Executor executor2() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setThreadNamePrefix("schedule2-");
        executor.setCorePoolSize(15);
        executor.setMaxPoolSize(20);
        executor.setQueueCapacity(0);
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        return executor;
    }
}
```


```
#定时任务1

    @Async("executor1")
    @Scheduled(cron = "0/30 * * * * ?")
    public void distributeBulletWorkOrder(){...}
```

```
#定时任务2
    @Async("executor2")
    @Scheduled(cron ="0/10 * * * * ?")
    public void warningBulletWorkOrder(){...}
```
