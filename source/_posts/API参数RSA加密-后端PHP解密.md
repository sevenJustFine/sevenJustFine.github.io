---
title: RSA密钥对 生成
date: 2022-10-13 12:00:00
tags:
---
* RSA密钥对 生成

```shell

#生成私钥
openssl genrsa -out rsa_1024_priv.pem 1024  

#生成公钥
openssl rsa -pubout -in rsa_1024_priv.pem -out rsa_1024_pub.pem

```

* api请求使用公钥加密参数

```js

<textarea name="" id="key" cols="200" rows="10">-----BEGIN PUBLIC KEY-----
    ############################################################
    ############################################################
    ############################################################
    ############################################################
    -----END PUBLIC KEY-----
</textarea>


<script src="/admin/static/jsencrypt.min.js"></script>
<script type="text/javascript">
    $(function () {
        $('#submit').click(function () {
            test();
        })
    })

    function test() {
    let encrypt = new JSEncrypt();
    let pub_key = $('#key').val();
    encrypt.setPublicKey(pub_key);
    let data = encrypt.encrypt("123456");
    let csrf = '@csrf';
    csrf = $(csrf).val();
    $.post('{{url("admin/~~test~~/ajax")}}', {data: encodeURI(data).replace(/\+/g, '%2B'), _token: csrf}, function (res) {
            console.log(res);
        }, 'json')
    }
</script>




```

* 后端用私钥解密

```php

$data = request()->post('data');
$private_key = file_get_contents(storage_path('rsa_1024_priv.pem'));//读取私钥
$privateKey = openssl_pkey_get_private($original);//这个函数可用来判断私钥是否是可用的，可用，返回资源，私钥可用file_get_content从文件中获取
$encrypted_data=null;
$result = openssl_private_decrypt(base64_decode(urldecode($data)), $encrypted_data, $private_key);


dump($result);
dump($encrypted_data);
exit;
 

```