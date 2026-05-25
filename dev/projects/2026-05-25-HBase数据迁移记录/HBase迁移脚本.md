---
title: HBase迁移脚本
type: project
tags:
  - HBase
  - Python
status: in-progress
sources: []
---

```Python
# 作用：hbase快照迁移表（兼容目标端Kerberos/无认证）
# 创建者：唐志强
# 创建时间：2024-09-12 10：00
# 联系方式：1317667746@qq.com

import subprocess
import logging
import paramiko
from concurrent.futures import ThreadPoolExecutor, as_completed

# ===================== 【配置区：全部改成你自己的】 =====================
# 日志
LOG_FILE = 'hbase_migration.log'
logging.basicConfig(
    level=logging.INFO,
    filename=LOG_FILE,
    filemode='a',
    format='%(asctime)s - %(levelname)s - %(message)s'
)

# HBase 配置
HBASE_SHELL_CMD = "hbase shell -n"
HBASE_EXPORT_CMD = "hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot"
HBASE_QUEUE_NAME = "-D mapreduce.job.queuename=dtsw"
HBASE_SRC_PATH = "hdfs://10.24.137.1:8020/hbase/"
HBASE_DST_PATH = "hdfs://10.24.147.9:54310/hbase/"

# SSH 源集群
SSH_HOST = '10.24.139.126'
SSH_PORT = 2236
SSH_USERNAME = 'dtsw_user'

# 表名文件
TABLE_NAMES_FILE = '/app/tzq/hbasesynctable.txt'

# ===================== 【通用开关：二选一即可】 =====================
# True  = 目标端需要 Kerberos 认证
# False = 目标端不需要认证（原始模式）
NEED_KERBEROS = False

# Kerberos 配置（只有 NEED_KERBEROS=True 时才需要填）
KRB_PRINCIPAL = "dtsw_user@DTAD.COM"
KRB_KEYTAB = "/app/tzq/dtsw_user.keytab"
KRB_KINIT_CMD = f"kinit -kt {KRB_KEYTAB} {KRB_PRINCIPAL}"

# 并发线程数
MAX_WORKERS = 5
# ====================================================================

def kerberos_init():
    """仅当需要 Kerberos 时执行"""
    if not NEED_KERBEROS:
        logging.info("✅ 目标端无需 Kerberos 认证")
        return
    
    try:
        logging.info("正在初始化 Kerberos 认证...")
        subprocess.run(
            KRB_KINIT_CMD,
            shell=True,
            check=True,
            capture_output=True,
            text=True
        )
        logging.info("✅ Kerberos 认证成功！")
    except subprocess.CalledProcessError as e:
        logging.error(f"❌ Kerberos kinit 失败: {e.stderr}")
        raise Exception("Kerberos 认证失败，脚本退出")

def execute_local_command(command):
    """执行本地命令，自动兼容认证/无认证模式"""
    try:
        # 根据开关自动拼接命令
        if NEED_KERBEROS:
            full_cmd = f"{KRB_KINIT_CMD} && {command}"
        else:
            full_cmd = command

        result = subprocess.run(
            full_cmd,
            shell=True,
            check=True,
            text=True,
            capture_output=True
        )
        logging.info(f"本地命令成功: {command}")
        return result.stdout
    except subprocess.CalledProcessError as e:
        logging.error(f"本地命令失败: {command}\n错误: {e.stderr}")
        raise

def execute_ssh_command(command):
    """远程执行创建快照（源集群一般无kerberos）"""
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    try:
        client.connect(SSH_HOST, port=SSH_PORT, username=SSH_USERNAME, timeout=30)
        stdin, stdout, stderr = client.exec_command(command)
        error = stderr.read().decode().strip()

        if error:
            logging.error(f"SSH执行错误: {error}")
            raise Exception(f"远程命令失败: {error}")

        logging.info(f"SSH命令成功: {command}")
        return stdout.read().decode()
    finally:
        client.close()

def execute_hbase_commands(tablename):
    """单表迁移流程"""
    snapshot_name = f"{tablename}_snapshot"
    logging.info(f"开始处理表: {tablename}")

    try:
        # 1. 远程创建快照
        logging.info(f"创建快照: {snapshot_name}")
        create_snapshot_cmd = f"echo \"snapshot 'DTSW:{tablename}', '{snapshot_name}'\" | {HBASE_SHELL_CMD}"
        execute_ssh_command(create_snapshot_cmd)

        # 2. 导出快照到目标集群
        logging.info(f"导出快照 {snapshot_name}")
        export_cmd = (
            f"{HBASE_EXPORT_CMD} {HBASE_QUEUE_NAME} "
            f"-snapshot {snapshot_name} "
            f"-copy-from {HBASE_SRC_PATH} "
            f"-copy-to {HBASE_DST_PATH}"
        )
        execute_local_command(export_cmd)

        # 3. 目标集群禁用表
        logging.info(f"禁用表: DTSW:{tablename}")
        disable_cmd = f"echo \"disable 'DTSW:{tablename}'\" | {HBASE_SHELL_CMD}"
        execute_local_command(disable_cmd)

        # 4. 恢复快照
        logging.info(f"恢复快照: {snapshot_name}")
        restore_cmd = f"echo \"restore_snapshot '{snapshot_name}'\" | {HBASE_SHELL_CMD}"
        execute_local_command(restore_cmd)

        # 5. 启用表
        logging.info(f"启用表: DTSW:{tablename}")
        enable_cmd = f"echo \"enable 'DTSW:{tablename}'\" | {HBASE_SHELL_CMD}"
        execute_local_command(enable_cmd)

        logging.info(f"✅ 表 {tablename} 迁移完成！")

    except Exception as e:
        logging.error(f"❌ 表 {tablename} 迁移失败: {str(e)}")
        raise

def main():
    # 初始化认证（自动判断是否需要）
    kerberos_init()

    # 读取表名
    with open(TABLE_NAMES_FILE, 'r', encoding='utf-8') as f:
        table_names = [line.strip() for line in f if line.strip()]

    logging.info(f"总表数: {len(table_names)}")

    # 多线程并发迁移
    with ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        futures = {executor.submit(execute_hbase_commands, t): t for t in table_names}

        for future in as_completed(futures):
            table = futures[future]
            try:
                future.result()
                print(f"✅ 成功: {table}")
            except Exception as exc:
                print(f"❌ 失败: {table} | {exc}")

if __name__ == "__main__":
    main()
```


