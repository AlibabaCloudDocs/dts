# Whitelist DTS IP ranges for your user-created database

Your user-created database hosted off Alibaba Cloud may have been configured to only accept connections from designated IP ranges. In this case, you need to configure your security settings to allow DTS servers to connect.

## Applicable data stores

Certain types of user-created databases, either as the source or target database, require that you configure the security settings to allow DTS servers to access your user-created database. This is required if the database type is any of the following types: user-created database with public IP address, database without public IP:port \(accessed through database gateway\), self built database accessed through Cloud Enterprise Network \(CEN\), or user-created database connected over Express Connect, VPN Gateway, or Smart Access Gateway.

**Note:** You do not need to modify the security settings for ApsaraDB instances, such as ApsaraDB for MySQL and ApsaraDB for MongoDB instances, and user-created databases that are hosted on ECS. DTS automatically adds the CIDR blocks of DTS servers to the IP whitelists of ApsaraDB instances or the security rules of ECS instances.

## Determine the DTS task region

Use the following list to determine which region of the DTS servers that you need to whitelist:

-   Data migration: select the region of the target database and whitelist the corresponding IP ranges in the source and target database settings.
-   Change tracking: select the region of the source database and whitelist the corresponding IP ranges in the source database settings.
-   Data synchronization:
    -   To allow DTS to access the source database, select the regions of the source and target databases and whitelist the corresponding IP ranges in the source database settings.
    -   To allow DTS to access the target database, select the region of the target database and whitelist the corresponding IP ranges in the target database settings.

## Obtain the IP range

The IP range varies, depending on the network over which DTS accesses your database.

**Reachable over the Internet:** If DTS accesses your user-created database over the Internet, use the following table to obtain the DTS IP ranges \(CIDR blocks\) for your selected region:

**Note:** Data synchronization only supports user-created databases that are reachable from an internal network. You do not need to whitelist any of the following IP ranges when you configure data synchronization tasks.

