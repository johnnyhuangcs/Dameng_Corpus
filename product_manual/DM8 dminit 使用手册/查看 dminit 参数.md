

# 查看 dminit 参数

dminit 使用较为灵活，参数较多。用户可使用“dminit HELP”快速查看 dminit 版本信息和各参数信息。

在安全版本和非安全版本中，dminit help 看到的参数略有不同。下面展示的为安全版本中 help 信息。

建库完成后，在 DM 服务器运行期间，可以通过查询 V$DM_INI 动态视图查看建库参数的具体信息。

```
./dminit help
initdb V8
db version: 0x7000c
License will expire on 2025-03-25
version: 05134284325-20240927-244391-10000
格式: ./dminit     KEYWORD=value

例程: ./dminit     PATH=/public/dmdb/dmData PAGE_SIZE=16

关键字                     说明（默认值）
--------------------------------------------------------------------------------
INI_FILE                   初始化文件dm.ini存放的路径
PATH                       初始数据库存放的路径
CTL_PATH                   控制文件路径
LOG_PATH                   日志文件路径
EXTENT_SIZE                数据文件使用的簇大小(16)，可选值：16, 32, 64，单位：页
PAGE_SIZE                  数据页大小(8)，可选值：4, 8, 16, 32，单位：K
LOG_SIZE                   日志文件大小(2048)，单位为：M，范围为：256M ~ 8G
CASE_SENSITIVE             大小敏感(Y)，可选值：Y/N，1/0
CHARSET/UNICODE_FLAG       字符集(0)，可选值：0[GB18030]，1[UTF-8]，2[EUC-KR]
SEC_PRIV_MODE              权限管理模式(0)，可选值：0[TRADITION]，1[BMJ]，2[EVAL]，3[BAIST]，4[ZBMM]
SYSDBA_PWD                 设置SYSDBA密码
SYSAUDITOR_PWD             设置SYSAUDITOR密码
DB_NAME                    数据库名(DAMENG)
INSTANCE_NAME              实例名(DMSERVER)
PORT_NUM                   监听端口号(5236)
BUFFER                     系统缓存大小(8000)，单位M
TIME_ZONE                  设置时区(+08:00)
PAGE_CHECK                 页检查模式(3)，可选值：0/1/2/3
PAGE_HASH_NAME             设置页检查HASH算法
EXTERNAL_CIPHER_NAME       设置默认加密算法
EXTERNAL_HASH_NAME         设置默认HASH算法
EXTERNAL_CRYPTO_NAME       设置根密钥加密引擎
RLOG_ENCRYPT_NAME          设置日志文件加密算法，若未设置，则不加密
RLOG_POSTFIX_NAME          设置日志文件后缀名，长度不超过10。默认为log，例如DAMENG01.log
USBKEY_PIN                 设置USBKEY PIN
PAGE_ENC_SLICE_SIZE        设置页加密分片大小，可选值：0、512、4096，单位：Byte
ENCRYPT_NAME               设置全库加密算法
BLANK_PAD_MODE             设置空格填充模式(0)，可选值：0/1
SYSTEM_MIRROR_PATH         SYSTEM数据文件镜像路径
MAIN_MIRROR_PATH           MAIN数据文件镜像
ROLL_MIRROR_PATH           回滚文件镜像路径
MAL_FLAG                   初始化时设置dm.ini中的MAL_INI(0)
ARCH_FLAG                  初始化时设置dm.ini中的ARCH_INI(0)
MPP_FLAG                   Mpp系统内的库初始化时设置dm.ini中的mpp_ini(0)
CONTROL                    初始化配置文件（配置文件格式见系统管理员手册）
AUTO_OVERWRITE             是否覆盖所有同名文件(0) 0:不覆盖 1:部分覆盖 2:完全覆盖
USE_NEW_HASH               是否使用改进的字符类型HASH算法(1)
ELOG_PATH                  指定初始化过程中生成的日志文件所在路径
AP_PORT_NUM                分布式环境下协同工作的监听端口
HUGE_WITH_DELTA            是否仅支持创建事务型HUGE表(1) 1:是 0:否
RLOG_GEN_FOR_HUGE          是否生成HUGE表REDO日志(1) 1:是 0:否
PSEG_MGR_FLAG              是否仅使用管理段记录事务信息(0) 1:是 0:否
CHAR_FIX_STORAGE           CHAR是否按定长存储(N)，可选值：Y/N，1/0
SQL_LOG_FORBID             是否禁止打开SQL日志(N)，可选值：Y/N，1/0
DPC_MODE                   指定DPC集群中的实例角色(0) 0:无 1:MP 2:BP 3:SP，取值1/2/3时也可以用MP/BP/SP代替
USE_DB_NAME                路径是否拼接DB_NAME(1) 1:是 0:否
MAIN_DBF_PATH              MAIN数据文件存放路径
SYSTEM_DBF_PATH            SYSTEM数据文件存放路径
ROLL_DBF_PATH              ROLL数据文件存放路径
TEMP_DBF_PATH              TEMP数据文件存放路径
ENC_TYPE                   数据库内部加解密使用的加密接口类型(1), 可选值: 1: 优先使用EVP类型 0: 不启用EVP类型
RANDOM_CRYPTO              随机数算法所在加密引擎名
DPC_TENANCY                指定DPC集群是否启用多租户模式(0) 0:不启用 1:启用，取值0/1时也可以用FALSE/TRUE代替
SYSSSO_PWD                 设置SYSSSO密码
SYSDBO_PWD                 设置SYSDBO密码
PRIV_FLAG                  设置权限标记(0)，可选值：0[三权分立]，1[四权分立]
ENV_LABEL                  设置是否启用环境标记功能(0)，可选值：1/0
ENABLE_MAC                 是否打开强制访问控制功能(0)，仅对安全版有效，可选值： 1: 打开 0: 不打开
HELP                       打印帮助信息
```
