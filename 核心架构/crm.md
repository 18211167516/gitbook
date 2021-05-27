## 创建活动

> [!TIP]
> 核心关键点
> 在路由：web.php下activitys.create
> 
> 控制器：Http\Controllers\Activity\ActivitysController.php
> 
> Servers/ActivitysService.php create

### 1. 获取产品信息（products）

```php
    getProductParams($productId)
```

### 2. 获取主账号信息

```php
//可以考虑 Admin::getMasterAdminId() 优化
$masterAdminInfo = Admin::findOrFail(Admin::getMasterAdminId());
    
```

### 3. 初始化活动总表

```php
    initRoute()

    1、生成数据到activity_route
    2、生成sn
    3、计算分表规则 activity_route->table 
    4、计算是否有组件需要再次分表规则 activity_route->sub_table
    5、更新表、返回活动信息
```

### 4. 全局request类添加attributes

```php
request()->attributes->add([
    'activityId' => $activity->id, //活动ID  全局id
    'activitySn' => $activity->sn, //活动sn
    'activityType' => $activity->activity_type, //活动类型
    'database' => 'activity_' . $activity->database, //落得库
    'masterId' => $activity->master_id,//主账号ID
    'table' => $activity->table //分表
]);

主要在Model层使用

```
> [!TIP]
> Model\Traits\InitTable.php

```php
$activityType = request()->attributes->get('activityType');

//理论上在获取到主账号信息的时候就应该往request加，应该不需要从缓存或者表里取
$masterId = request()->attributes->get('masterId') ?? Admin::getMasterAdminId();
//分表
$table = request()->attributes->get('table') ? '_' . request()->attributes->get('table') : '';
      
//理论上在获取到主账号信息的时候就应该往request加，应该不需要从缓存或者表里取
$database = request()->attributes->get('database') ?? 'activity_' . Cache::rememberForever('database_' . $masterId, function () use ($masterId) {
        return Admin::where('id', $masterId)->value('database');
});
//二级分表
$subList = request()->attributes->get('sub_table');
$subTable = 0;
if (!empty($subList)){
    foreach($subList as $k => $v)
    {
        if(stripos($this->table,$k) !== false)
        {
            $subTable = $v;
            break;
        }
    }
}
$this->table = sprintf(
    $this->table, 
    $activityType,//%$1s
    $masterId,//%$2d
    $table,//%$3s
    $subTable ? "_".$subTable :""//%$4s
);

//连接的库  crm_admins的database  格式 activity_0
$this->connection = $database;  
parent::__construct($attributes);
```
### 5. 初始化表 initTable
```php 
1、查询出所有待初始化的表并合并
// 查询主账号分表模板表
$tables1 = DB::select("SHOW TABLES LIKE 'tpl_pub_%'");
// 查询所有活动类型公用的活动下业务表模板表
$tables2 = DB::select("SHOW TABLES LIKE 'tpl_activity_%'");
// 查询活动类型下业务表或活动扩展表模板表（兼容旧）
$tables3 = DB::select("SHOW TABLES LIKE 'tpl_{$activity->activity_type}_%'");
$tables = array_merge($tables1, $tables2, $tables3);
// 查询活动组件模板表  tables4
foreach ($componentIds as $component) {
    if (! empty($this->componentConfig[$component])) {
        $tables = array_merge($tables, DB::select("SHOW TABLES LIKE 'tpl_component_{$this->componentConfig[$component]}_%'"));
    }
}

2、循环所有表，通过不同的规则生成表

```

> [!TIP]
> table1  

> 生成规则例子：tpl_pub_activity = sprintf('activity_%d',$masterId) //按主账号分表
可以考虑在创建主账号的时候就生成主账号表

```sql
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_pub_%'
```