|Region|IP range|
|------|--------|
|China \(Hangzhou\)|101.37.14.0/24,114.55.89.0/24,115.29.198.0/24,118.178.120.0/24,118.178.121.0/24,120.26.106.0/24,120.26.116.0/24,120.26.117.0/24,120.26.118.0/24,120.55.192.0/24,120.55.193.0/24,120.55.194.0/24,120.55.241.0/24,121.40.125.0/24,121.196.246.0/24,101.37.12.0/24,101.37.13.0/24,101.37.15.0/24,101.37.25.0/24,47.96.39.0/24,118.31.184.0/24,118.31.165.0/24,118.31.246.0/24,120.55.12.0/24,47.97.7.0/24,47.97.27.142/32,47.97.73.210/32,121.43.162.118/32,121.43.185.141/32,121.196.211.16/32,114.55.125.94/32,121.43.179.168/32,121.43.174.187/32,47.99.171.159/32,47.97.118.150/32,47.98.251.185/32,47.99.43.73/32,47.97.195.167/32,120.27.211.237/32,47.97.125.64/32,47.98.52.255/32,47.97.116.109/32,47.97.119.148/32,47.98.51.78/32,47.97.106.64/32,116.62.172.149/32,120.55.40.134/32,47.98.39.64/32,116.62.197.0/24,121.196.199.0/24,116.62.206.0/24,116.62.208.0/24,116.62.57.0/24,116.62.20.0/24,116.62.64.0/24,116.62.201.0/24,121.196.198.0/24,118.31.43.101,101.37.152.136,118.31.38.161,120.55.60.232,120.55.60.183,101.37.149.3,112.124.237.0/24|
|China \(Shanghai\)|139.196.17.0/24,139.196.18.0/24,139.196.25.0/24,139.196.27.0/24,139.196.154.0/24,139.196.116.0/24,139.196.254.0/24,139.196.166.0/24,106.14.46.0/24,106.14.37.0/24,106.14.36.0/24,106.15.250.0/24,101.132.248.0/24,47.100.95.0/24,106.15.73.0/24,106.15.75.0/24,47.100.137.0/24,106.14.177.89/32,106.14.178.118/32,139.196.138.36/32,106.14.4.132/32,139.196.92.27/32,139.196.143.11/32,139.196.44.156/32,139.196.6.35/32,139.196.50.106/32,139.196.25.56/32,139.196.47.137/32,139.196.6.124/32,139.196.49.138/32,139.196.41.168/32,139.196.48.218/32,139.196.51.72/32,47.101.194.1/32,47.101.166.207/32,47.101.181.171/32,47.101.177.224/32,47.100.186.20/32|
|China \(Qingdao\)|115.28.200.0/24,115.28.216.0/24,115.28.226.0/24,115.28.247.0/24,118.190.133.0/24,120.27.53.0/24,10.31.69.0/24,10.144.88.0/24,10.144.153.0/24,10.161.39.0/24,10.161.59.0/24,10.252.29.0/24,100.104.72.0/24,47.104.10.200,118.190.157.247,47.104.19.209,47.104.105.196,47.104.97.251|
|China \(Beijing\)|112.126.80.0/24,112.126.87.0/24,112.126.91.0/24,112.126.92.0/24,123.56.108.0/24,123.56.120.0/24,123.56.137.0/24,123.56.148.0/24,123.56.164.0/24,123.57.48.0/24,182.92.153.0/24,182.92.186.0/24,101.200.174.0/24,101.200.160.0/24,101.200.176.0/24,47.94.36.0/24,47.94.47.0/24,101.201.214.0/24,101.201.82.0/24,123.56.182.0/24,101.201.105.0/24,182.92.132.0/24,60.205.157.0/24,101.201.107.0/24,60.205.164.0/24,60.205.165.0/24,59.110.4.0/24,59.110.17.0/24,123.56.186.0/24,60.205.146.0/24,59.110.37.0/24,59.110.19.0/24,60.205.112.0/24,60.205.243.0/24,101.201.108.0/24,59.110.38.0/24,60.205.197.0/24,60.205.166.0/24,101.200.194.0/24,101.200.182.0/24,123.57.204.0/24,101.200.235.0/24,123.57.206.0/24,123.57.65.0/24,47.94.167.117/32,182.92.157.129/32,101.200.39.123/32,101.200.192.4/32,39.105.58.165/32,101.200.213.59/32,59.110.164.0/24,47.94.150.0/24,39.105.56.0/24,47.93.21.0/24,47.93.30.0/24,47.93.24.0/24,60.205.222.0/24,60.205.186.0/24,47.93.22.174/32,47.93.10.168/32,47.94.246.43/32,47.94.94.233/32,47.95.241.173/32,59.110.155.242/32,60.205.230.219/32,101.200.50.74/32,101.201.65.33/32,112.126.96.49/32,112.126.96.184/32,112.126.98.30/32,112.126.99.22/32,112.126.99.87/32,112.126.99.205/32,39.105.247.0/24|
|China \(Zhangjiakou\)|47.92.22.0/24,47.92.185.0/26,47.92.185.64/26,47.92.185.128/26,47.92.185.192/26,39.98.96.0/26,39.98.96.128/26,39.98.96.192/26,39.98.96.64/26|
|China \(Hohhot\)|39.104.29.0/24|
|China \(Shenzhen\)|112.74.0.0/16,120.24.0.0/16,120.25.0.0/16,120.78.6.0/24,120.78.5.0/24,47.115.165.0/24,47.115.166.0/24,47.115.162.0/24,47.115.161.0/24,120.24.65.0/24,120.24.67.0/24,120.24.160.0/24,120.25.215.0/24,120.24.214.0/24,120.24.223.0/24,120.25.124.0/24,120.25.107.0/24,120.25.79.0/24,112.74.211.0/24,120.24.174.0/24,120.24.173.0/24,120.25.150.0/24,112.74.98.0/24,120.25.123.0/24,112.74.97.0/24,47.106.221.0/24,120.78.184.0/24,47.107.118.0/24,47.106.38.0/24,39.108.66.0/24,39.108.110.0/24|
|China \(Hong Kong\)|203.88.163.0/24,47.90.37.0/24,47.90.38.0/24,47.89.39.0/24,47.52.111.0/24,47.52.25.202/32,47.91.228.249/32,47.52.166.98/32,47.244.33.65/32,47.244.35.187/32|
|Singapore|47.88.133.0/24,47.88.139.0/24,47.74.206.0/24,47.88.235.0/24,47.88.139.0/24,11.193.8.0/24,10.45.241.0/24,10.45.244.0/24,10.45.245.0/24|
|Australia \(Sydney\)|47.91.49.0/24,47.91.50.0/24|
|Malaysia \(Kuala Lumpur\)|47.254.212.0/24,120.55.129.0/24,112.124.140.0/24|
|Indonesia \(Jakarta\)|149.129.228.0/24,149.129.229.0/24|
|India \(Mumbai\)|149.129.164.0/24,147.139.21.0/24|
|Japan \(Tokyo\)|47.91.9.0/24,47.91.13.0/24,47.91.27.0/24|
|US \(Silicon Valley\)|198.11.174.0/24,198.11.175.0/24,47.89.244.175/32|
|US \(Virginia\)|47.89.170.0/24,47.88.98.0/24,47.250.29.0/24|
|Germany \(Frankfurt\)|47.91.82.0/24,47.91.83.0/24,47.91.84.0/24|
|UK \(London\)|8.208.17.0/24|
|UAE \(Dubai\)|47.91.102.0/24,47.91.103.0/24|

