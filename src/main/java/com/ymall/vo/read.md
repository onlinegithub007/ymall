值对象模型

VO，值对象(Value Object)，PO，持久对象(Persisent Object)，它们是由一组属性和属性的get和set方法组成。从结构上看，它们并没有什么不同的地方。但从其意义和本质上来看是完全不同的。

１．VO是用new关键字创建，由GC回收的。

PO则是向数据库中添加新数据时创建，删除数据库中数据时削除的。并且它只能存活在一个数据库连接中，断开连接即被销毁。

２．VO是值对象，精确点讲它是业务对象，是存活在业务层的，是业务逻辑使用的，它存活的目的就是为数据提供一个生存的地方。

PO则是有状态的，每个属性代表其当前的状态。它是物理数据的对象表示。使用它，可以使我们的程序与物理数据解耦，并且可以简化对象数据与物理数据之间的转换。

３．VO的属性是根据当前业务的不同而不同的，也就是说，它的每一个属性都一一对应当前业务逻辑所需要的数据的名称。

PO的属性是跟数据库表的字段一一对应的。

PO对象需要实现序列化接口。

VO是独立的Java Object。

PO是由hibernate纳入其实体容器（Entity Map）的对象，它代表了与数据库中某条记录对应的Hibernate实体，PO的变化在事务提交时将反应到实际数据库中。如果一个PO与Session对应的实体容器中分离（如Session关闭后的PO），那么此时，它又会变成一个VO。

由Hibernate VO和Hibernate PO的概念，又引申出一些系统层次设计方面的问题。如在传统的MVC架构中，位于Model层的PO，是否允许被传递到其他层面。由于PO的更新最终将被映射到实际数据库中，如果PO在其他层面（如View层）发生了变动，那么可能会对Model 层造成意想不到的破坏。

因此，一般而言，应该避免直接PO传递到系统中的其他层面，一种解决办法是，通过一个VO，通过属性复制使其具备与PO相同属性值，并以其为传输媒质（实际上，这个VO被用作Data Transfer Object，即所谓的DTO），将此VO传递给其他层面以实现必须的数据传送。

VO经过Hibernate进行处理，就变成了PO。

session.save(user)中，我们把一个VO “user”传递给Hibernate的Session.save方法进行保存。在save方法中，Hibernate对其进行如下处理：

1．在当前session所对应的实体容器（Entity Map）中查询是否存在user对象的引用。

2．如果引用存在，则直接返回user对象id，save过程结束. Hibernate中，针对每个Session有一个实体容器（实际上是一个Map对象），如果此容器中已经保存了目标对象的引用，那么hibernate会认为此对象已经 与Session相关联。

对于save操作而言，如果对象已经与Session相关联（即已经被加入Session 的实体容器中），则无需进行具体的操作。因为之后的Session.flush过程中，Hibernate会对此实体容器中的对象进行遍历，查找出发生变化的实体，生成并执行相应的update语句。

3．如果引用不存在，则根据映射关系，执行insert操作。

a) 在我们这里的示例中，采用了native的id生成机制，因此hibernate会从数据库取得insert操作生成的id并赋予user对象的id属性。

b) 将user对象的引用纳入Hibernate的实体容器。

c) save过程结束，返回对象id.

而Session.load方法中，再返回对象之前，Hibernate就已经将此对象纳入其实体容器中。

最后看一张领域模型的系统装化图： 
领域模型的系统装化图