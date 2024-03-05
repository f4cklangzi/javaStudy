
# 开始(getBean）

1. 实例化前：
    - InstantiationAwareBeanPostProcessor->postProcessBeforeInstantiation
    - 执行位置：AbstractAutowireCapableBeanFactory.java->resolveBeforeInstantiation
    - 说明：返回不为null则不再继续后面的扩展点，直接执行InstantiationAwareBeanPostProcessor->postProcessAfterInitialization然后返回bean

# 实例化阶段(Instantiation)

1. 实例化
    - 执行位置：AbstractAutowireCapableBeanFactory.java->createBeanInstance
    - 说明：根据BeanDefinition信息创建出实例(构造方法)

# 属性设置阶段(populate):

1. 实例化后：
    - InstantiationAwareBeanPostProcessor->postProcessAfterInstantiation
    - 执行位置：AbstractAutowireCapableBeanFactory.java->populateBean
    - 说明：返回false不再执行后续扩展点
2. 属性设置：
    - InstantiationAwareBeanPostProcessor->postProcessProperties
    - 执行位置：AbstractAutowireCapableBeanFactory.java->populateBean
    - 说明：@Autowired/@Resource注解设置值
      - AutowiredAnnotationBeanPostProcessor处理@Autowired和@Value
      - CommonAnnotationBeanPostProcessor处理@Resource

# 初始化阶段(Initialization)

1. Aware调用
   - BeanNameAware->BeanClassLoaderAware->BeanFactoryAware
   - 执行位置: AbstractAutowireCapableBeanFactory.java->invokeAwareMethods
2. 初始化前
    - BeanPostProcessor->postProcessBeforeInitialization
    - 执行位置:AbstractAutowireCapableBeanFactory.java->applyBeanPostProcessorsBeforeInitialization
    - 说明：返回的bean会替换之前的bean,CommonAnnotationBeanPostProcessor的父类InitDestroyAnnotationBeanPostProcessor执行@PostConstruct
3. 执行初始化方法
   - 执行初始化方法InitializingBean->afterPropertiesSet
   - 执行自定义初始化方法：initMethod
   - 执行位置：AbstractAutowireCapableBeanFactory.java->invokeInitMethods
4. 初始化后
   - BeanPostProcessor->postProcessAfterInitialization
   - 执行位置：AbstractAutowireCapableBeanFactory.java->applyBeanPostProcessorsAfterInitialization
   - 说明：AbstractAutoProxyCreator代理增强bean
5. Lifecycle开始
   - AbstractApplicationContext.java->start
# 销毁阶段(Destruction)

1. Lifecycle结束
   - DefaultLifecycleProcessor.java->doStart
2. 销毁前调用@PreDestroy
   - InitDestroyAnnotationBeanPostProcessor.java->destroy
3. 销毁
   - DisposableBean->destroy
   - 执行位置：DisposableBeanAdapter.java->destroy
4. 执行自定义销毁方法
   - DisposableBeanAdapter.java->invokeCustomDestroyMethod
