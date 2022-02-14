- Mysql [coalesce](<coalesce.md>)()函数 主要用来返回第一个非空参数;
    - select coalesce(a,b,c);
    - 如果a==null,则选择b；如果b==null,则选择c；如果a!=null,则选择a；如果a b c 都为null ，则返回为null（没意义）。
- [京东中间件](<京东中间件.md>)
    - [R2M](<R2M.md>)版本
        - [r2m-web](<r2m-web.md>)
            - 版本:4.0.2
            - 描述： r2m 管控台
        - [r2m-3c](<r2m-3c.md>)
            - 版本:1.0.4
            - 描述： r2m客户端配置中心
        - [r2m-manager](<r2m-manager.md>)
            - 版本:2.3.0
            - 描述： manager集中管控，负责agent管理，agent与web通信
            - 本地编译
                - ```shell
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags "-X r2m_manager/version.Buildstamp=`date '+%Y-%m-%dT%H:%M:%S-%Z'` -X r2m_manager/version.Githash=`git rev-parse HEAD` -X r2m_manager/version.Version=`git rev-parse --abbrev-ref HEAD` " -o output/bin/r2m_manager ./cmd/r2m_manager
```
            - 
        - [r2m-proxy](<r2m-proxy.md>)
            - 版本:1.5.6
            - 描述： 代理模式访问集群中间件
        - [r2m-port](<r2m-port.md>)
            - 版本:1.6.5
            - 描述： R2M数据迁移工具
        - [r2m-agent](<r2m-agent.md>)
            - 版本:3.0.0
            - 描述： 单机管控
            - 打包172.24.2.13服务器
            - ```shell
sudo su
su - dev
/export/project/lxyer
sh build.sh ex
```
- go 的指针:
    - 类型 *T 是指向 T 类型值的指针。其零值为 nil。
    - & 操作符会生成一个指向其操作数的指针。 &先套上,与指针进行关联
    - * 操作符表示指针指向的底层值。  *下钻操作指针对应的底层的值
    - ```c++
    i, j := 42, 2701

	p := &i         // 指向 i
	fmt.Println(*p) // 通过指针读取 i 的值
	*p = 21         // 通过指针设置 i 的值
	fmt.Println(i)  // 查看 i 的值

	p = &j         // 指向 j
	*p = *p / 37   // 通过指针对 j 进行除法运算
	fmt.Println(j) // 查看 j 的值```
