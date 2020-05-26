Ruby On Rails
====

1. 介绍
----
Rails 是使用 Ruby 语言，开发 Web 应用或者 Api 的框架。  

2. Rails 安装和使用
----
**安装 Rails，请使用 RubyGems 提供的 gem install 命令：**  
    
    $ gem install rails  
**执行下面的命令来确认所有软件是否都已正确安装：**  

    $ rails --version
**创建项目:**  
    
    $ rails new share-api-demo
这个命令会在文件夹 share-api-demo 中创建名为 share-api-demo 的 Rails 应用，然后执行 bundle install 命令安装 Gemfile 中列出的 gem 及其依赖。  

**启动 Web 服务：**  

	$ rails server
上述命令会启动 Puma，这是 Rails 默认使用的 Web 服务器。要查看运行中的应用，请打开浏览器窗口，访问 http://localhost:3000。
3. 创建第一个 API
----
**配置路由：**  

	#在config/routes.rb文件中
	namespace :api do
      namespace :v1 do
        resource :users, only:[] do
          get :showInfo
        end
      end±
    end
**查看路由：**

	$ bundle exe rails routes
**生成控制器：**  
    
    $ bundle exe rails g controller api/v1/users --no-assets
**返回 json 数据：**  

	#在app/controllers/api/v1/users_controller.rb文件中
	def showInfo
        render json: { 
            data: "user" , 
            message: "OK", 
            status: 1
        }
    end
**使用 curl 请求 api：**  
	
	$ curl -i http://localhost:3000/api/v1/users/showInfo
4. 创建 User 模型和 users 表
----
**创建模型和表：**
	
	$ bundle exe rails g model User
**定义数据类型：**

	#在db/migrate/2020**********_create_users.rb
	class CreateUsers < ActiveRecord::Migration[6.0]
	  def change
	    create_table :users do |t|
	      t.string :name
	      t.timestamps
	    end
	  end
	end
**数据迁移：**

	$ bundle exe rake db:migrate
**数据初始化：**
	
	#在db/seeds.rb文件中
	users = User.create([
	                     {
	                       name: '张三',
	                     }
	                    ])
**创建数据初始化：**

	$ bundle exe rake db:seed
**api 数据从数据库获取：**

	#在app/controllers/api/v1/users_controller.rb文件中修改
	def showInfo
        user = User.first

        render json: { 
            data: user , 
            message: "OK", 
            status: 1
        }
    end
**使用 curl 请求 api 查看结果：**  
	
	$ curl -i http://localhost:3000/api/v1/users/showInfo
5. 链接第二个数据库
----
**这里使用本地 postgresql 数据库：**

	$ psql -h localhost -p 5432 railsdemo
**安装 pg gem：**
	
	#在 Gemfile 文件里添加
	gem 'pg'
	#然后执行
	$ bundle install
**添加数据库配置：**

	#在database.yml添加以下代码
	other_database_pg:
	  adapter: postgresql
	  encoding: utf8
	  reconnect: true
	  pool: 5
	  host: localhost
	  # username: 
	  # password: 
	  database: railsdemo
**添加数据库关联：**

	#在initializers目录下新建external_data.rb文件
	class ExternalData < ActiveRecord::Base
	    self.abstract_class = true
	    establish_connection :other_database_pg
	end
**添加 model：**
	
	#在models文件夹里新建student.rb文件
	class Student < ExternalData
	end
**使用：**

	a = Student.first
	puts "name:#{a.name}"
6. Ruby 语法和特点
----
**合并赋值与置换变量：**

	a,b = 'a','b'
    a,b = b,a
    puts "#{a}/#{b}"
**建立多行字符串：**
	
	ms = print <<EOF
	    这是第一行
	      这是第二行
	EOF
**数据类型：**
	
	Number、String、Ranges、Symbols，以及true、false和nil这几个特殊值，同时还有两种重要的数据结构——Array和Hash。
	
	#整数,可以加下划线
	a=123_456
	puts "a:#{a}"
	
	#Range
	(1..5).each do |n|
    	print n, ' '
    end
    (1...5).each do |n|
    	print n, ' '
    end
    ..从开始值运行到结束值（包含结束值）...从开始值运行到结束值（不包含结束值)
	
	#Array
	arr=["1",1,2,3]
	puts "#{arr[-1]}"
	
	#Hash
	H = Hash["a" => 100, "b" => 200]
	puts "#{H['a']}"
	puts "#{H['b']}"
**条件判断：**
	
	值 false 和 nil 为假，其他值都为真。0为真。一行内。
	a = '真'
	if 0 then puts a end
	puts a if 0
	
	#if...elsif...else...end
	x=1
	if x > 2
	   puts "x 大于 2"
	elsif x <= 2 and x!=0
	   puts "x 是 1"
	else
	   puts "无法得知 x 的值"
	end
	
	#unless 语句，和if作用相反
	puts '真' unless false
**迭代器：**

	#迭代器是集合支持的方法，数组(Array)和哈希(Hash)可以称之为集合。
	#Ruby each 迭代器
	ary = [1,2,3,4,5]
	ary.each do |i|
	   puts i
	end
	
	#Ruby collect 迭代器
	a = [1,2,3,4,5]
	b = a.collect{|x| 10*x}
	puts b
**方法：**

	#可以为参数设置默认值
	def method_name (var1='value1', var2='value2')
        puts "var1:#{var1}"
        puts "var2:#{var2}"
    end
    method_name 1
    
    #可变数量的参数
    def sample (*test)
    	puts "参数个数为 #{test.length}"
	    for i in 0...test.length
	        puts "参数值为 #{test[i]}"
	    end
	end
	sample "Zara", "6"
	sample "Mac", "36", "M"
	
	#返回值，默认最后一个变量。如果给出超过两个的表达式，包含这些值的数组将是返回值
	def method_return
        a = 'a'
        b = 'b'
		a
	end
	puts "method_return:#{method_return}"
	
