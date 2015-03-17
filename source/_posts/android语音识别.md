title: android语音识别
date: 2015/3/17 16:21:08 
tags:
- 技术
- android
- 语音识别
categories: android
---
>语音识别：Android主要通过`RecognizerIntent`来实现语音识别,
>
>它主要包括一些常量来表示语音的模式等

```{bash}
	ACTION_RECOGNIZE_SPEECH        开启语音活动
	ACTION_WEB_SEARCH              开启网络语音模式，结果将以网页搜索显示
	EXTRA_LANGUAGE　               可以理解为设置一个语言库
	EXTRA_LANGUAGE_MODEL　         语音识别模式
	EXTRA_MAX_RESULTS              返回的最大结果
	EXTRA_PROMPT                   提示用户可以开始语音了
	EXTRA_RESULTS                  将字符串返回到一个Arraylist中
	LANGUAGE_MODEL_FREE_FORM       在一种语言模式上自由语音
	LANGUAGE_MODEL_WEB_SEARCH      使用语音模型在Web上搜索
	RESULT_AUDIO_ERROR             返回结果时，音频遇到错误
	RESULT_NETWORK_ERROR           返回结果时，网络错误
	RESULT_CLIENT_ERROR            返回结果时客户端错误
	RESULT_NO_MATCH                没有检测到语音的错误
	RESULT_SERVER_ERROR            返回结果时，服务器错误
```

这里，我们只需要通过Intent来传递一个动作以及一些属性，然后通过`startActivityForResult`来开始语音：
```{java}
	Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
	intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL,RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
	intent.putExtra(RecognizerIntent.EXTRA_PROMPT,"开始语音");
	startActivityForResult(intent,VOICE_RECOGNITION_REQUEST_CODE);
```