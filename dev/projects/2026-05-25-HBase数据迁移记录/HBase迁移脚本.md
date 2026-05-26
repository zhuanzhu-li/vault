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

import subprocess
import logging
import paramiko
from concurrent.futures import ThreadPoolExecutor, as_completed

def execute_local_command(command):
    """执行本地命令"""
    try:
        result = subprocess.run(
            command,
            shell=True,
            check=True,
            text=True,
            capture_output=True
        )
        logging.info(f"Local command executed successfully: {command}\nOutput: {result.stdout}")
        return result.stdout
    except subprocess.CalledProcessError as e:
        logging.error(f"Error executing local command: {command}\nError: {e.stderr}")

# 配置日志
LOG_FILE = 'hbase_migration.log'
logging.basicConfig(
    level=logging.INFO,
    filename=LOG_FILE,
    filemode='w',
    format='%(asctime)s - %(levelname)s - %(message)s'
)

# 在脚本开始前清空日志文件
with open(LOG_FILE, 'w') as log_file:
    log_file.write('')

# HBase相关配置
HBASE_SHELL_CMD = "hbase shell -n"
HBASE_EXPORT_CMD = "hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot"
HBASE_QUEUE_NAME = "-D mapreduce.job.queuename=dtsw"
HBASE_SRC_PATH = "hdfs://10.24.137.1:8020/apps/hbase/data"
HBASE_DST_PATH = "hdfs://10.24.147.9:54310/apps/hbase/data"

HBASE_HDFS_PATH = "/apps/hbase/data"

# SSH配置
SSH_HOST = '10.24.139.126'
SSH_PORT = 2236
SSH_USERNAME = 'dtsw_user'

# 读取表名文件
TABLE_NAMES_FILE = '/app/tzq/hbasesynctable.txt'


def execute_ssh_command(host, username, command):
    """通过SSH执行命令"""
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    try:
        client.connect(host, port=SSH_PORT, username=username)
        stdin, stdout, stderr = client.exec_command(command)
        output = stdout.read().decode()
        error = stderr.read().decode()
        if error:
            print(f"Error executing command over SSH: {error}")
            logging.error(f"Error executing command over SSH: {error}")
        else:
            print(f"Command executed successfully: {command}")
            logging.info(f"Command executed successfully: {command}")
        return output
    except Exception as e:
        print(f"Failed to execute command over SSH: {e}")
        logging.error(f"Failed to execute command over SSH: {e}")
    finally:
        client.close()


def execute_hbase_commands(tablename):
    """执行HBase相关的命令"""
    snapshot_name = f"{tablename}_snapshot"
    # 创建快照，需要通过SSH远程执行
    create_snapshot_cmd = f"echo \"snapshot 'DTSW:{tablename}', '{snapshot_name}'\" | {HBASE_SHELL_CMD}"
    print(f"Executing remote command: {create_snapshot_cmd}")
    logging.info(f"Executing remote command: {create_snapshot_cmd}")
    output = execute_ssh_command(SSH_HOST, SSH_USERNAME, create_snapshot_cmd)
    print(f"Command output: {output}")
    logging.info(f"Command output: {output}")

    # 其他命令可以直接在本地执行（假设这里的环境已经配置好了HBase Shell）
    local_commands = [
        # 删除快照
        f"hadoop fs -rm -r {HBASE_HDFS_PATH}/{snapshot_name}",
        f"hadoop fs -rm -r {HBASE_HDFS_PATH}/.tmp/{snapshot_name}",
        # 导出快照
        f"{HBASE_EXPORT_CMD} {HBASE_QUEUE_NAME} -snapshot {snapshot_name} -copy-from {HBASE_SRC_PATH} -copy-to {HBASE_DST_PATH}",
        # 禁用表
        f"echo \"disable 'DTSW:{tablename}'\" | {HBASE_SHELL_CMD}",
        # 从快照恢复表
        f"echo \"restore_snapshot '{snapshot_name}'\" | {HBASE_SHELL_CMD}",
        # 启用表
        f"echo \"enable 'DTSW:{tablename}'\" | {HBASE_SHELL_CMD}"
    ]

    for cmd in local_commands:
        print(f"Executing local command: {cmd}")
        logging.info(f"Executing local command: {cmd}")
        # 假设这里的环境已经配置好了HBase Shell
        # 这里需要替换为实际执行本地命令的方法
        output = execute_local_command(cmd)


def main():
    # 读取表名
    with open(TABLE_NAMES_FILE, 'r') as file:
        table_names = [line.strip() for line in file if line.strip()]
    
    # 清空日志
    with open(LOG_FILE, 'w') as file:
        file.write('')

    # 使用线程池执行命令
    max_workers = 5  # 根据实际情况调整最大工作线程数
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = {executor.submit(execute_hbase_commands, tablename): tablename for tablename in table_names}

        for future in as_completed(futures):
            tablename = futures[future]
            try:
                future.result()
                print(f"Finished processing table: {tablename}")
                logging.info(f"Finished processing table: {tablename}")
            except Exception as exc:
                print(f"Failed to process table {tablename}: {exc}")
                logging.error(f"Failed to process table {tablename}: {exc}")


if __name__ == "__main__":
    main()
```


