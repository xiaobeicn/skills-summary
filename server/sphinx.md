### sphinx 配置文件全解析

#### 关键词解析
* source：数据源，数据是从什么地方来的。
* index：索引，当有数据源之后，从数据源处构建索引。索引实际上就是相当于一个字典检索。有了整本字典内容以后，才会有字典检索。
* searchd：提供搜索查询服务。它一般是以deamon的形式运行在后台的。
* indexer：构建索引的服务。当要重新构建索引的时候，就是调用indexer这个命令。
* attr：属性，属性是存在索引中的，它不进行全文索引，但是可以用于过滤和排序。

#### 配制文件：

```bash
## 数据源src1
source src1
{
    ## 说明数据源的类型。数据源的类型可以是：mysql，pgsql，mssql，xmlpipe，odbc，python
    ## 有人会奇怪，python是一种语言怎么可以成为数据源呢？
    ## python作为一种语言，可以操作任意其他的数据来源来获取数据，更多数据请看：（http://www.coreseek.cn/products-install/python/）
    type            = mysql

    ## 下面是sql数据库特有的端口，用户名，密码，数据库名等。
    sql_host        = localhost
    sql_user        = test
    sql_pass        =
    sql_db          = test
    sql_port        = 3306

    ## 如果是使用unix sock连接可以使用这个。
    # sql_sock      = /tmp/mysql.sock

    ## indexer和mysql之间的交互，需要考虑到效率和安全性。
    ## 比如考虑到效率，他们两者之间的交互需要使用压缩协议；考虑到安全，他们两者之间的传输需要使用ssl
    ## 那么这个参数就代表这个意思，0/32/2048/32768  无/使用压缩协议/握手后切换到ssl/Mysql 4.1版本身份认证。
    # mysql_connect_flags   = 32

    ## 当mysql_connect_flags设置为2048（ssl）的时候，下面几个就代表ssl连接所需要使用的几个参数。
    # mysql_ssl_cert        = /etc/ssl/client-cert.pem
    # mysql_ssl_key     = /etc/ssl/client-key.pem
    # mysql_ssl_ca      = /etc/ssl/cacert.pem

    ## mssql特有，是否使用windows登陆
    # mssql_winauth     = 1

    ## mssql特有，是使用unicode还是单字节数据。
    # mssql_unicode     = 1 # request Unicode data from server

    ## odbc的dsn串
    # odbc_dsn      = DBQ=C:\data;DefaultDir=C:\data;Driver={Microsoft Text Driver (*.txt; *.csv)};

    ## sql某一列的缓冲大小，一般是针对字符串来说的。
    ## 为什么要有这么一种缓冲呢？
    ## 有的字符串，虽然长度很长，但是实际上并没有使用那么长的字符，所以在Sphinx并不会收录所有的字符，而是给每个属性一个缓存作为长度限制。
    ## 默认情况下非字符类型的属性是1KB，字符类型的属性是1MB。
    ## 而如果想要配置这个buffer的话，就可以在这里进行配置了。
    # sql_column_buffers    = content=12M, comments=1M

    ## indexer的sql执行前需要执行的操作。
    # sql_query_pre     = SET NAMES utf8
    # sql_query_pre     = SET SESSION query_cache_type=OFF

    ## indexer的sql执行语句
    sql_query       = \
        SELECT id, group_id, UNIX_TIMESTAMP(date_added) AS date_added, title, content \
        FROM documents

    ## 有的时候有多个表，我们想要查询的字段在其他表中。这个时候就需要对sql_query进行join操作。
    ## 而这个join操作可能非常慢，导致建立索引的时候特别慢，那么这个时候，就可以考虑在sphinx端进行join操作了。
    ## sql_joined_field是增加一个字段，这个字段是从其他表查询中查询出来的。
    ## 这里封号后面的查询语句是有要求的，如果是query，则返回id和查询字段，如果是payload-query，则返回id，查询字段和权重。
    ## 并且这里的后一个查询需要按照id进行升序排列。
    # sql_joined_field  = tags from query; SELECT docid, CONCAT('tag',tagid) FROM tags ORDER BY docid ASC
    # sql_joined_field  = wtags from payload-query; SELECT docid, tag, tagweight FROM tags ORDER BY docid ASC

    ## 外部文件字段，意思就是一个表中，有一个字段存的是外部文件地址，但是实际的字段内容在文件中。比如这个字段叫做content_file_path。
    ## 当indexer建立索引的时候，查到这个字段，就读取这个文件地址，然后加载，并进行分词和索引建立等操作。
    # sql_file_field        = content_file_path

    ## 当数据源数据太大的时候，一个sql语句查询下来往往很有可能锁表等操作。
    ## 那么我么就可以使用多次查询，那么这个多次查询就需要有个范围和步长，sql_query_range和sql_range_step就是做这个使用的。
    ## 获取最大和最小的id，然后根据步长来获取数据。比如下面的例子，如果有4500条数据，这个表建立索引的时候就会进行5次sql查询。 
    ## 而5次sql查询每次的间隔时间是使用sql_ranged_rhrottle来进行设置的。单位是毫秒。
    # sql_query_range       = SELECT MIN(id),MAX(id) FROM documents
    # sql_range_step        = 1000
    # sql_ranged_throttle   = 0

    ## 下面都是些不同属性的数据了
    ## 先要了解属性的概念：属性是存在索引中的，它不进行全文索引，但是可以用于过滤和排序。

    ## uint无符号整型属性
    sql_attr_uint       = group_id

    ## bool属性
    # sql_attr_bool     = is_deleted

    ## 长整型属性
    # sql_attr_bigint       = my_bigint_id

    ## 时间戳属性，经常被用于做排序
    sql_attr_timestamp  = date_added

    ## 字符串排序属性。一般我们按照字符串排序的话，我们会将这个字符串存下来进入到索引中，然后在查询的时候比较索引中得字符大小进行排序。
    ## 但是这个时候索引就会很大，于是我们就想到了一个方法，我们在建立索引的时候，先将字符串值从数据库中取出，暂存，排序。
    ## 然后给排序后的数组分配一个序号，然后在建立索引的时候，就将这个序号存入到索引中去。这样在查询的时候也就能完成字符串排序的操作。
    ## 这，就是这个字段的意义。
    # sql_attr_str2ordinal  = author_name

    ## 浮点数属性，经常在查询地理经纬度的时候会用到。
    # sql_attr_float        = lat_radians
    # sql_attr_float        = long_radians

    ## 多值属性（MVA）
    ## 试想一下，有一个文章系统，每篇文章都有多个标签，这个文章就叫做多值属性。
    ## 我要对某个标签进行查询过滤，那么在建立查询的时候就应该把这个标签的值放入到索引中。
    ## 这个字段，sql_attr_multi就是用来做这个事情的。
    # sql_attr_multi        = uint tag from query; SELECT docid, tagid FROM tags
    # sql_attr_multi        = uint tag from ranged-query; \
    #   SELECT docid, tagid FROM tags WHERE id>=$start AND id<=$end; \
    #   SELECT MIN(docid), MAX(docid) FROM tags

    ## 字符串属性。
    # sql_attr_string       = stitle

    ## 文档词汇数记录属性。比如下面就是在索引建立的时候增加一个词汇数的字段
    # sql_attr_str2wordcount    = stitle

    ## 字符串字段，可全文搜索，可返回原始文本信息。
    # sql_field_string  = author

    ## 文档词汇数记录字段，可全文搜索，可返回原始信息
    # sql_field_str2wordcount   = title

    ## 取后查询，在sql_query执行后立即操作。
    ## 它和sql_query_post_index的区别就是执行时间不同
    ## sql_query_post是在sql_query执行后执行，而sql_query_post_index是在索引建立完成后才执行。
    ## 所以如果要记录最后索引执行时间，那么应该在sql_query_post_index中执行。
    # sql_query_post        =

    ## 参考sql_query_post的说明。
    # sql_query_post_index  = REPLACE INTO counters ( id, val ) \
    #   VALUES ( 'max_indexed_id', $maxid )

    ## 命令行获取信息查询。
    ## 什么意思呢？
    ## 我们进行索引一般只会返回主键id，而不会返回表中的所有字段。
    ## 但是在调试的时候，我们一般需要返回表中的字段，那这个时候，就需要使用sql_query_info。
    ## 同时这个字段只在控制台有效，在api中是无效的。
    sql_query_info      = SELECT * FROM documents WHERE id=$id

    ## 比如有两个索引，一个索引比较旧，一个索引比较新，那么旧索引中就会有数据是旧的。
    ## 当我要对两个索引进行搜索的时候，哪些数据要按照新的索引来进行查询呢。
    ## 这个时候就使用到了这个字段了。
    ## 这里的例子（http://www.coreseek.cn/docs/coreseek_4.1-sphinx_2.0.1-beta.html#conf-sql-query-killlist）给的非常清晰了。
    # sql_query_killlist    = SELECT id FROM documents WHERE edited>=@last_reindex

    ## 下面几个压缩解压的配置都是为了一个目的：让索引重建的时候不要影响数据库的性能表现。
    ## SQL数据源解压字段设置
    # unpack_zlib       = zlib_column
    ## MySQL数据源解压字段设置
    # unpack_mysqlcompress  = compressed_column
    # unpack_mysqlcompress  = compressed_column_2
    ## MySQL数据源解压缓冲区设置
    # unpack_mysqlcompress_maxsize  = 16M


    ## xmlpipe的数据源就是一个xml文档
    # type          = xmlpipe

    ## 读取数据源的命令
    # xmlpipe_command       = cat /home/yejianfeng/instance/coreseek/var/test.xml

    ## 字段
    # xmlpipe_field     = subject
    # xmlpipe_field     = content

    ## 属性
    # xmlpipe_attr_timestamp    = published
    # xmlpipe_attr_uint = author_id

    ## UTF-8修复设置
    ## 只适用xmlpipe2数据源，数据源中有可能有非utf-8的字符，这个时候解析就有可能出现问题
    ## 如果设置了这个字段，非utf-8序列就会全部被替换为空格。
    # xmlpipe_fixup_utf8    = 1
}

## sphinx的source是有继承这么一种属性的，意思就是除了父source之外，这个source还有这个特性
source src1throttled : src1
{
    sql_ranged_throttle = 100
}

## 索引test1
index test1
{
    ## 索引类型，包括有plain，distributed和rt。分别是普通索引/分布式索引/增量索引。默认是plain。
    # type          = plain

    ## 索引数据源
    source          = src1
    ## 索引文件存放路径
    path            = /home/yejianfeng/instance/coreseek/var/data/test1

    ## 文档信息的存储模式，包括有none,extern,inline。默认是extern。
    ## docinfo指的就是数据的所有属性（field）构成的一个集合。
    ## 首先文档id是存储在一个文件中的（spa）
    ## 当使用inline的时候，文档的属性和文件的id都是存放在spa中的，所以进行查询过滤的时候，不需要进行额外操作。
    ## 当使用extern的时候，文档的属性是存放在另外一个文件（spd）中的，但是当启动searchd的时候，会把这个文件加载到内存中。
    ## extern就意味着每次做查询过滤的时候，除了查找文档id之外，还需要去内存中根据属性进行过滤。
    ## 但是即使这样，extern由于文件大小小，效率也不低。所以不是有特殊要求，一般都是使用extern
    docinfo         = extern

    ## 缓冲内存锁定。
    ## searchd会讲spa和spi预读取到内存中。但是如果这部分内存数据长时间没有访问，则它会被交换到磁盘上。
    ## 设置了mlock就不会出现这个问题，这部分数据会一直存放在内存中的。
    mlock           = 0

    ## 词形处理器
    ## 词形处理是什么意思呢？比如在英语中，dogs是dog的复数，所以dog是dogs的词干，这两个实际上是同一个词。
    ## 所以英语的词形处理器会讲dogs当做dog来进行处理。
    morphology      = none

    ## 词形处理有的时候会有问题，比如将gps处理成gp，这个设置可以允许根据词的长度来决定是否要使用词形处理器。
    # min_stemming_len  = 1

    ## 词形处理后是否还要检索原词？
    # index_exact_words = 1

    ## 停止词，停止词是不被索引的词。
    # stopwords     = /home/yejianfeng/instance/coreseek/var/data/stopwords.txt

    ## 自定义词形字典
    # wordforms     = /home/yejianfeng/instance/coreseek/var/data/wordforms.txt

    ## 词汇特殊处理。
    ## 有的一些特殊词我们希望把它当成另外一个词来处理。比如，c++ => cplusplus来处理。
    # exceptions        = /home/yejianfeng/instance/coreseek/var/data/exceptions.txt

    ## 最小索引词长度，小于这个长度的词不会被索引。
    min_word_len        = 1

    ## 字符集编码类型，可以为sbcs,utf-8。对于Coreseek，还可以有zh_cn.utf-8,zh_ch.gbk,zh_ch.big5
    charset_type        = sbcs

    ## 字符表和大小写转换规则。对于Coreseek，这个字段无效。
    # 'sbcs' default value is
    # charset_table     = 0..9, A..Z->a..z, _, a..z, U+A8->U+B8, U+B8, U+C0..U+DF->U+E0..U+FF, U+E0..U+FF
    #
    # 'utf-8' default value is
    # charset_table     = 0..9, A..Z->a..z, _, a..z, U+410..U+42F->U+430..U+44F, U+430..U+44F

    ## 忽略字符表。在忽略字符表中的前后词会被连起来当做一个单独关键词处理。
    # ignore_chars      = U+00AD

    ## 是否启用通配符，默认为0，不启用
    # enable_star       = 1

    ## min_prefix_len,min_infix_len,prefix_fields,infix_fields都是在enable_star开启的时候才有效果。
    ## 最小前缀索引长度
    ## 为什么要有这个配置项呢？
    ## 首先这个是当启用通配符配置启用的前提下说的，前缀索引使得一个关键词产生了多个索引项，导致索引文件体积和搜索时间增加巨大。
    ## 那么我们就有必要限制下前缀索引的前缀长度，比如example，当前缀索引长度设置为5的时候，它只会分解为exampl，example了。
    # min_prefix_len        = 0
    ## 最小索引中缀长度。理解同上。
    # min_infix_len     = 0

    ## 前缀索引和中缀索引字段列表。并不是所有的字段都需要进行前缀和中缀索引。
    # prefix_fields     = filename
    # infix_fields      = url, domain

    ## 词汇展开
    ## 是否尽可能展开关键字的精确格式或者型号形式
    # expand_keywords       = 1

    ## N-Gram索引的分词技术
    ## N-Gram是指不按照词典，而是按照字长来分词，这个主要是针对非英文体系的一些语言来做的（中文、韩文、日文）
    ## 对coreseek来说，这两个配置项可以忽略。
    # ngram_len     = 1
    # ngram_chars       = U+3000..U+2FA1F

    ## 词组边界符列表和步长
    ## 哪些字符被看做分隔不同词组的边界。
    # phrase_boundary       = ., ?, !, U+2026 # horizontal ellipsis
    # phrase_boundary_step  = 100

    ## 混合字符列表
    # blend_chars       = +, &, U+23
    # blend_mode        = trim_tail, skip_pure

    ## html标记清理，是否从输出全文数据中去除HTML标记。
    html_strip      = 0

    ## HTML标记属性索引设置。
    # html_index_attrs  = img=alt,title; a=title;

    ## 需要清理的html元素
    # html_remove_elements  = style, script

    ## searchd是预先打开全部索引还是每次查询再打开索引。
    # preopen           = 1

    ## 字典文件是保持在磁盘上还是将他预先缓冲在内存中。
    # ondisk_dict       = 1

    ## 由于在索引建立的时候，需要建立临时文件和和副本，还有旧的索引
    ## 这个时候磁盘使用量会暴增，于是有个方法是临时文件重复利用
    ## 这个配置会极大减少建立索引时候的磁盘压力，代价是索引建立速度变慢。
    # inplace_enable        = 1
    # inplace_hit_gap       = 0 # preallocated hitlist gap size
    # inplace_docinfo_gap   = 0 # preallocated docinfo gap size
    # inplace_reloc_factor  = 0.1 # relocation buffer size within arena
    # inplace_write_factor  = 0.1 # write buffer size within arena

    ## 在经过过短的位置后增加位置值
    # overshort_step        = 1

    ## 在经过 停用词 处后增加位置值
    # stopword_step     = 1

    ## 位置忽略词汇列表
    # hitless_words     = all
    # hitless_words     = hitless.txt

    ## 是否检测并索引句子和段落边界
    # index_sp          = 1

    ## 字段内需要索引的HTML/XML区域的标签列表
    # index_zones       = title, h*, th
}

index test1stemmed : test1
{
    path            = /home/yejianfeng/instance/coreseek/var/data/test1stemmed
    morphology      = stem_en
}

index dist1
{
    type            = distributed

    local           = test1
    local           = test1stemmed

    ## 分布式索引（distributed index）中的远程代理和索引声明
    agent           = localhost:9313:remote1
    agent           = localhost:9314:remote2,remote3
    # agent         = /var/run/searchd.sock:remote4

    ## 分布式索引（ distributed index）中声明远程黑洞代理
    # agent_blackhole       = testbox:9312:testindex1,testindex2

    ## 远程代理的连接超时时间
    agent_connect_timeout   = 1000

    ## 远程查询超时时间
    agent_query_timeout = 3000
}

index rt
{
    type            = rt

    path            = /home/yejianfeng/instance/coreseek/var/data/rt

    ## RT索引内存限制
    # rt_mem_limit      = 512M

    ## 全文字段定义
    rt_field        = title
    rt_field        = content

    ## 无符号整数属性定义
    rt_attr_uint        = gid

    ## 各种属性定义
    # rt_attr_bigint        = guid
    # rt_attr_float     = gpa
    # rt_attr_timestamp = ts_added
    # rt_attr_string        = author
}

indexer
{
    ## 建立索引的时候，索引内存限制
    mem_limit       = 32M

    ## 每秒最大I/O操作次数，用于限制I/O操作
    # max_iops      = 40

    ## 最大允许的I/O操作大小，以字节为单位，用于I/O节流
    # max_iosize        = 1048576

    ## 对于XMLLpipe2数据源允许的最大的字段大小，以字节为单位
    # max_xmlpipe2_field    = 4M

    ## 写缓冲区的大小，单位是字节
    # write_buffer      = 1M

    ## 文件字段可用的最大缓冲区大小，字节为单位
    # max_file_field_buffer = 32M
}

## 搜索服务配置
searchd
{
    # listen            = 127.0.0.1
    # listen            = 192.168.0.1:9312
    # listen            = 9312
    # listen            = /var/run/searchd.sock

    ## 监听端口
    listen          = 9312
    listen          = 9306:mysql41

    ## 监听日志
    log         = /home/yejianfeng/instance/coreseek/var/log/searchd.log

    ## 查询日志
    query_log       = /home/yejianfeng/instance/coreseek/var/log/query.log

    ## 客户端读超时时间 
    read_timeout        = 5

    ## 客户端持久连接超时时间，即客户端读一次以后，持久连接，然后再读一次。中间这个持久连接的时间。
    client_timeout      = 300

    ## 并行执行搜索的数目
    max_children        = 30

    ## 进程id文件
    pid_file        = /home/yejianfeng/instance/coreseek/var/log/searchd.pid

    ## 守护进程在内存中为每个索引所保持并返回给客户端的匹配数目的最大值
    max_matches     = 1000

    ## 无缝轮转。防止 searchd 轮换在需要预取大量数据的索引时停止响应
    ## 当进行索引轮换的时候，可能需要消耗大量的时间在轮换索引上。
    ## 但是启动了无缝轮转，就以消耗内存为代价减少轮转的时间
    seamless_rotate     = 1

    ## 索引预开启，是否强制重新打开所有索引文件
    preopen_indexes     = 1

    ## 索引轮换成功之后，是否删除以.old为扩展名的索引拷贝
    unlink_old      = 1

    ## 属性刷新周期
    ## 就是使用UpdateAttributes()更新的文档属性每隔多少时间写回到磁盘中。
    # attr_flush_period = 900

    ## 索引字典存储方式
    # ondisk_dict_default   = 1

    ## 用于多值属性MVA更新的存储空间的内存共享池大小
    mva_updates_pool    = 1M

    ## 网络通讯时允许的最大的包的大小
    max_packet_size     = 8M

    ## 崩溃日志文件
    # crash_log_path        = /home/yejianfeng/instance/coreseek/var/log/crash

    ## 每次查询允许设置的过滤器的最大个数
    max_filters     = 256

    ## 单个过滤器允许的值的最大个数
    max_filter_values   = 4096

    ## TCP监听待处理队列长度
    # listen_backlog        = 5

    ## 每个关键字的读缓冲区的大小
    # read_buffer       = 256K

    ## 无匹配时读操作的大小
    # read_unhinted     = 32K

    ## 每次批量查询的查询数限制
    max_batch_queries   = 32

    ## 每个查询的公共子树文档缓存大小
    # subtree_docs_cache    = 4M

    ## 每个查询的公共子树命中缓存大小
    # subtree_hits_cache    = 8M

    ## 多处理模式（MPM）。 可选项；可用值为none、fork、prefork，以及threads。 默认在Unix类系统为form，Windows系统为threads。
    workers         = threads # for RT to work

    ## 并发查询线程数
    # dist_threads      = 4

    ## 二进制日志路径
    # binlog_path       = # disable logging
    # binlog_path       = /home/yejianfeng/instance/coreseek/var/data # binlog.001 etc will be created there

    ## 二进制日志刷新
    # binlog_flush      = 2

    ## 二进制日志大小限制
    # binlog_max_log_size   = 256M

    ## 线程堆栈
    # thread_stack          = 128K

    ## 关键字展开限制
    # expansion_limit       = 1000

    ## RT索引刷新周期 
    # rt_flush_period       = 900

    ## 查询日志格式
    ## 可选项，可用值为plain、sphinxql，默认为plain。 
    # query_log_format      = sphinxql

    ## MySQL版本设置
    # mysql_version_string  = 5.0.37

    ## 插件目录
    # plugin_dir            = /usr/local/sphinx/lib

    ## 服务端默认字符集
    # collation_server      = utf8_general_ci
    ## 服务端libc字符集
    # collation_libc_locale = ru_RU.UTF-8

    ## 线程服务看守
    # watchdog              = 1
    ## 兼容模式
    # compat_sphinxql_magics    = 1
}
```
