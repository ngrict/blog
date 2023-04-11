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
I. Using Cloudflare Workers to address the issue of inaccessibility of OpenAI and ChatGPT APIs

Prior Preparations:
To proceed with the solution, one must possess an unauthenticated domain name, for instance, by registering for one on Ali Cloud, which may require a real-name authentication process. An alternative option is to use Tencent Cloud to authenticate oneself, in which case the process may cost just 7 RMB per year. Additionally, one will need to create an account with Cloudflare.

Deployment:
To begin with the deployment, one must visit the Cloudflare management interface and select the "Websites" option on the side panel. By clicking on the "Add a Site" button, one can follow the prompts provided by Cloudflare to modify the NS address on their domain name registrar (e.g. Tencent Cloud), and wait until domain name resolution is successful to proceed with the remaining tasks.
 
Next, create a Cloudflare worker by selecting the "Workers" option on the Cloudflare management interface and clicking on "Create a Service" to create a new worker. It is not necessary to select a starter at this point. Then, edit the code for the Cloudflare worker to include the following:


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


Lastly, click on the "Save and deploy" button in the lower right corner to deploy the code. A confirmation dialog will pop up, where you should press "Save and deploy" to confirm your deployment. Bind your domain name by navigating to "Triggers" on Cloudflare Workers' management interface, selecting the "Custom Domians" tab, and clicking on the "Add Custom Domain" button to bind the domain name. Wait for a while, and you should be able to use your own domain name instead of the OpenAI API address.

This way, one can access the API address without requiring any VPN to connect to it.

II. One-click ChatGPT deployment for your own web UI

To keep one's ChatGPT UI updated:

1. Firstly, delete the original repo.
2. Then fork this project.
3. Proceed to the Vercel console, delete the original project, and create a new one by selecting the forked project for deployment.
4. During the deployment process, manually add an environment variable named "OPENAI_API_KEY" and input one's own API key provided by OpenAI for the project to utilize.

Password Configuration:
This project comes with limited access control functionality, so you should add an environment variable named "CODE" with a custom password separated by English commas on the environment variable page of the Vercel project control panel as a way of password protection. After adding or modifying the environment variable, you should redeploy the project for the changes to take effect.

Environment Variables:
1. OPENAI_API_KEY (required): OpenAI key.
2. CODE (optional): Access password, separated by commas.
3. BASE_URL (optional, default is api.openai.com): OpenAI interface proxy URL.
4. PROTOCOL (optional, default is https, alternative values are http and https): OpenAI interface protocol.

Custom Environment Variables:
Create a custom environment variable called BASE_URL with a URL provided by Cloudflare Workers.

III. Working with https://vercel.com/dashboard

After entering the ChatGPT-Next-Web application under "Overview," proceed to "Setting -> Domains," input your domain name into the input field and click on "Add." Follow the prompts to modify A and CNAME in Cloudflare, and wait for a while for the changes to take effect. The CNAME should be changed to cname-china.vercel-dns.com as illustrated.

As a result, one can access all domains and APIs without requiring a VPN.


I. 使用 Cloudflare Workers 解决 OpenAI 和 ChatGPT API 不可访问的问题

前期准备：
为了进行这项解决方案，你必须拥有一个未经认证的域名，例如在阿里云上注册一个，这可能需要进行真实姓名认证过程。另一个选择是使用腾讯云进行认证，此时该过程可能仅需每年 7 元人民币。此外，你还需要在 Cloudflare 上创建一个帐户。

部署：
要开始部署，你需要访问 Cloudflare 管理界面，在侧边栏中选择“网站”选项。通过单击“添加站点”按钮，你可以按照 Cloudflare 提供的提示修改其域名注册器（例如腾讯云）上的 NS 地址，然后等待域名解析成功，以继续进行剩余的任务。
 
接下来，在 Cloudflare 管理界面上选择“Workers”选项，单击“创建服务”以创建一个新的 worker，此时无需选择 starter。然后，编辑 Cloudflare worker 的代码以包括以下内容：


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


最后，单击右下角的“保存和部署”按钮来部署代码。一个确认对话框将弹出，你应该按“保存和部署”以确认你的部署。通过导航到 Cloudflare Workers 的管理界面上的“触发器”，选择“自定义域名”选项卡，然后单击“添加自定义域名”按钮来绑定域名。等待片刻，你就可以使用自己的域名而不需要 VPN 来连接 OpenAI API。

这样，你就可以访问 API 地址而不需要 VPN 进行连接。

II. 为你自己的 Web UI 部署 ChatGPT 的一键部署

为了保持 ChatGPT UI 的最新状态：

1. 首先，删除原始的 repo。
2. 然后 fork 这个项目。
3. 进入 Vercel 控制台，删除原始项目，创建一个新项目，选择 fork 的项目进行部署。
4. 在部署过程中，手动添加名为“OPENAI_API_KEY”的环境变量，输入 OpenAI 为该项目提供的自己的 API 密钥。

密码配置：
该项目带有有限的访问控制功能，因此，你应该在 Vercel 项目控制面板的环境变量页面上添加名为“CODE”的环境变量，其中包含一串用英文逗号分隔的自定义密码作为密码保护方式。在添加或修改环境变量后，你应该重新部署项目以使更改生效。

环境变量：
1. OPENAI_API_KEY（必需）：OpenAI 密钥。
2. CODE（可选）：访问密码，用逗号分隔。
3. BASE_URL（可选，默认为 api.openai.com）：OpenAI 接口代理 URL。
4. PROTOCOL（可选，默认为 https，可选值为 http 和 https）：OpenAI 接口协议。

自定义环境变量：
创建名为 BASE_URL 的自定义环境变量，其中包含 Cloudflare Workers 提供的 URL。

III. 使用 https://vercel.com/dashboard

在“概览”下进入 ChatGPT-Next-Web 应用程序后，进入“设置->域名”，将你的域名输入到输入字段中，然后单击“添加”。按照提示修改 Cloudflare 中的 A 和 CNAME，等待一段时间以使更改生效。CNAME 应该被改成 cname-china.vercel-dns.com，如图所示。

因此，你可以访问所有域名和 API，而不需要 VPN。