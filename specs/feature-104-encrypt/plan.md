# feature-104-encrypt 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-104-encrypt | 最后更新：2026-08-10

## 1. 技术上下文
- 加密引擎：Hutool (AES/RSA) + BouncyCastle (SM2/SM4)
- MyBatis 拦截器（Interceptor）在参数设置/结果集映射阶段介入
- API 加密：Filter 封装 Request/Response Wrapper（DecryptRequestBodyWrapper / EncryptResponseBodyWrapper）
- 动态密钥：每次请求生成随机 AES 密钥，用 RSA 公钥加密后通过 Header 传输

## 2. 模块结构
- ruoyi-common-encrypt: 25 个 Java 文件（annotation/config/core/enums/filter/interceptor/properties/utils）
- annotation: @ApiEncrypt（方法级）+ @EncryptField（字段级）
- core: IEncryptor 接口 + 5 个加密器实现 + EncryptorManager + EncryptContext
- filter: CryptoFilter + 请求/响应 Wrapper
- interceptor: MybatisEncryptInterceptor + MybatisDecryptInterceptor
- 配置自动装配：ApiDecryptAutoConfiguration, EncryptorAutoConfiguration

## 3. 控制器清单（使用方）

| Controller | 路径 | 用途 |
|------------|------|------|
| AuthController | /auth/* | 登录接口加密 |
| SysUserController | /system/user/* | 用户信息加密 |
| SysProfileController | /system/user/profile/* | 个人信息加密 |
| TestEncryptController | /demo/encrypt | Demo 演示 |

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-encrypt/annotation/ApiEncrypt.java | API 加密注解（response 属性） |
| ruoyi-common-encrypt/annotation/EncryptField.java | 字段加密注解（algorithm/password/publicKey/privateKey/encode） |
| ruoyi-common-encrypt/config/EncryptorAutoConfiguration.java | 加密器自动配置 |
| ruoyi-common-encrypt/config/ApiDecryptAutoConfiguration.java | API 解密自动配置 |
| ruoyi-common-encrypt/core/IEncryptor.java | 加密器接口 |
| ruoyi-common-encrypt/core/AbstractEncryptor.java | 加密器抽象基类 |
| ruoyi-common-encrypt/core/EncryptorManager.java | 加密器管理器（注册/获取） |
| ruoyi-common-encrypt/core/EncryptContext.java | 加密上下文 |
| ruoyi-common-encrypt/core/EncryptContextFactory.java | 加密上下文工厂 |
| ruoyi-common-encrypt/core/EncryptedFieldProcessor.java | 加密字段处理器 |
| ruoyi-common-encrypt/core/encryptor/Base64Encryptor.java | BASE64 加密器 |
| ruoyi-common-encrypt/core/encryptor/AesEncryptor.java | AES 加密器 |
| ruoyi-common-encrypt/core/encryptor/RsaEncryptor.java | RSA 加密器 |
| ruoyi-common-encrypt/core/encryptor/Sm2Encryptor.java | SM2 国密加密器 |
| ruoyi-common-encrypt/core/encryptor/Sm4Encryptor.java | SM4 国密加密器 |
| ruoyi-common-encrypt/enums/AlgorithmType.java | 算法类型枚举（DEFAULT/BASE64/AES/RSA/SM2/SM4） |
| ruoyi-common-encrypt/enums/EncodeType.java | 编码类型枚举（DEFAULT/BASE64/HEX） |
| ruoyi-common-encrypt/filter/CryptoFilter.java | API 加解密过滤器 |
| ruoyi-common-encrypt/filter/DecryptRequestBodyWrapper.java | 请求体解密包装器 |
| ruoyi-common-encrypt/filter/EncryptResponseBodyWrapper.java | 响应体加密包装器 |
| ruoyi-common-encrypt/interceptor/MybatisEncryptInterceptor.java | MyBatis 加密拦截器 |
| ruoyi-common-encrypt/interceptor/MybatisDecryptInterceptor.java | MyBatis 解密拦截器 |
| ruoyi-common-encrypt/properties/EncryptorProperties.java | 加密器配置属性 |
| ruoyi-common-encrypt/properties/ApiDecryptProperties.java | API 解密配置属性 |
| ruoyi-common-encrypt/utils/EncryptUtils.java | 加解密工具类 |
