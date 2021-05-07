
### mysql 读写时不支持中文

1. 第一种解决方法：

修改MySQL安装目录下的my-default文件，命名为：my.ini，然后添加以下语句：

default-character-set=utf8

character-set-server=utf8

注意：修改并保存my.ini文件后，必须重新启动MySQL服务，修改配置才可以生效。


2. 设置字符集的编码格式，在mysql终端执行以下语句： （亲测有效）

set character_set_database=utf8;
set character_set_server=utf8;
set character_set_client=gbk;
set character_set_connection=gbk;

注意：设置完成后，重新创建数据库，并且创建表，发现可以插入中文数据了。