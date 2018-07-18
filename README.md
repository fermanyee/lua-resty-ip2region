# lua-resty-ip2region

根据IP地址定位所在区域的工具函数(IP数据来源于[ip2region](https://github.com/lionsoul2014/ip2region))

# Overview



    lua_package_path '/the/path/to/your/project/lib/?.lua';
	lua_shared_dict ip_data 100m;

	server {
		location =/iplocation {
			default_type text/html;
			content_by_lua_block {
				local ip2region = require 'resty.ip2region.ip2region';
				local location = ip2region.new({
				    file = "/the/path/to/your/project/lib/resty/ip2region/data/ip2region.db",
				    root = "/the/path/to/your/project",
				    dict = "ip_data",
				    mode = "memory" -- maybe memory,binary or btree
				});
                            local  data, err = location:search('202.108.22.5');
				--[[
                                  {
					country = "中国",
					region = "华东"
					province = "浙江", 
					city = "杭州", 
					isp = "电信"
                                   }
                            --]]
			        if (err == nil) then
			            ngx.say(data['city'])
                            else
                                ngx.say(err)
                            end
			}
		}
	}


# Methods

## new

* 用法: location, err = ip2region.new({file = 'the/path/to/the/data/file', dict = 'shared dict name'})

* 功能：初始化ip2region模块

* 参数：是一个table，里面有以下选项
     
   + file：数据文件所在路径(它可以是相对目录也可以是绝对路径)

   + root: 项目根目录(如果file是相对目录，则要设置根目录，它默认是设置的root变量或document_root变量值)

   + dict:共享字典的名称(默认为ip_data，注：字典的大小建议为5m，因为文件存到内存中所占内存大约为1.５多M),
   
   + mode:查询方式(支持内存(memory)查找,二进制(binary)查找和二叉树(btree)查找)

## search

* 用法:ip_tab,err = location:search(ip, multi)

* 功能：通过一个范围查找它的中位数以及它对应的IP数据(根据初始化参数调用相应的查询方法)

* 参数：
     
   + ip:查询的IP

   + multi:是否多次查找(多次查询不会关闭文件流，但需要手动调用close方法关闭文件流)
   
* 返回数据说明：
   
如果查询成功，则得到一个table数据，其结构如下：
   
       {
           country = "中国",
           region = "华东"
           province = "浙江",
	       city = "杭州",
	       isp = "电信",
      
       }
   
* 字段说明：
   
  
   + country：为国家名称(都是中文)
   
   + region : 大区名称(目前该字段不可用)
  
   + province：省级行政区名称(不带行政区行政单位名称)
   
   + city:城市名称
   
   + isp:网络提供商
   

## memory_search

* 用法:ip_tab,err = location:memory_search(ip)

* 功能：通过从内存数据中查找数据(如果没有对应的字典，则从数据文件中查找)

* 参数：

   +  ip:查询的IP


* 返回值与search方法相同

## bin_search

* 用法:ip_tab,err = location:bin_search(ip)

* 功能：通过二进制文件查找(二分法查找)

* 参数：

   + ip:查询的IP

   + multi:是否多次查找(多次查询不会关闭文件流，但需要手动调用close方法关闭文件流)

* 返回值与search方法相同

## btree_search

* 用法:ip_tab,err = location:btree_search(ip)

* 功能：通过btree方法在文件中查找(通过B树查找)

* 参数：

   + ip:查询的IP

   + multi:是否多次查找(多次查询不会关闭文件流，但需要手动调用close方法关闭文件流)

* 返回值与search方法相同


## loadfile

* 用法：content, err = location:loadfile()

* 功能：加载数据文件并返回数据文件中的数据(内部使用)

## close

* 用法：location:close()

* 功能：关闭文件流



# TODO

数据文件进一步加工(返回更多的字段)

# contact

也请各位同学反馈bug

E-mail:ishixinke@qq.com

website:[www.shixinke.com](http://www.shixinke.com "诗心客的博客")
