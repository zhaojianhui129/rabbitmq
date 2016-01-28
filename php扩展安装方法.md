一、安装librabbitmq-c和rabbitmq-codegen

先安装libtool包
yum install libtool -y
# 下载0-9-1版的rabbitmq-c
git clone git://github.com/alanxz/rabbitmq-c.git
cd rabbitmq-c
# Enable and update the codegen git submodule
git submodule init
git submodule update
# Configure, compile and install
autoreconf -i && ./configure && make && sudo make install

第二步：
安装php的amqp扩展：
git clone https://github.com/pdezwart/php-amqp.git
cd php-amqp/
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --with-amqp
make
make install
#直接在php.ini中添加生成的amqp.so
vi  /etc/php.ini 
# 加入一行 extension = amqp.so
service php-fpm restart