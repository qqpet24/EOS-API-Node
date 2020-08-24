# EOS-API-Node
Tutorial for creating your own EOS API node.. 建立自己的EOS API node教程..

(0)Centos 7 x64
CPU至少要1核心不限制使用的，内存至少要1G（只有1G内存需要加swap），实际上这么低的配置无法正常使用，但是可以安装。建议使用2核心4G以上配置。
The CPU of your computer should have at least 1 vCPU without limitation and 1G ram(If your computer only has 1G ram a swap memory is needed). Actually, it won't working well if your computer's performance likes this. But you can install it. A computer with 2 vCPUs and 4G ram is recommended.
(1)编译安装zstd( Compile and install zstd)
yum  install  gcc
yum install git
git clone https://github.com/facebook/zstd.git
cd zstd
sudo make
sudo make install
(zstd 压缩 zstd -z my_file.txt 解压：zstd -d my_file.txt.zst）

(2)eosio系统安装(Install eosio)
wget https://github.com/eosio/eos/releases/download/v2.0.7/eosio-2.0.7-1.el7.x86_64.rpm
sudo yum install ./eosio-2.0.7-1.el7.x86_64.rpm

(3)写配置文件(Write the config)
cd..
mkdir eos
cd eos
mkdir config
mkdir data
mkdir snapshots
(a)cd config
nano config.ini
然后把下列内容填上去(Copy the following text into config.ini)
//P2P节点(Peer to peer node)
//https://validate.eosnation.io/eos/reports/config.html
//https://eosnodes.privex.io/

agent-name = AgentName
http-server-address = 0.0.0.0:8888
p2p-server-address = 0.0.0.0:9876
http-validate-host = false
verbose-http-errors = true  
chain-state-db-size-mb = 16384
reversible-blocks-db-size-mb = 2048
sync-fetch-span = 2000

validation-mode = light
abi-serializer-max-time-ms = 30000
chain-threads = 2
http-threads = 2

plugin = eosio::chain_api_plugin
plugin = eosio::chain_plugin
plugin = eosio::http_plugin   
plugin = eosio::net_api_plugin

p2p-peer-address = peer.eosn.io:9876
p2p-peer-address = p2p.eoseoul.io:9876
p2p-peer-address = eosnode.b1.run:9876
p2p-peer-address = p2p.bitmars.one:8080
p2p-peer-address = eosbp-0.atticlab.net:9876
p2p-peer-address = 47.91.245.50:9876
p2p-peer-address = eos-bp.bitfinex.com:9876
p2p-peer-address = node1.starteos.io:9876
p2p-peer-address = peer1.mainnet.helloeos.com.cn
p2p-peer-address = p2p-eos.whaleex.com:9876
p2p-peer-address = peer1.eoshuobipool.com:8181
p2p-peer-address = peer2.eoshuobipool.com:8181 
p2p-peer-address = p2p.meet.one:9876
p2p-peer-address = p2p.prod.eosgravity.com:80
p2p-peer-address = eu-west-nl.eosamsterdam.net:9876
p2p-peer-address = p2p.mainnet.eosgermany.online:9876
p2p-peer-address = p2p.genereos.io:9876
p2p-peer-address = mainnet.eospay.host:19876
p2p-peer-address = 54.153.59.31:9999
p2p-peer-address = peer.main.alohaeos.com:9876
p2p-peer-address = peer.eosn.io:9876
p2p-peer-address = prod.mainnet.eos.cybex.io:9888
p2p-peer-address = eosbattles.com:9877
p2p-peer-address = 34.226.76.22:9876
p2p-peer-address = mainnet.eosoasis.io:9876
p2p-peer-address = node.eosflare.io:1883
p2p-peer-address = eno.eosvan.io:19866

(b)安装snapshots并启动重放,snapshot必须下载最新的版本并立即使用，否则重放非常缓慢(Download snapshots and replay the blocks. The snapshots should be the latest version, or it will take a long time)
snapshots地址https://snapshots.eosnation.io/

wget https://snapshots.eosnation.io/eos/latest
mv latest latest.zst
zstd -d latest.zst
screen -S eosnode
nodeos  --config-dir /eos/config --data-dir /eos/data --snapshot /eos/snapshots/latest --delete-all-blocks
然后ctrl+A+D分离窗口(Detach window use ctrl+A+D)
(c)安装FTP(注意，这样设置的FTP如果密码泄露后会很不安全，整个服务器文件都能看到，但是能用)(Install ftp)(Attendion! Install ftp as the following wouldn't be safe if your leak your passwords as all the files in the server could be seen. But it still work well despite of this)
yum install -y vsftpd
systemctl enable vsftpd.service
useradd -d /eos/eosjs1 -s /bin/bash ftpname #/eos/eosjs1为ftp的根目录
passwd ftpname
#这步需要自行输入密码(In this step, you should write passwords)
service vsftpd start
chmod 777 /eos/eosjs1 #/eos/eosjs1为ftp的根目录
(d)安装npm（编译安装）(Compile and install npm)
wget https://npm.taobao.org/mirrors/node/v11.0.0/node-v11.0.0.tar.gz
tar -xvf node-v11.0.0.tar.gz
cd node-v11.0.0
sudo yum install gcc gcc-c++
./configure
make
sudo make install
(4)正常启动(Start eosio node)
nodeos  --config-dir /eos/config --data-dir /eos/data
(5)参考命令(Reference command)
rm blocks/* -r
rm state/* -r
nodeos --config-dir /.local/share/eosio/nodeos/config
nodeos  --config-dir /eos/config --data-dir /eos/data --genesis-json /eos/config/genesis.json

