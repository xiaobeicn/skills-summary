### 基于用户投票的排名算法的PHP实现之 Hacker News 篇
  
```php
//将排名下拉的重力因子
//该因子决定了post 的下降速度, 值越高排名下降越快, 时效性的参数之一
define('RANK_G', 1.8);
//获得的投票
$vote = 100;
//数据创建时间
//$created = strtotime('-1 days');
//$created = strtotime('-1 hours');
$created = strtotime('-3 days');
//距离创建时间的小时数, 时效性的参数之二
$hourDiffCreated = (time() - $created) / 3600;
//详细算法
$socre = ($vote - 1) / pow(($hourDiffCreated + 2), RANK_G);
echo $socre, "\n";
```
  
### 基于用户投票的排名算法的PHP实现之 Reddit 篇
  
```php
//获得赞成票
$vote = 100;
//获得的反对票
$devote = 99;
//投票差额
$voteDiff = $vote - $devote;
//投票方向
if($voteDiff > 0) {
    $pos = 1;
} elseif($voteDiff < 0) {
    $pos = -1;
} else {
    $pos = 0;
}
//帖子的争议度(赞成/否定)
$voteDispute = $voteDiff != 0 ? abs($voteDiff) : 1;

//项目创建时间
$fund = strtotime('2012-09-01');
//数据创建时间
$created = strtotime('-3 days');
//考虑时间因素, 用来拉低比较老的post的分数
$time = $created - $fund;
//详细算法
$socre = log10($voteDispute) + $pos * $time / 45000;
echo $socre, "\n";
```
  
> * via: http://xwsoul.com/posts/444
> * via: http://xwsoul.com/posts/449
  