**Reachable from an internal network:**If DTS accesses your user-created database over a virtual or physical internal network, such as Express Connect, VPN Gateway, and Smart Access Gateway, use the following table to obtain the DTS IP ranges \(CIDR blocks\) for your selected region:

**Note:** When DTS adds new servers, your user-created database may become inaccessible if you do not update the whitelist settings in a timely manner. To avoid this issue, we recommend that you whitelist the 100.104.0.0/16 IP range for all user-created databases that are reachable from an internal network.

|Region|IP range|
|------|--------|
|China \(Hangzhou\)|100.104.52.0/24,100.104.61.128/26,100.104.244.64/26,100.104.216.192/26|
|China \(Shanghai\)|100.104.205.0/24,100.104.226.128/26|
|China \(Qingdao\)|100.104.72.0/24|
|China \(Beijing\)|100.104.183.0/24,100.104.236.128/26,100.104.227.192/26|
|China \(Zhangjiakou\)|100.104.175.0/24|
|China \(Hohhot\)|100.104.72.0/24|
|China \(Shenzhen\)|100.104.75.64/26,100.104.235.192/26,100.104.205.0/24|
|China \(Hong Kong\)|100.104.233.0/24|
|Singapore|100.104.188.0/24,100.104.207.128/26|
|Australia \(Sydney\)|100.104.233.0/24|
|Malaysia \(Kuala Lumpur\)|100.104.5.0/24|
|Indonesia \(Jakarta\)|100.104.175.0/24|
|India \(Mumbai\)|100.104.8.0/24|
|Japan \(Tokyo\)|100.104.112.0/24|
|US \(Silicon Valley\)|100.104.175.0/24|
|US \(Virginia\)|100.104.233.0/24|
|Germany \(Frankfurt\)|100.104.5.0/24|
|UK \(London\)|100.104.133.64/26|
|UAE \(Dubai\)|100.104.205.0/24|

## Whitelist the IP range

After obtaining the IP range, you need to whitelist the IP range in the security settings of your user-created database system. The actual procedure may vary, depending on your system deployment. For example, if your database is deployed behind a firewall, you need to configure the security rules to allow the DTS IP range to access your database server. If your database is deployed on a virtual machine offered by a third-party vendor, you need to configure the security groups to allow the DTS IP range to access your virtual machine.

