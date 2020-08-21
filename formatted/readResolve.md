
# Backlinks
## [单例](<单例.md>)
- 同样存在使用反射进行攻击的问题,还有一个问题就是,在序列化的时候,仅仅在声名中加上“implement Serializable”是不够的。为了维护并保证Singleton，必须声名所有的实例都是[transient](<transient.md>)，并提供一个[readResolve](<readResolve.md>)方

