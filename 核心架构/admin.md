## 创建产品

```php
config/activity.php
app/http/Controllers/crm/ProductsController.php
保存后在DB_CONNECTION = mysql下的 ccbhd下的 products表
```

### 1.1、模板参数

```
activity.status //开启状态
activity.is_white_on //白名单
activity.is_black_on //黑名单
activity.is_valid //是否有效
templet_set.is_lamp_on //跑马灯开关
templet_set.custom_lamp_text //跑马灯自定义文案
templet_set.nowin_subtitle //未中奖文案
indicatorTask.complete_one //循环次数
indicatorTask.ccb_feedback_number //回馈编号  达标查询使用
indicatorTask.service_no //
signup.default_num //初始报名机会
signup.max_num //最大报名次数
signup.day_max_num //每天报名次数
signup.max_person //最大报名人数
signup.validator //验证字段
signup.is_batch //是否跑批
```

```
{
    "activity": {
        "title": "建行跨境分会场",
        "start_time": "now",
        "end_time": "today +7 day 0:0:0",
        "platforms": 41,
        "status": 2,
        "is_white_on": "2",
        "is_black_on": "1",
        "is_valid": 1,
        "pic_url": "https://ccbhdimg.kerlala.com/hd/crossborder/v1/b.jpg",
        "rules": "默认规则",
        "share_set": {
            "share_title": "默认",
            "share_desc": "默认",
            "share_pic": "https://ccbhdimg.kerlala.com/hd/community/v1/thumb.png"
        },
        "templet_set": {
            "background_pic": "https://hdimg.kerlala.com/hd/community/v1/cover.png",
            "is_draw_info_on": "1",
            "is_indicator_on": "2",
            "is_lamp_on": "1",
            "custom_lamp_text": "",
            "person_is_show": "1",
            "no_white_text": "很遗憾您暂时还不能参与~",
            "logo_url": "https://ccbhdimg.kerlala.com/hd/czdraw/wheelnew_v1/logo.png",
            "logo_is_show": "1",
            "sub_name": "",
            "title_pic": "https://ccbhdimg.kerlala.com/hd/lctopic/title.png",
            "duihuan_url": "",
            "home_url": "",
            "prize_url": "",
            "ccb_detail_url": "",
            "nowin_subtitle": ""
        },
        "sys_templet_set": {
            "ccb_activity_number": "",
            "ccb_feedback_number": "",
            "java_ccb_guid": ""
        },
        "crm_templet_set": [],
        "ext": {}
    },
    "prize": [
        {
            "name": "奖品",
            "jackpot": 3,
            "pic_url": "https://ccbhdimg.kerlala.com/hd/common/prize_default.png",
            "type": "jccb",
            "is_dzq": 2,
            "num": 0,
            "money": 0,
            "probability_num": 0,
            "prize_max_win_num": 0,
            "prize_person_max_win_num": 0,
            "prize_person_day_max_win_num": 0,
            "is_valid": 1,
            "desc": "请您输入奖品说明",
            "other_temp_set": {
                "ccb_rights_number": "",
                "ccb_bkright_actnum": "",
                "ccb_bkright_num": ""
            }
        }
    ],
    "goods": [],
    "indicatorTask": [
        {
            "id": "",
            "indicator_id": "",
            "complete_one": 0,
            "number_limit": 1,
            "limit_status": "1",
            "task_link": "",
            "ccb_feedback_number": "",
            "service_no": "",
            "sort": 1,
            "show": 1,
            "ext": {
                "prize_id": ""
            },
            "is_loop": "2",
            "day_limit": 0,
            "every_day_num": 0
        }
    ],
    "advert": [
        {
            "title": "",
            "position": "",
            "img_url": "",
            "img_link": "",
            "img_title": "",
            "is_show": 2
        }
    ],
    "draw": {
        "probability": 1,
        "draw_num_type": 1,
        "default_draw_num": 0,
        "day_add_draw_num": 5,
        "person_day_max_draw_num": 0,
        "person_max_draw_num": 0,
        "person_day_max_win_num": 0,
        "person_max_win_num": 0,
        "day_max_win_num": 0,
        "win_start_time": 0,
        "win_end_time": 24,
        "forward_person_num": 0,
        "forward_draw_count": 0,
        "forward_draw_count_limit": 0,
        "nowin_text": "",
        "nowin_subtitle": "",
        "redpack_money": 0,
        "many_probability_set": "",
        "setting": ""
    },
    "signup": {
        "start_time": 0,
        "end_time": 0,
        "default_num": 1,
        "max_num": 1,
        "day_max_num": 0,
        "max_person": 0,
        "validator": "phone,idcard4,idcardtype",
        "is_batch": 1,
        "setting": {
            "ccbsms": 1,
            "success_text": "报名成功",
            "fail_text": ""
        }
    }
}
                           
```
