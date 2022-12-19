1. 初始化一个环境：python db_manage.py db init 
2. 自动检测模型，生成迁移脚本：python db_manage.py db migrate
3. 将迁移脚本映射到数据库中：python db_manage.py db upgrade 
4. 更多命令：python db_manage.py db --help