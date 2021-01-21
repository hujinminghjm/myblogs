# Service和ServiceImpl的使用

Service层 = Service接口 + ServiceImpl 实现类
Service 接口层就是是端口，ServiceImpl 实现类里不同的方法就是 线路1、线路2甚至是线路3
Spring 官方推荐也是这种模式

Controller层推荐是注入service层

Service层都是定义的接口，ServiceImpl是实现类，继承接口
1. 当此时只有一个实现类时，其实可以直接使用@Autowired注解引入，因为默认是按照类型引入，会直接引入实现类，其实此时定义@Service("实现类的类名，去掉impl，首字母小写")service的名字，其实也没有什么作用，感觉可能是为了后续有多个实现类时的名称规范
2. 当此时有多个实现类时，此时用@Autowired注解引入会报错，此时有两种方式
- @Autowired，@Qualifier("实现类的类名，首字母小写")
- @Resource(name = "实现类的类名，首字母小写")
如果此时在实现类的中，定义了@Service("名字")，那么注入的时候就要采用定义的名字

另外因为感觉实现类后面都有一个impl，此时可能从名字上不是很好看，所以感觉在实现类中定义@Service("实现类的类名，去掉impl，首字母小写")，感觉会更规范些