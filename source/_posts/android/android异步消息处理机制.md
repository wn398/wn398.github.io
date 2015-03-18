title: android异步消息处理机制
date: 2015/3/14 15:57:26 
tags:
- 技术
- android

categories: android
---
##安卓异步消息机制
>Android不允许在子线程中进行UI操作。但Android提供了一套异步消息处理机制，解决了在子线程中进行UI操作的问题。

###异步消息处理：使用handler
如：

	public static final int UPDATE_TEXT = 1;
	//在onCreate()方法中注册Handler
	private Handler handler = new Handler() {
		public void handleMessage(Message msg) {
			switch (msg.what) {
				case UPDATE_TEXT:
				// 在这里可以进行UI操作
				text.setText("Nice to meet you");
			break;
			default:
			break;
			}
		}
	};

消息触发的地方

	Message message = new Message();
	message.what = UPDATE_TEXT;
	handler.sendMessage(message); // 将Message对象发送出去

###异步处理消息4部分：
Android中的异步消息处理主要由四个部分组成，`Message`、`Handler`、`MessageQueue`和`Looper`。其中Message和Handler在上一小节中我们已经接触过了，而MessageQueue和Looper对于你来说还是全新的概念，下面我就对这四个部分进行一下简要的介绍。

1. Message
	>Message是在线程之间传递的消息，它可以在内部携带少量的信息，用于在不同线程之间交换数据。上一小节中我们使用到了Message的what字段，除此之外还可以使用arg1和arg2字段来携带一些整型数据，使用obj字段携带一个Object对象。

2. Handler
	>Handler顾名思义也就是处理者的意思，它主要是用于发送和处理消息的。发送消息一般是使用Handler的sendMessage()方法，而发出的消息经过一系列地辗转处理后，最终会传递到Handler的handleMessage()方法中。

3. MessageQueue
	>MessageQueue是消息队列的意思，它主要用于存放所有通过Handler发送的消息。这部分消息会一直存在于消息队列中，等待被处理。每个线程中只会有一个MessageQueue对象。

4. Looper
	>Looper是每个线程中的MessageQueue的管家，调用Looper的loop()方法后，就会进入到一个无限循环当中，然后每当发现MessageQueue中存在一条消息，就会将它取出，并传递到Handler的handleMessage()方法中。每个线程中也只会有一个Looper对象。

了解了Message、Handler、MessageQueue以及Looper的基本概念后，我们再来对异步消息处理的整个流程梳理一遍。首先需要在主线程当中创建一个Handler对象，并重写handleMessage()方法。然后当子线程中需要进行UI操作时，就创建一个Message对象，并通过Handler将这条消息发送出去。之后这条消息会被添加到MessageQueue的队列中等待被处理，而Looper则会一直尝试从MessageQueue中取出待处理消息，最后分发回Handler的handleMessage()方法中。由于Handler是在主线程中创建的，所以此时handleMessage()方法中的代码也会在主线程中运行，于是我们在这里就可以安心地进行UI操作了


##可返回结果的多线程：
###使用AsyncTask:
由于AsyncTask是一个抽象类，所以如果我们想使用它，就必须要创建一个子类去继承它。在继承时我们可以为AsyncTask类指定三个泛型参数，这三个参数的用途如下。

1. Params  在执行AsyncTask时需要传入的参数，可用于在后台任务中使用。
2. Progress  后台任务执行时，如果需要在界面上显示当前的进度，则使用这里指定的泛型作为进度单位。
3. Result  当任务执行完毕后，如果需要对结果进行返回，则使用这里指定的泛型作为返回值类型。

因此，一个最简单的自定义AsyncTask就可以写成如下方式：

	class DownloadTask extends AsyncTask<Void, Integer, Boolean> {
	……
	}
>这里我们把AsyncTask的第一个泛型参数指定为Void，表示在执行AsyncTask的时候不需要传入参数给后台任务。第二个泛型参数指定为Integer，表示使用整型数据来作为进度显示单位。第三个泛型参数指定为Boolean，则表示使用布尔型数据来反馈执行结果。

###AsyncTask中的方法
>我们还需要去重写AsyncTask中的几个方法才能完成对任务的定制。经常需要去重写的方法有以下四个。

1. onPreExecute()
	>这个方法会在后台任务开始执行之前调用，用于进行一些界面上的初始化操作，比如显示一个进度条对话框等。
2. doInBackground(Params...)
	>这个方法中的所有代码都会在子线程中运行，我们应该在这里去处理所有的耗时任务。任务一旦完成就可以通过return语句来将任务的执行结果返回，如果AsyncTask的第三个泛型参数指定的是Void，就可以不返回任务执行结果。注意，在这个方法中是不可以进行UI操作的，如果需要更新UI元素，比如说反馈当前任务的执行进度，可以调用publishProgress(Progress...)方法来完成。
3. onProgressUpdate(Progress...)
	>当在后台任务中调用了publishProgress(Progress...)方法后，这个方法就会很快被调用，方法中携带的参数就是在后台任务中传递过来的。在这个方法中可以对UI进行操作，利用参数中的数值就可以对界面元素进行相应地更新。
4. onPostExecute(Result)
	>当后台任务执行完毕并通过return语句进行返回时，这个方法就很快会被调用。返回的数据会作为参数传递到此方法中，可以利用返回的数据来进行一些UI操作，比如说提醒任务执行的结果，以及关闭掉进度条对话框等。
       
简单来说，使用AsyncTask的诀窍就是，在doInBackground()方法中去执行具体的耗时任务，在onProgressUpdate()方法中进行UI操作，在onPostExecute()方法中执行一些任务的收尾工作。

如果想要启动这个任务，只需编写以下代码即可：
	
	new DownloadTask().execute();
