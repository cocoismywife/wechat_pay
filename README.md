# WechatPay

Wechat Pay: https://open.weixin.qq.com/cgi-bin/frame?t=home/pay_tmpl&lang=zh_CN

It contains:

* generate access_token
* generate payment params for App
* verify notify

## Installation

Add this line to your application's Gemfile:

    gem 'wechat_pay', '~> 0.1.0'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install wechat_pay

## Usage

### Config

```ruby
WechatPay.app_id       = 'YOUR_APP_ID'
WechatPay.app_secret   = 'YOUR_APP_SECRET'
WechatPay.pay_sign_key = 'YOUR_PAY_SIGN_KEY'
WechatPay.partner_id   = 'YOUR_PARTNER_ID'
WechatPay.partner_key  = 'YOUR_PARTNER_KEY'
```

### Access Token

```ruby
WechatPay::AccessToken.generate # => { access_token: 'ACCESS_TOKEN', expires_in: 7200 }
```

Your should cache the `access_token`, see [http://mp.weixin.qq.com/wiki...](http://mp.weixin.qq.com/wiki/index.php?title=%E8%8E%B7%E5%8F%96access_token)

You may wanna do something like this in Rails:

```ruby
Rails.cache.fetch(:wechat_pay_access_token, expires_in: 7200.seconds raw: true) do
  WechatPay::AccessToken.generate[:access_token]
end
```

### Payment params

```ruby
# Please keep in mind that all key MUST be Symbol
params = {
  body:             'body',
  traceid:          'traceid',      # Your user id
  out_trade_no:     'out_trade_no', # Your order id
  total_fee:        '100',          # 注意：单位是分，不是元
  notify_url:       'http://your_domain.com/notify',
  spbill_create_ip: '192.168.1.1'
}

WechatPay::App.payment('ACCESS_TOKEN', params)
# =>
#   {
#     nonce_str:  'noncestr',
#     package:    'Sign=WXpay',
#     partner_id: 'partner_id',
#     prepay_id:  'prepay_id',
#     timestamp:  '1407165191',
#     sign:       'sign'
#   }
```
### Verify notify

```ruby
# for rails, you may want to except :controller_name, :action_name, :host, etc.
# notify_params = params.except(*request.path_parameters.keys)
WechatPay::Notify.verify?(notify_params)
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
