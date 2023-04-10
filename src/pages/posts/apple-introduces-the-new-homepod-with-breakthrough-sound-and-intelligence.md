---
layout: '../../layouts/MarkdownPost.astro'
title: 'Cloudflare Workers+OpenAI+Github+Vercel+TencentCloud'
pubDate: 2023-04-02
description: 'none'
author: 'me'
cover:
    url: ''
    square: ''
    alt: 'cover'
tags: ["chatgpt", "cloudflare", "vercel"] 
theme: 'light'
featured: true

meta:
 - name: author
   content: 作者是我
 - name: keywords
   content: key3, key4

keywords: key1, key2, key3
---
#使用 Cloudflare Workers 解决 OpenAI 和 ChatGPT 的 API 无法访问的问题   https://github.com/noobnooc/noobnooc/discussions/9
##准备工作
	1、一个没有被 GFW 认证的域名：先注册了阿里云，结果实名认证失败，之后到腾讯云，同样的资料实名认证成功，7块钱买了一年的域名。
	2、一个 Cloudflare 账号：直接注册。
	3、注册或登录到 Cloudflare 的管理界面后，点击侧边栏的 “Websites” ，然后点击 “Add a Site” 按钮，根据 Cloudflare 的提示，在域名注册商（腾讯云）处将 NS 修改到 Cloudflare 指定的地址，等待域名解析成功后，即可进行后续操作。
##操作开始
	1、创建一个 Cloudflare Worker：登录到 Cloudflare 的管理界面后，点击侧边栏的 “Workers” 选项，然后点击 “Create a Service” 创建一个 Worker。
	2、然后在创建界面中输入 “Service name” 后点击 “Create Service” 按钮新建 Worker。“Select a starter” 项先不用管。
	3、修改 Cloudflare Worker 的代码：在 Worker 的管理界面，点击右上角的 “Quick Edit” 按钮编辑代码 Worker 的代码。
	
	const TELEGRAPH_URL = 'https://api.openai.com';
	addEventListener('fetch', event => {
	event.respondWith(handleRequest(event.request))
	})
	async function handleRequest(request) {
	const url = new URL(request.url);
	url.host = TELEGRAPH_URL.replace(/^https?:\/\//, '');
	const modifiedRequest = new Request(url.toString(), {
	headers: request.headers,
	method: request.method,
	body: request.body,
	redirect: 'follow'
	});
	const response = await fetch(modifiedRequest);
	const modifiedResponse = new Response(response.body, response);
	// 添加允许跨域访问的响应头
	modifiedResponse.headers.set('Access-Control-Allow-Origin', '*');
	return modifiedResponse;
	}
	
	4、最后点击编辑器右下角的 “Save and deploy” 按钮部署该代码，在弹出的对话框中继续选择“Save and deploy” 确认部署。
	5、绑定域名：在 Cloudflare Workers 的管理界面中，点击 “Triggers” 选项卡，然后点击 “Custom Domians” 中的 “Add Custom Domain” 按钮以绑定域名。
	至此便大功告成。等待片刻，应该就可以通过你自己的域名来代替 OpenAI 的 API 地址。
	————>至此，API地址已经不用翻墙访问。
#一键拥有你自己的 ChatGPT 网页服务。 One-Click to deploy your own ChatGPT web UI.
来自 < https://github.com/Yidadaa/ChatGPT-Next-Web>
##保持更新 Keep Updated
	• 删除掉原先的 repo；
	• fork 本项目；
	• 前往 vercel 控制台，删除掉原先的 project，然后新建 project，选择你刚刚 fork 出来的项目重新进行部署即可；
	• 在重新部署的过程中，请手动添加名为 OPENAI_API_KEY 的环境变量，并填入你的 api key 作为值。
##配置密码 Password
	本项目提供有限的权限控制功能，请在 Vercel 项目控制面板的环境变量页增加名为 CODE 的环境变量，值为用英文逗号分隔的自定义密码：
	code1,code2,code3
	增加或修改该环境变量后，请重新部署项目使改动生效。
##环境变量 Environment Variables
	OPENAI_API_KEY (required)
	OpanAI 密钥。
	Your openai api key.
	CODE (optional)
	访问密码，可选，可以使用逗号隔开多个密码。
	Access passsword, separated by comma.
	BASE_URL (optional)
	Default: api.openai.com
	OpenAI 接口代理 URL。
	Override openai api request base url.
	PROTOCOL (optional)
	Default: https
	Values: http | https
	OpenAI 接口协议。
	Override openai api request protocol.
	来自 < https://github.com/Yidadaa/ChatGPT-Next-Web>
	自定义环境变量 BASE_URL，值为Cloudflare Workers 的url
#https://vercel.com/dashboard 中的操作：
	Overview中，点进ChatGPT-Next-Web，依次点击Setting -> Domains，输入框中输入你自己的域名，点击Add，按照提示在Cloudflare 中修改A、CNAME，等待片刻即可完成。其中 CNAME 改为cname-china.vercel-dns.com
	————>至此，所有域名、API访问均不需要翻墙了。