组件 - 为其他产品提供能力 - License签名+验证 包括激活 校验 紧急 售前试用
license安全算法整改
添加了双秘钥 + 改进过度license签名算法RSA2048-SHA1 到 RSA3072-SHA256
SHA1属于过时的安全性较低的加密算法
采用双签名License的解决方案

License文件规范升级后 采用新的XML格式去显示双标签 License文件包含了客户销售以及公共信息

老标签含有公司签名和产品签名 | 新标签是用来描述另外一对公司签名和产品签名

VCG: 运行在公司ESDP IT系统的插件 用于给license签名
单签名

# 双签名
ESDP传入4个秘钥对和待签名的License 给VCG
秘钥对A RSA2048公司秘钥对
秘钥对B RSA2048产品秘钥对
秘钥对C RSA3072公司秘钥对
秘钥对D RSA3072产品秘钥对
待签名LicenseA

1. 生成产品签名
计算LicenseA的哈希值 使用秘钥对D对哈希值进行不对称私钥加密 得到加密字符串 将字符串作为标签值调到LicenseA得到LicenseB
2. 生产公司签名 
计算LcienseB的哈希值....
....
得到最终的License就是最终ESDP下发的双签名License

# SHA加密算法
one way encryption.
哈希函数将信息或数据压缩成摘要 再讲数据打乱混合 重新创建一个散列值 通常用一个随机字母和数字来代表
SHA256会产生一个256bit长度的哈希值 称为信息摘要

# RSA签名算法 公钥加密算法
模运算是一种 单向计算容易 逆向反推很难 的求余运算。当N值很大时 反推是不现实的 所以也被称为单向函数(one-way function)
(message ^ encrypt key) mod N = cipher 加密过程
(cipher ^ decrypt key) mod N = message 解密过程
N是公钥 是对所有人公开的信息

message ^ (encrypt key * decrypt key) mod N = message  (1)
如何选择这里的e和d成为了关键

Euler's Theorem
message ^ PHI(n) = 1 mod n 
PHI(n) -> 小于或等于n的正整数中 有多少个与n互质的数 (Phi(6) = 2 (1 and 5)) {质因数分解}
message ^ (k * phi(n)) * m = 1 * m (2)

结合 (1) 和 (2):
ed = k*PHI(n) + 1
- 对于任何质数 p => PHI(p) = p - 1
- 对于两个互为质数的p和q PHI(p * q) = PHI(q) * PHI(p)
PHI(17 * 23) = PHI(391) = 352 
    p     q         n
选取k值和秘钥e值算出私钥d 此时p和q就不需要了 
比如: (k = 5, e = 3, 算出d)
e和n会被公布作为加密时用的公钥(public key)
d会被保存下来作为解密时用的私钥(private key)
由于其他人是不知道p和q这两个关键的质因数 是无法得到PHI(n)的 RSA利用信息不对等 
让加密者能够快速构造出一个PHI(N) 而其他人无法在有限时间内求解 大数的质因数分解是非常困难的事情(computational infeasible)
