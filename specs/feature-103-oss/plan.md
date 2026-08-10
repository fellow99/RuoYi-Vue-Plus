# feature-103-oss 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-103-oss | 最后更新：2026-08-10

## 1. 技术上下文
- AWS S3 SDK (software.amazon.awssdk:s3 + s3-transfer-manager), S3 异步客户端
- MinIO / RustFS 作为默认实现，兼容所有 S3 协议云存储
- OssFactory 工厂模式 + ConcurrentHashMap 客户端缓存 + ReentrantLock 线程安全
- 配置缓存：Redis 缓存默认配置 key (sys_oss:default_config) + CacheUtils 缓存各配置

## 2. 模块结构
- ruoyi-common/ruoyi-common-oss: 18 个 Java 文件（client/config/enums/factory/model/properties/util）
- ruoyi-system: SysOssController(/resource/oss) + SysOssConfigController(/resource/oss/config) + Service + Mapper + Entity
- ruoyi-system 数据库表: sys_oss, sys_oss_config

## 3. 控制器清单

| Controller | 路径 | 用途 |
|------------|------|------|
| SysOssController | /resource/oss | OSS 对象管理（分页查询/上传/下载/删除） |
| SysOssConfigController | /resource/oss/config | OSS 配置管理（CRUD/状态切换） |

## 4. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-oss/client/OssClient.java | S3 操作统一接口（upload/download/delete/presign 等 60+方法） |
| ruoyi-common-oss/client/DefaultOssClientImpl.java | S3 客户端默认实现 |
| ruoyi-common-oss/client/AbstractOssClientImpl.java | S3 客户端抽象基类 |
| ruoyi-common-oss/factory/OssFactory.java | 客户端工厂（缓存+锁+动态切换） |
| ruoyi-common-oss/config/OssClientConfig.java | 客户端配置类 |
| ruoyi-common-oss/config/OssAsyncExecutorConfig.java | 异步线程池配置 |
| ruoyi-common-oss/config/Config.java | 配置接口定义 |
| ruoyi-common-oss/config/AccessControlPolicyConfig.java | 访问策略配置 |
| ruoyi-common-oss/properties/OssProperties.java | YAML 配置属性 |
| ruoyi-common-oss/enums/AccessPolicy.java | 访问策略枚举（PRIVATE/PUBLIC_READ_WRITE/PUBLIC_READ） |
| ruoyi-common-oss/constant/OssConstant.java | OSS 常量（默认配置 key / 系统数据 ID / 云服务商列表） |
| ruoyi-common-oss/exception/S3StorageException.java | S3 存储异常 |
| ruoyi-common-oss/model/PutObjectResult.java | 上传返回值 |
| ruoyi-common-oss/model/GetObjectResult.java | 下载返回值 |
| ruoyi-common-oss/model/HandleAsyncResult.java | 异步操作结果 |
| ruoyi-common-oss/model/Options.java | 操作选项 |
| ruoyi-common-oss/io/OutputStreamDownloadSubscriber.java | 输出流下载订阅器 |
| ruoyi-common-oss/util/BucketUrlUtil.java | 存储桶 URL 工具 |
| ruoyi-system/controller/system/SysOssController.java | OSS 对象控制器 |
| ruoyi-system/controller/system/SysOssConfigController.java | OSS 配置控制器 |
| ruoyi-system/service/ISysOssService.java | OSS 对象服务接口 |
| ruoyi-system/service/ISysOssConfigService.java | OSS 配置服务接口 |
| ruoyi-system/mapper/SysOssMapper.java | OSS 对象 Mapper |
| ruoyi-system/mapper/SysOssConfigMapper.java | OSS 配置 Mapper |
