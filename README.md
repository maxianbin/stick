#Stickpackage


###  StickPackage，NodeJs的TCP中的粘包、分包问题的解决方案！

配置介绍

1. 本类库提供对TCP粘包处理的解决方案
2. 本库默认缓冲512个字节，当接收数据超过512字节，自动以512倍数扩大缓冲空间
3. 本库默认采用包头两个字节表示包长度
4. 本库默认采用大端接模式接收数据
5. 本库可以配置自定义包头长度[后期迭代]
6. 本头可以配置大端小端读取[后期迭代]

安装
```
npm i stickpackage
```

源码地址

[喜欢的话请点star，想订阅点watch](https://github.com/lvgithub/stickPackage.git)

---

案例分析
* 案例一:一次接受到两个完整的数据包

测试数据包
```
let bytes3 = Buffer.from([0x00, 0x02, 0x66, 0x66, 0x00, 0x04, 0x88, 0x02, 0x11, 0x11]);
```
测试结果
```
[fly@fly Stickpackage]$ node stickTest.js 
log:传入两个包,一次Put[验证一次性Put数据包]
receive data,length:4
receive data,contents:{"type":"Buffer","data":[0,2,102,102]}
receive data,length:6
receive data,contents:{"type":"Buffer","data":[0,4,136,2,17,17]}
```
* 案例二:接收第一包全包+第二包部分数据，第二次接收第二包剩余数据


测试数据包
```
let bytes4 = Buffer.from([0x00, 0x02, 0x66, 0x66, 0x00, 0x04, 0x88, 0x02, 0x11]);
let bytes5 = Buffer.from([0x11]);

```
测试结果
```
log:传入两个包,分两次Put[验证分两次Put数据包]
receive data,length:4
receive data,contents:{"type":"Buffer","data":[0,2,102,102]}
receive data,length:6
receive data,contents:{"type":"Buffer","data":[0,4,136,2,17,17]}
```
* 案例三:数据刚好填满缓冲

测试数据包
```
let bytes6 = Buffer.from([0x01, 0xfe]);
let bytes7 = Buffer.alloc(510).fill(33);
```
测试结果
```
传入512个字节的数据包[验证缓冲全满情况]
receive data,length:512
receive data,contents:{"type":"Buffer","data":[1,254,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33]}
```

* 案例四:数据超过满缓冲空间，自动扩增缓冲

测试数据包
```
let bytes8 = Buffer.from([0x01, 0xff]);
let bytes9 = Buffer.alloc(511).fill(33);
```

测试结果
```
传入513个字节的数据包[验证缓冲扩增情况]
receive data,length:513
receive data,contents:{"type":"Buffer","data":[1,255,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33,33]}
```


[源码地址，喜欢的话请点star，想订阅点watch](https://github.com/lvgithub/stickPackage.git)