|表名|注释|
|---|---|
|tpl_pub_activity|活动汇总基础信息表|
|tpl_pub_activity_benefits|活动权益表|
|tpl_pub_activity_benefits_batch|活动权益批次表|
|tpl_pub_activity_benefits_stock|活动权益库存表|
|tpl_pub_activity_benefits_tongji|活动权益统计表|
|tpl_pub_activity_indicator|活动指标表|
|tpl_pub_advert|广告表|
|tpl_pub_answer_group|答题分组表|
|tpl_pub_answer_question|答题题目表|
|tpl_pub_assetenhance_invitation_list|建行代发资产-邀请列表|
|tpl_pub_assetenhance_set_invitation|建行代发资产-邀请好友设置表|
|tpl_pub_assetenhance_set_raking|建行代发资产-邀请好友排行榜|
|tpl_pub_assetenhance_set_user|建行代发资产-本人资产提升设置表|
|tpl_pub_black_coupon|黑名单导入批次表|
|tpl_pub_black_fail_log|黑名单导入错误日志|
|tpl_pub_black_list|黑名单表|
|tpl_pub_component_comment|评论组件表|
|tpl_pub_component_draw|抽奖组件表|
|tpl_pub_component_friend|好友组件配置表|
|tpl_pub_component_help|助力组件表|
|tpl_pub_component_rank|榜单组件表|
|tpl_pub_component_signup|报名组件表|
|tpl_pub_component_task|任务组件表|
|tpl_pub_gather_activity_draw_user_ext|抽奖组件用户扩展-汇总表|
|tpl_pub_gather_activity_draw_user_win|抽奖用户中奖表-汇总表|
|tpl_pub_gather_activity_user|活动用户用户-汇总表|
|tpl_pub_goods|商品表|
|tpl_pub_goods_code|电子券码表|
|tpl_pub_goods_coupon|电子券导入批次表|
|tpl_pub_goods_stock|商品库存表|
|tpl_pub_goods_user_win|商品中奖记录表|
|tpl_pub_group|活动分组表|
|tpl_pub_indicator|指标表|
|tpl_pub_integrate_activity|
|tpl_pub_pay_order|支付订单表|
|tpl_pub_prize|奖品基础信息表|
|tpl_pub_prize_code|电子券码表|
|tpl_pub_prize_coupon|电子券导入批次表|
|tpl_pub_prize_stock|奖品库存表|
|tpl_pub_purchase_goods_pool|一分购商品池|
|tpl_pub_redpack_log|红包发放日志|
|tpl_pub_salary_task|代发工资-任务表|
|tpl_pub_salary_user_ext|用户扩展表|
|tpl_pub_salary_user_task|代发薪人礼-任务完成表|
|tpl_pub_wage_task|代发工资-任务表|
|tpl_pub_wage_user_task|代发工资任务完成表|
|tpl_pub_white_coupon|白名单导入批次表|
|tpl_pub_white_fail_log|白名单导入错误日志|
|tpl_pub_white_list|白名单列表|
|tpl_pub_white_list_log|白名单日志|

-----------------

> [!TIP]
> tables2

> 生成规则：tpl_activity_ccb_order = sprintf("%s_ccb_order_%d_%d",$activity_type,$masterId,$table); $table 可以没有

```sql
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_activity_%'
```


|表名|注释|
|---|---|
|tpl_activity_ccb_order|用户ccb订单表|
|tpl_activity_indicator_result|指标完成结果表|
|tpl_activity_indicator_result_daylog|指标完成天结果表|
|tpl_activity_indicator_result_log|指标结果日志表|
|tpl_activity_share_visit_log|分享访问日志|
|tpl_activity_user|活动用户用户表|

-----------

> [!TIP]
> tables3
> 
> 生成规则：tpl_crossborder_user_ext = sprintf("crossborder_user_ext_%d_%d",$masterId,$table);//$table 可以为空

```sql
//crossborder是活动类型
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_crossborder_%' 
```


|表名|注释|
|---|---|
|tpl_crossborder_user_ext|跨境分会场活动用户扩展表|
----------


> [!TIP]
> tables4 组件表（精力有限先分析3个）

