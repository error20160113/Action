## Timer  
schedule的四种用法见API  

* public void schedule(TimerTask task,long delay) 
* public void schedule(TimerTask task,Date time)
* public void schedule(TimerTask task,long delay,long period)
* public void schedule(TimerTask task, Date firstTime,long period)  

  
scheduleAtFixedRate两种用法见API  

schedule和scheduleAtFixedRate区别  

* 首次执行时间小于当前时间：schedule会以当前时间为准；scheduleAtFixedRate会追赶进度，TimerTask需要考虑同步
* 任务执行时间超出执行周期间隔：schedule会以上一次执行完成时间点为准，因此执行时间会不断延后；scheduleAtFixedRate会以上一次执行开始时间为准，不会延后，存在并发性

Timer的缺点  

* 管理并发任务缺陷：Timer仅有一个线程去执行定时任务，串行执行，不支持并发，如果多个任务且时间过长会出现效果与预期不符
* 任务抛出异常时缺陷：抛出RunTimeException时Timer会停止所有任务的执行


## Quartz  

* Job：实现业务逻辑的任务接口，只有一个execute方法，相当于TimmerTask的run方法，里面写业务逻辑
* JobDetail：描述Job实例的详细信息。name--job名称,group--job组默认为default,jobClass--job实现类,jobDataMap--存储Job实例的状态信息，调度器使用这些信息添加Job实例

JobExecutionContext  

* 当scheduler调用一个job，就会将JobExecutionContext传递给job的execute方法
* Job能通过JobExecutionContext访问Quartz运行环境以及Job的明细数据

JobDataMap

* 进行任务调度时JobDataMap存储在JobExecutionContext中，获取非常方便
* JobDataMap可以装载任何可序列化的对象，当job实例对象被执行时这些参数对象会传递给他
* JobDataMap实现了JDK的Map接口
* getJobDetail.getJobDataMap()和getTrigger().getJobDataMap()

Trigger 
 
* quartz中的触发器，用来告诉调度程序作业什么时候出发，即trigger是用来出发job的  
* SimpleTirgger和CronTrigger
 
CronTrigger 基于日历的作业调度器  
Cron表达式 [秒][分][时][日][月][周][年]  
![Alt text](/image/cron1.png "cron表达式")      

![Alt text](/image/cron2.png "cron表达式举例")   
  
![Alt text](/image/cron3.png "cron通配符") 

quartz.properties:优先读取工程目录下，读不到则读取jar包    
  
  
Spring需要配置JobDetail、Trigger、Scheduler
  
1. Job：需要处理的事情
2. Trigger：触发时间点，什么时候去调。
3. Scheduler：调度器

JobDetail与Trigger是一对一的关系，Scheduler与Trigger是一对多的关系。  
  
	<bean id="cronJobDetail" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
		<property name="jobClass" value="com.zy.springquartz.quartz.FirstScheduledJob" />
		<property name="jobDataMap">
			<map>
				<entry key=" " value-ref="要传给jobDataMap的POJO" />
			</map>
		</property>				
	</bean>
	
	<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
	    <property name="jobDetail"  ref="cronJobDetail"/>
	    <property name="cronExpression"  value="0/5 * * ? * *"/>
	</bean>
	
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
	    <property name="jobDetails">
	        <list>
	            <ref bean="cronJobDetail"/>
	        </list>
	    </property>
	    <property name="triggers">
	        <list>
	            <ref bean="cronTrigger"/>
	        </list>
	    </property>
	</bean>  
	
JobDetail与Trigger是一对一的关系，Scheduler与Trigger是一对多的关系。
  
 

