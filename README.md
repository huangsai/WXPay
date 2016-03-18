# WXPay
####1.服务器首先联网获取ACCESS_TOKEN，然后根据获取到的access_token，生成预支付订单；
####2.客户端会去访问服务器获取预支付订单，主要是 prepayId、nonceStr 这两个参数；
####3.客户端获取到以上两个参数后；
	 //调起微信支付
  	private void sendPayReq(String prepayId, String nonceStr) {
		
		PayReq req = new PayReq();
		req.appId = Constants.WX_APPID;
		req.partnerId = Constants.WX_PARTNER_ID;
		req.prepayId = prepayId;
		req.nonceStr = nonceStr;
		req.timeStamp = String.valueOf(SystemClock.currentThreadTimeMillis());
		req.packageValue = "Sign=WXPay";
		
		List<NameValuePair> signParams = new LinkedList<NameValuePair>();
		signParams.add(new BasicNameValuePair("appid", req.appId));
		signParams.add(new BasicNameValuePair("noncestr", req.nonceStr));
		signParams.add(new BasicNameValuePair("package", req.packageValue));
		signParams.add(new BasicNameValuePair("partnerid", req.partnerId));
		signParams.add(new BasicNameValuePair("prepayid", req.prepayId));
		signParams.add(new BasicNameValuePair("timestamp", req.timeStamp));
		req.sign = genAppSign(signParams);
		
		// 在支付之前，如果应用没有注册到微信，应该先调用IWXMsg.registerApp将应用注册到微信
		api.sendReq(req);
	}
	//生成签名方法
	private String genAppSign(List<NameValuePair> params) {
		StringBuilder sb = new StringBuilder();
		
		for (int i = 0; i < params.size(); i++) {
			sb.append(params.get(i).getName());
			sb.append('=');
			sb.append(params.get(i).getValue());
			sb.append('&');
		}
		sb.append("key=");
		sb.append(Constants.WX_APP_KEY);
		String sign = MD5.getMessageDigest(sb.toString().getBytes()).toUpperCase();
		return sign;
	}
