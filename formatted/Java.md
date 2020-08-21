- 分层[领域模型](<领域模型.md>)规约：
    - [DO（ Data Object）](<DO（ Data Object）.md>)：与数据库表结构一一对应，通过DAO层向上传输数据源对象。
    - [DTO（ Data Transfer Object）](<DTO（ Data Transfer Object）.md>)：数据传输对象，Service或Controller向外传输的对象。
    - [BO（ Business Object）](<BO（ Business Object）.md>)：业务对象。 由Service层输出的封装业务逻辑的对象。
    - [AO（ Application Object）](<AO（ Application Object）.md>)：应用对象。 在Web层与Service层之间抽象的复用对象模型，极为贴近展示层，复用度不高。
    - [VO（ View Object）](<VO（ View Object）.md>)：显示层对象，通常是Web向模板渲染引擎层传输的对象。
    - [POJO（ Plain Ordinary Java Object）](<POJO（ Plain Ordinary Java Object）.md>)：在本手册中， POJO专指只有setter/getter/toString的简单类，包括DO/DTO/BO/VO等。
    - [Query](<Query.md>)：数据查询对象，各层接收上层的查询请求。 注意超过2个参数的查询封装，禁止使用Map类来传输。

# Backlinks
## [Areas](<Areas.md>)
- [Java](<Java.md>)

## [Study](<Study.md>)
- [Java](<Java.md>)

