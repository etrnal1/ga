### `功能`  



| 日志     |                                                           |
| -------- | --------------------------------------------------------- |
|          | 查看日志,下一页 ，按照顺序，修改了某个用户 打印了某个胸卡 |
|          | 用户的活动需要进行记录                                    |
| 功能测试 | 当用户 进行注册和添加备注信息的时候，日志这边能进行输出   |



#### 查看日志

##### 读取用户的表进行数据查询



#####  写一个方法 user   找到管理员的姓名 两表查询



读取id,note,app_id   crl  ip   

 用户表  user  

​    

######   需要查询 管理员的姓名   备注信息

####  删除日志

#### 显示分页

每页显示几条记录 

和当前是第几页

每页显示几条记录

$pagesize

$offset 记录偏移量

offset  =$ pagesize* (page-1)

$page  计算总页数

select * from table limit 





### 使用分页功能



第一个参数传递控制器，第二个方法传递方法，第三个传递参数

$this->url('','','');

```php
分页url  
$pageurl  当前网址

系统封装好的方法
$this-url 
$offset  计算偏移量
$page  计算当前页数
$num  计算页数
phpok_page() 传递的参数
```



#### 处理日志的部分

我需要理清楚  用户做了哪些操作 是如何通过日志生成的;

管理员去查看   这些log日志是从数据库读取出来的

当管理员去操作的时候。如何记录操作行为

#### git 添加了两个文件夹



  log-master 日志文件夹

zfegg-admin-operation-log 日志文件夹

#### 记录用户操作行为 

  seaslog   是第三方轮子





##### 第三方轮子 

​    

##### 当用户进行登录的时候，获取用户的登录地址与控制器里面的方法，写入到数据库里面的log表进行记录

  记录信息,在需要的地方定义好事件和监听器

##### 记录 控制器与方法

​     

​       代码里面要获得控制器 与方法

##### 记录用户的行为

##### 记录用户的ip 

##### 记录用户的 时间 



#### log 控制器写了两个方法 

  一个用来取得log 表里的数据,一个用来删除管理员表的数据

找到对应的log  对应的控制

   去读取日志用户里面的信息,

```php+HTML
<?php
/**
 * 日志管理
 * @package phpok\admin
 * @作者 qinggan <admin@phpok.com>
 * @版权 深圳市锟铻科技有限公司
 * @主页 http://www.phpok.com
 * @版本 4.x
 * @许可 http://www.phpok.com/lgpl.html PHPOK开源授权协议：GNU Lesser General Public License
 * @时间 2017年05月07日
**/
if(!defined("PHPOK_SET")){exit("<h1>Access Denied</h1>");}
class log_control extends phpok_control
{
	public function __construct()
	{
        
        //访问自己的基类
		parent::control();
	}

	public function index_f()
	{
        
       //获取网站关键词
		$keywords = $this->get('keywords');

		// 获取当前的网址，传入log 控制器
		$pageurl = $this->url('log');
		$condition = '1=1';
        
        // 如果关键词存在的话
		if($keywords){
			$pageurl .= "&keywords=".rawurlencode($keywords);
			$this->assign('keywords',$keywords);
			$condition .= " AND l.note LIKE '%".$keywords."%'";
		}
        // admin  获取y
		$adminer = $this->get('adminer');
		if($adminer){
			$pageurl .= "&adminer=".rawurlencode($adminer);
			$this->assign('adminer',$adminer);
			$condition .= " AND a.account='".$adminer."'";
		}
        
        //类似于tp 的助手函数
		$user = $this->get('user');
        //如果存在
		if($user){
			$pageurl .= "&user=".rawurlencode($user);
			$this->assign('user',$user);
			$condition .= " AND a.user='".$user."'";
		}
        //获取posetion 管理员角色
		$position = $this->get('position');
		if(!$position){
			$position = 'admin';
		}
        //拼接角色
		$pageurl .= "&position=".$position;
        //分配变量
		$this->assign('position',$position);
		$condition .= " AND l.app_id='".$position."'";
        //获得开始时间
		$start_time = $this->get('start_time');
        
		if($start_time){
			$pageurl .= "&start_time=".rawurlencode($start_time);
			$this->assign('start_time',$start_time);
			$condition .= " AND l.dateline>=".strtotime($start_time);
		}
		$stop_time = $this->get('stop_time');
		if($stop_time){
			$pageurl .= "&stop_time=".rawurlencode($stop_time);
			$this->assign('stop_time',$stop_time);
			$condition .= " AND l.dateline<=".(strtotime($stop_time) + 24 * 3600);
		}
		$psize = $this->get('psize','int');
		if(!$psize){
			$psize = $this->config['psize'] ? $this->config['psize'] : 20;
		}
		$pageurl .= "&psize=".rawurlencode($psize);
		$this->assign('psize',$psize);
        
		$pageid = $this->get($this->config['pageid'],'int');
		if(!$pageid){
			$pageid = 1;
		}
		$offset = ($pageid-1) * $psize;
		$total = $this->model('log')->get_count($condition);
        // 去查log 文件
		if($total>0){
			$rslist = $this->model('log')->get_list($condition,$offset,$psize);
			$this->assign('rslist',$rslist);
			$this->assign('pageid',$pageid);
			$this->assign('offset',$offset);
			$this->assign('psize',$psize);
			$string = 'home='.P_Lang('首页').'&prev='.P_Lang('上一页').'&next='.P_Lang('下一页').'&last='.P_Lang('尾页').'&half=5';
			$string.= '&add='.P_Lang('数量：').'(total)/(psize)'.P_Lang('，').P_Lang('页码：').'(num)/(total_page)&always=1';
			$pagelist = phpok_page($pageurl,$total,$pageid,$psize,$string);
			$this->assign('pagelist',$pagelist);
		}
		$this->addjs('js/laydate/laydate.js');
        
        // 分配模板变量
		$this->view('log_index');
	}

	public function delete_f()
	{
        
        //管理员
		if(!$this->session->val('admin_rs.if_system')){
			$this->error(P_Lang('只有系统管理员才有此权限'));
		}
		$id = $this->get('id','int');
		$ids = $this->get('ids');
		$date = $this->get('date','int');
		if(!$id && !$ids && !$date){
			$this->error(P_Lang('参数不完整！'));
		}
		if($id){
			$condition = "id='".$id."'";
			$tip = P_Lang('删除日志#{id}',array('id'=>$id));
		}
		if($ids){
			$lst = explode(",",$ids);
			foreach($lst as $key=>$value){
				if(!$value || !trim($value) || !intval($value)){
					unset($lst[$key]);
					continue;
				}
				$lst[$key] = intval($value);
			}
			$ids = implode(",",$lst);
			if(!$ids){
				$this->error(P_Lang('未指定要删除的日志'));
			}
			$condition = "id IN(".$ids.")";
			$tip = P_Lang('删除日志#{id}',array('id'=>$ids));
		}
		if($date){
			$time = strtotime(date("Y-m-d",$this->time)) - $date*24*60*60;
			$condition = "dateline<".$time;
			$tip = P_Lang('删除{date}天前的日志',array('date'=>$date));
		}
		$this->model('log')->delete($condition);
		$this->model('log')->save($tip);
		$this->success();
	}
}

```



操作是如何写入到表里面的

   $list = false;



// 去列表里面去读取用户 信息

  $rslist 



如果存在去读取 键和值

   list 控制器 action 方法 