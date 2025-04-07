## Dome Sever上传Factory logs到Google SFTP Server方法:
# 准备事项:
> 参考链接
* [Process on SFTP Service for Partners](https://chromeos.google.com/partner/dlm/docs/factory/process_on_sftp_Service.html)


1. 针对每台需要访问google SFTP server的dome server，需要先申请public key, 然后将所有dome server创建的public key上传到issue tracker上请gogole 开通工厂dome server访问google SFTP server的权限. 申请后dome server 如有重新安装,需要重新创建public key和申请访问权限

2. 访问Google SFTP server 账号: cpfe-quanta@partnerupload.google.com 

3. 需要访问google SFTP server的dome必须连接VPN 网, 146/166/186/84 已在2023/06 配备VPN 网


# 上传方法:
1. 手动upload factory logs 指令:
```
# 登录至Google SFTP, 例如:sftp -P 19321 -i /home/sysadmin/.ssh/id_rsa cpfe-quanta@partnerupload.google.com
sftp -P 19321 -i PATH/TO/PRIVATE/KEY USERNAME@partnerupload.google.com
# 上传factory logs 指令
put PATH/TO/ARCHIVE
# 退出google SFTP server
quit
```

2. 自动收集、压缩、上传factory logs 至Google SFTP server by script:
```
# 为了方便，我们统一将每台dome server /home/sysadmin/下创建了文件夹pubkey_for_upload_report_to_sftp 用于存放KEY和script,请注意script 一定要使用最新版本,如果不是请至git ~/shopfloor/scripts/ 去copy.
sudo bash /home/sysadmin/pubkey_for_upload_report_to_sftp/factory_report_integrate_and_upload.sh
# 自20230612 dome server 146/166/186/84 已全部配备好VPN 网络并实现每日定时自动收集&上传factory logs到google SFTP server.
# 但需要配置linux定时任务crontab, 方法有如下两种:
方法一:首先执行指令“sudo su”去进入root 用户，然后执行“crontab -u root -e” 去增加自动收集&上传的script 路径
方法二:直接执行指令“sudo crontab -u root -e”直接使用root 启动命名并添加script路径实现定时任务
查看当前用户的定时任务
``crontab -l``
查看root用户的定时任务
``sudo crontab -u root -l``
```

# 注意事项： 
1.  **<font color="red">重中之重:</font>**

   * 特别注意, 所有新机种upload factory logs前首先进Google SFTP server先创建对应的机种文件夹，格式**Google Name + _ + Report**              
  
     例如：`lazor_report`, `cozmo_report` ... 

   * 进入SFTP Server方法见上文 <font color="blue">**手动upload factory logs 指令**</font>
   
2. 特别注意，所有新机种upload factory logs前需将新机种quanta name 与Google name对应关系维护进**factory_report_integrate_and_upload.sh**,否则script 无法执行收集和上传logs.
	例如： 
	    "0H7") 
              google_name=scout;;
            "GML")  
              google_name=bobba;;
            "0RK")
              google_name=quackingstick;;
            "ZAC")
              google_name=magister;;

3. DOME Bundle 命名规则： *广达机种名 + SF + Umpire Port + 创建时间*， 例如： `ZBR_SF_12470_20220924`

4. 在上传过程中禁止移除网线， 避免漏传和文件丢失

## 从SFTP上下载Report的方法：
1. 登陆到Google SFTP服务器
```
sftp -P 19321 -i /home/sysadmin/pubkey_for_upload_report_to_sftp/quanta_pu4_upload_report_public_key cpfe-quanta@partnerupload.google.com
```

2. 找到机种report path, 使用<font color="red">**`ls`**</font>， 然后进入对应路径

3. 然后使用命令下载, 例如：
```
sftp> cd cozmo_report/
sftp> get cozmo_ZDG_20230217_842c39fbed_20230310083300.tar.bz2
Fetching /cozmo_report/cozmo_ZDG_20230217_842c39fbed_20230310083300.tar.bz2 to cozmo_ZDG_20230217_842c39fbed_20230310083300.tar.bz2
/cozmo_report/cozmo_ZDG_20230217_842c39fbed_20230310083300.tar.bz2
```

	

