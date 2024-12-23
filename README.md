
# doubao_parallel_test

## 模块信息
- 模块名称：doubao_parallel_test
- 模块描述：提供基于多线程对doubao模型进行大规模数据测试的能力
- 作者：Albert Liu
- 创建日期：2024-11-18
- 版本：v5.0

## 更新记录
**v5.0**
- sp和up增加字符串泛型约束
- 解决并发控制失效问题
- 修改时间记录逻辑
- 优化日志处理逻辑
- 增加多轮对话测试能力
- 修改配置文件设定

**v4.0**
- 使用python coroutine重构代码，提升代码运行效率
- 大幅缩减为可执行任务种类为2个，增强代码可读性便于自定义修改逻辑
- 增加简易demo，上手即用
- 更新Ark sdk版本，升级到v3，兼容openai sdk
- bug修复:
   - 解决前序版本数据集读取重复导致内存占用过大等问题
   - 解决v2.0版本加锁异常
 
**v3.0**
- 修复并发启动时出现部分thread启动失败问题
- 修改并发逻辑，处理单文件并发写入问题
  
**v2.0**
- 增加多线程并发测试能力
  
**v1.0**
- 实现基本逻辑

## 使用说明
### 依赖
- openpyxl (安装方法: `pip3 install openpyxl`)
- volcengine.maas.v3 (安装方法: `pip3 install volcengine`)

### 用法示例：
### 低代码使用方法：
1. 从以下两种选择中，选择测试内容，修改config.ini文件中的test_data_name文件名
   - 单轮对话测试: multi_round.xlsx
   - 多轮对话测试: single_round.xlsx
2. 替换文件中数据待测试数据
   - 单轮对话：填充后的文档包含3个字段，如[test_index（从0开始）, system_prompt, user_prompt_1]
   - 多轮对话：填充后的文档每行包含一次与模型的完整多轮对话，每轮对话占一个单元格，如[test_index（从0开始）, system_prompt, up1,ap1,up2,ap2,up3,ap3....upX]，注意最后一个一定是upX
   
   （注：多轮对话的对话轮次允许不一致，如第一行只有3轮对话，第二行允许只有1轮对话）
3. 修改config.ini中api_key及endpoint_id
4. 选择以下两种方式其一，运行代码
   - order_test: 顺序查询，从0开始，查询到CONSTANTS.total_test_data_num，每个case查询CONSTANTS.try_times次
   - index_test: 点查，按照test_index_list中要求的index进行查询，每个case查询CONSTANTS.try_times次
5. 结果生成在./result/中
   - 单轮对话：single_round_v0.xlsx
   - 多轮对话：multi_round_v0.xlsx
### 高代码使用方法：
1. 支持以下4个方法重构，重构建议在方法注释中
    - load_test_data: 自定义测试集数据读取逻辑，适应不同的测试集数据格式
    - read_system_prompt: 自定义sp的组装格式，适应变量替换 or sp不一致场景
    - resolve_user_ass_prompt: 自定义up及ap的解析格式，适应up读取数据后，需要二次增加内容
    - result_process: 自定义结果存储格式，适应于格式化输出如json需解析内容
2. 如需要增加全局参数，考虑在config.ini定义后，在Constant class中读取
3. 结果输出，结果都生成在work_directory/result_directory路径下，一般会生成3类文件
    - data.xlsx: 存储模型运算结果数据 及 result_process后处理后的自定义结果数据
    - log.log: 运行日志
    - wrong.txt: 存储多次重试后，仍失败的任务index（test_data中的index）

## 注意：
1. 建议只修改以上4个推荐修改的函数，其他函数涉及到coroutine调度逻辑，建议不要动
2. 核心代码逻辑在request函数中
