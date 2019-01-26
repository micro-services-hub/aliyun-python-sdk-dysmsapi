# aliyun-python-sdk-dysmsapi
Python SDK for Aliyun sms services. officially supports Python 2.7 & 3.3-3.7


## INSTALL
```
pip install git+https://github.com/micro-services-hub/aliyun-python-sdk-dysmsapi.git
```

## Requirement.txt setup
```text
-e git+https://github.com/micro-services-hub/aliyun-python-sdk-dysmsapi@master#egg=AliyunDysmsapi
```

## DOC
  + https://help.aliyun.com/document_detail/55491.html


## Quick start
```python
# -*- coding: utf-8 -*-
import sys
import json
import uuid

from aliyunsdkdysmsapi.request.v20170525 import SendSmsRequest
from aliyunsdkdysmsapi.request.v20170525 import QuerySendDetailsRequest
from aliyunsdkcore.client import AcsClient
from aliyunsdkcore.profile import region_provider

"""
短信业务调用接口示例，版本号：v20170525

"""

reload(sys)
sys.setdefaultencoding('utf8')

# 注意：不要更改
REGION = "cn-hangzhou"
PRODUCT_NAME = "Dysmsapi"
DOMAIN = "dysmsapi.aliyuncs.com"

# ACCESS_KEY_ID/ACCESS_KEY_SECRET 根据实际申请的账号信息进行替换
ACCESS_KEY_ID = "%(ak)s"
ACCESS_KEY_SECRET = "%(secret)s"

acs_client = AcsClient(ACCESS_KEY_ID, ACCESS_KEY_SECRET, REGION)
region_provider.add_endpoint(PRODUCT_NAME, REGION, DOMAIN)


def send_sms(business_id, phone_numbers, sign_name, template_code, template_param=None):
    """发送短信验证码.

    Args:
        business_id(str): 流水号
        phone_numbers(str): 电话号码， 暂不支持国际化，e.g.: 18611111111
        sign_name(str): 签名,
        template_code(str): 模板ID
        template_param(str): json str， e.g: {"code": "123456"}，
          code 模板变量.

    Returns(str):
      - json str. e.g.: '{"Message":"\xe8\xa7\xa6\xe5\x8f\x91\xe5\x88\x86\xe9\x92\x9f\xe7\xba\xa7\xe6\xb5\x81\xe6\x8e\xa7Permits:1",
                          "RequestId":"11F58C05-6F60-447B-99F8-8BED591EB8B5",
                          "Code":"isv.BUSINESS_LIMIT_CONTROL"}'

    """
    smsRequest = SendSmsRequest.SendSmsRequest()
    # 申请的短信模板编码,必填
    smsRequest.set_TemplateCode(template_code)

    # 短信模板变量参数
    if template_param is not None:
        smsRequest.set_TemplateParam(template_param)

    # 设置业务请求流水号，必填。
    smsRequest.set_OutId(business_id)

    # 短信签名
    smsRequest.set_SignName(sign_name);

    # 短信发送的号码列表，必填。
    smsRequest.set_PhoneNumbers(phone_numbers)

    # 调用短信发送接口，返回json
    smsResponse = acs_client.do_action_with_exception(smsRequest)

    # TODO 业务处理

    return smsResponse


def query_send_detail(biz_id, phone_number, page_size, current_page, send_date):
    queryRequest = QuerySendDetailsRequest.QuerySendDetailsRequest()
    # 查询的手机号码
    queryRequest.set_PhoneNumber(phone_number)
    # 可选 - 流水号
    queryRequest.set_BizId(biz_id)
    # 必填 - 发送日期 支持30天内记录查询，格式yyyyMMdd
    queryRequest.set_SendDate(send_date)
    # 必填-当前页码从1开始计数
    queryRequest.set_CurrentPage(current_page)
    # 必填-页大小
    queryRequest.set_PageSize(page_size)

    # 调用短信记录查询接口，返回json
    queryResponse = acs_client.do_action_with_exception(queryRequest)

    # TODO 业务处理

    return queryResponse


if __name__ == '__main__':
    bid = uuid.uuid1()
    phone = '18612491225'
    sign = '服务'
    tpl_code = 'SMS_127005054'
    param = json.dumps({"code": "12345"})
    res = send_sms(bid, phone, sign, tpl_code, param)
    print(type(res), res, json.loads(res))

```
