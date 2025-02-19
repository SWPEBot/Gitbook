# Chrome OS Regstration Code

> Offical link: <https://chromeos.google.com/partner/dlm/docs/factory/factoryregistrationguide.html>

Note： Offical link is Confidential file, you need premission to access.

## 申请 Google Key

1. 准备在 Ubuntu Server 使用 gpg 生成 Key

    `gpg --gen-key`

2. 使用默认选项,填写邮件及密码

    ```bash
    Algorithm: Choose (1) RSA and RSA.
    Key Size: 2048
    Key never expires
    Real Name: board-crosreg-key
    Email Address: <Choose anything> Helin.liu@quantacn.com
    Comment: quanta_name
    Passphrase: <Choose an appropriate passphrase for the private key>
    ```

    _密码通常使用 google_name-quanta_

3. 导出私钥并将其存储

    `gpg --export-secret-key "board-crosreg-key" > board_crosreg.priv`

4. 导出公钥

    `gpg --export -a "board-crosreg-key" > board_crosreg.pub`

    _设置好后创建文件进行测试，如果是提交 Google 使用 google-cros-key，如果不是谷歌，请使用项目名称._

5. 加密

    `gpg --encrypt --local-user "board-crosreg-key" --recipient "board-crosreg-key" file`

    回传至 Google
    示例:`gpg --encrypt --local-user "<board>-crosreg-key" --recipient "<board>-crosreg-key" <need encrypt file>`

6. 解密

    `gpg -o newfilename --decrypt need_decrypt_filename.gpg`

    示例:`gpg -o xxxx --decrypt xxxx.gpg`

7. 上传到 CPFE 并申请 Google Key

    upload `board_crosreg.pub` to DLM.

## 删除无用密钥(gpg keys)

List keys :

`gpg –list-keys`

Delete keys :

```bash
pub   2048R/325D025A 2021-02-22
uid                  volta-crosreg-key (zbu) <young.yang123@quantacn.com>
sub   2048R/71C7EEE7 2021-02-22
```
`gpg --delete-secret-and-public-keys key-id{71C7EEE7}`


### 以上是传统方式， 如下是避免持续输入字符的方法
1. 进入DLM系统， 点选要申请的机种
2. 将公共的密钥(目前在10.18.6.84/quanta-pu4-crosreg.pub)上传至DLM系统
3. 选择数量，点击下载即可