> 生成规则：tpl_component_draw_ccb_right_order = sprintf('%s_component_draw_ccb_right_order_%d_%s',$activity_type,$masterId,$table,$sub_tab)
$table 可以为空，$sub_table 大部分为空

```sql
//抽奖
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_component_draw%' 
//报名
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_component_signup%' 
//任务
SELECT CONCAT_Ws('|',TABLE_NAME,TABLE_COMMENT) FROM information_schema.tables where table_schema = 'ccbhd' and table_name LIKE 'tpl_component_task%' 
```

> [!TIP]
> 抽奖组件

|表名|注释|
|---|---|
|tpl_component_draw_ccb_right_order|建行权益订单表|
|tpl_component_draw_ccb_right_user|建行权益用户表|
|tpl_component_draw_user_draw_log|抽奖组件抽奖日志表|
|tpl_component_draw_user_draw_num_log|抽奖组件用户抽奖次数变更日志表|
|tpl_component_draw_user_ext|抽奖组件用户扩展表|
|tpl_component_draw_user_win|抽奖组件用户中奖表|

----------------------

> [!TIP]
> 报名组件

|表名|注释|
|---|---|
|tpl_component_signup_callback|报名组件-跑批回馈表|
|tpl_component_signup_list|报名组件-纪录表|
|tpl_component_signup_relation|报名组件-报名&批次关系表|

-------------------

> [!TIP]
> 任务组件

|表名|注释|
|---|---|
|tpl_component_task_finish|指标完成结果表|
|tpl_component_task_reward|任务奖励|
|tpl_component_task_set|任务配置|
|tpl_component_task_user_result|用户结果集|

-------------------

### 6. 创建活动 initActivity

```php
    activity_10000 10000是主账号 插入默认数据
```

### 7. 添加组件 componentCreate

```php
 foreach ($components as $key => $component) {
            if (in_array($key, $this->componentConfig)) {
                $component_class_name = '\\App\\Models\\Component\\' . ucfirst($key);
                $component_class_name::create([
                    'activity_id' => $activityId
                ] + $component);
            }
        }
```

### 8. 添加默认奖品 prizeCreate

```php
foreach ($prizes as $prize) {
    //没有看懂为什么要app这种方式
            $data[] = app(PrizesService::class)->create($activityId, $activityType, $prize['jackpot'] ?? 1, $prize)['info'];
}


奖品表：prize_10000

//是不是可以tpl_pub_prize_code  放到当前PrizesService.php
$data->code_table = $data->id % app(ActivitysService::class)->getTableConfig()['tpl_pub_prize_code'];

库存表：prize_stock_10000

```

### 9. 添加默认商品 goodsCreate

```php
GoodsService->create

商品表：goods_10000
库存表：goods_stock_10000

```

### 10. 添加默认广告 advertCreate

```php
广告表：advert_10000
```

### 11. 添加活动扩展 typeCreate

```php

$typeServiceClass = '\\App\\Services\\' . ucfirst($activityType) . 'Service';
$typeModelClass = '\\App\\Models\\Activity\\' . ucfirst($activityType);
// 活动类型扩展专属的初始化逻辑
if (method_exists($typeServiceClass, 'create')) {
    // ...param  $prizesData, $goodsData,$product
    $return = app($typeServiceClass)->create($activityId, $activityData, ...$param);
} elseif (class_exists($typeModelClass)) {
    $return = $typeModelClass::create([
        'activity_id' => $activityId
    ] + $activityData);
}
```

### 12. 返回结果前台重新安编辑进活动


```php
左边的可见视图 是iframe直接引入的前台的地址 

https://jxjkhd.kerlala.com/a/e/QPyoryZj?t=1621325920&s=b04d665f1546d849dc2635c2e7b3646a8d35e3ff16a26cc7d635e597818a4d44

```

### 13. 重要接口 activitys/getEditApiList

> 如何自定义返回接口（活动类型特殊）

```php
App\Services\Common\SpecialService
```

