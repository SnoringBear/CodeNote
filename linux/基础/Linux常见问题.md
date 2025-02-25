### 一、Syntax error: "(" unexpected（linux系统）



### 原因：



兼容性问题，因为linux将sh默认指向了dash，而不是bash



代码对于标准bash而言没有错，因为Ubuntu/Debian为了加快开机速度，用dash代替了传统的bash，是dash在捣鬼



### 解决



sudo dpkg-reconfigure dash             在选择项中选No，搞定了！