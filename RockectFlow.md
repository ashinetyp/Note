# RocketChat

<table>
	<thead>
		<tr>
			<th>步驟<br></th>
			<th>Kotlin<br></th>
			<th>ReactNative</th>
			<th>Network</th>
		</tr>
	</thead>
	<tbody>
	<tr>
		<td>初始化&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;</td>
				<td>MainActivity<br>以singleton初始化MainPresenter<br>MainActivity<br>&nbsp; &nbsp; onCreate<br>&nbsp; &nbsp; &nbsp; &nbsp; with(presenter)
{
<br>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; connect()<br>&nbsp; &nbsp; &nbsp; &nbsp; }<br>確認db有無連線過的資訊&nbsp;</td>
				<td>index.js<br>在root做初始化<br><br>index.js-init.js<br>yield
takeLatest(APP.INIT, restore);<br>確認UserPreferences有無連線過的資訊</td>
				<td>Kotlin<br>socket接起來會開⼀個while
loop<br>在sdk client的onMessage
<br>不停讀取送來的東⻄<br>收到訊息就塞db<br>並且往下送,透過kotlinx.coroutines.Channel配發<br>使⽤okhttp3.internal.
ws.RealWebSocket
<br>(新版應該是要用Asteroid)<br><br>ReactNative是Asteroid
DDP ?!connect</td>
			</tr>
			<tr>
				<td>初始化-有連線過的server&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;</td>
				<td>如果有之前連過的,就連socket
<br>MainPresenterconnect<br>1.connectionManagerFactory.create(it)?.connect()<br>2.client.connect()<br>(client是SDK
instance)<br><br>當狀態變成連線成功就訂閱
client.subscribeSubscriptions<br>client.subscribeRooms<br>client.subscribeActiveUsers</td>
				<td>如果之前有連過,
就連socket<br>selectServer.js
<br>1.handleSelectServer
2.sdk.current.co nect()<br>(sdk是SDK
instance)
<br><br>當狀態變成連線成功就訂閱sdk.current.onStreamData
<br>'users',<br>'stream-notifyall'<br>'stream-roles'<br>etc…</td>
				<td>&nbsp;</td>
			</tr>
			<tr>
				<td>沒有連過的
server,由user輸入server path</td>
				<td>ServerFragment<br>使⽤者按下連線<br><br>presenter.checkServer -&gt;<br>presenter.connectconnectToServer
<br><br>1.checkServerInfo<br><br>2.client?.serverInfo()?
<br>打API 確認是否可連線<br><br>如果OK
<br>3.versionCheck -&gt;<br>View?.versionOk()<br>就會嘗試開啟登入/對話畫⾯<br><br>4.presenter.connect<br>問db登入狀態<br><br>5-1.有舊帳號就開對話清單<br>navigator.toChatList(serverUrl)<br>5-2.沒有就跳轉登入畫⾯<br>navigator.toLogin</td>
				<td>NewServerView<br>使⽤者按下連線
<br><serverinput submit}=""><br>dispatch(serverRequest(server)
);<br><br>selectServer.js<br>1.handleServerRequest<br><br>2.const
serverInfo =
yield
getServerInfo({
server });<br><br>3.有serverInfo就會嘗試開啟登入/對話畫⾯<br><br>4.handleSelectServer<br>問
UserPreferences登入狀態
<br><br>5-1.跳轉登入畫
⾯(順序相反)<br>Navigation.navigate('Workspace
View');
<br>或是iframe不能⽤時的登入畫⾯Navigation.navigate('LoginView',
{ username });<br>5-2發現有帳號就開啟對話清單
yield
put(appStart({ root: RootEnum.ROOT_INSIDE }));</serverinput></td>
				<td>拿Server狀態
<br><br>RocketChatClient.serverInfo()
<br><br>GetPath放baseUrl/api/info
<br><br>val url =
restUrl.newBuilder()<br>&nbsp; &nbsp; .addPathSe
gment("api")<br>&nbsp; &nbsp; .addPathSe
gment("info")<br>&nbsp; &nbsp; .build()</td>
			</tr>
			<tr>
				<td>有登入</td>
				<td>ServerFragment<br>問帳號<br>val account =<br>getAccountsInteractor.get().firstOrNull
{<br>&nbsp; &nbsp; it.serverUrl ==
serverUrl<br>}<br><br>有帳號就開對話<br>navigator.toChatList(serverUrl)</td>
				<td>selectServer.js<br>問帳號<br>const userId =<br>UserPreference
s.getString(`$
{TOKEN_KEY}-
${server}`);<br><br>有帳號就開對話
並且socket連線
<br>yield
connect({ server
, logoutOnError:
true });<br><br>⽤Asteroid做websocket的連線yield
put(appStart({ root: RootEnum.ROOT_INSIDE }));</td>
				<td>&nbsp;</td>
			</tr>
			<tr>
				<td>沒登入</td>
				<td>ServerFragment<br>開登入畫⾯<br>navigator.toLogin(serverUrl)<br>顯⽰LoginFragment<br><br>按下登入按鈕<br>presenter.authenticateWithUserAndPassword<br>打API做登入,有三種<br>client.loginWithLdap(usernameOrEmail,
client.loginWithEmail(usernameOrEmail, password)<br>client.login(usernameOrEmail,
password)<br><br>登入之後存拿到的token<br>tokenRepository.save(url, result)<br><br>之後拿user資料<br>client.me()<br>之後存db<br></td>
				<td>selectServer.js<br>開登入畫⾯<br>Navigation.navigate('LoginView',
{ username });<br>顯⽰
LoginView.tsx<br><br>按下登入按鈕<br>dispatch(loginRequest({ user,
password }));<br>接到login.jshandleLoginReq
uest<br>打API做登入<br>result = yield
call(loginWithPa
sswordCall,
credentials);
<br>-loginTOTP(params, true);<br>-sdk.current.login(credentials);
<br><br>SDK是import
{ Rocketchat }
from
'@rocket.chat/
sdk';
<br><br>成功就打<br>yield
put(loginSuccess(result));
到
handleLoginSuccess<br>拿⼀堆資料喔<br>yield
fork(fetchRoomsFork);
yield
fork(fetchPermissionsFork);<br>yield
fork(fetchCustomEmojisFork);<br>yield
fork(fetchRolesFork);<br>yield
fork(fetchSlashCommandsFork)
;<br>yield
fork(registerPushTokenFork);<br>yield
fork(fetchUsersPresenceFork);
<br>yield
fork(fetchEnterpriseModulesFork, { user });<br>fork(subscribeS
ettingsFork);
<br><br>yield
put(encryptionInit());<br><br>之後切換畫⾯<br>yield
put(appStart({ root: RootEnum.ROOT_INSIDE }));<br></td>
				<td>⽤帳密做登入<br>by okHttpClient
<br><br>RocketChatClient.loginWithEmail
<br><br>baseUrl.newBuilder()<br>&nbsp; &nbsp; .addPathSe
gment("api")<br>&nbsp; &nbsp; .addPathSe
gment("v1")<br>&nbsp; &nbsp; .addPathSe
gment(method)
<br>會組出PostPath<br>baseUrl/api/v1/login
body<br><br>帶入帳密<br>打post拿回token拿user資料,<br>val httpUrl =
requestUrl(rest<br>Url,
"me").build()<br>val request =
requestBuilderF
orAuthenticatedMethods(httpUrl
).get().build()<br><br>會組出
baseUrl/me<br><br>builder.addHeader("X-AuthToken",
it.authToken).<br>&nbsp; &nbsp; addHeader("XUser-Id", it.userId)<br><br>ReactNative版也是body帶入帳密打<br>post拿回token
<br><br>authHeaders['XAuth-Token']
=== '' ||
authHeaders['XUser-Id'] === ''<br><br><br>User應該會有這些資料<br><br>const u = {<br>token:
user.token,<br>username:
user.username,<br>name:
user.name,<br>language:
user.language,<br>status:
user.status,<br>statusText:&nbsp;&nbsp;user.statusText,<br>roles:
user.roles,
isFromWebView: user.isFromWebView,
<br>showMessageInMainThread: user.showMessageInMainThread,
<br>avatarETag:
user.avatarETag<br><br>};<br></td>
			</tr>
			<tr>
				<td>切換畫⾯到 ChatList</td>
				<td>LoginFragment<br><span style="font-style: normal; font-weight: 400;">登入完成</span><br><span style="font-style: normal; font-weight: 400;">navigator.toChatList(serverUrl)</span><br><br><span style="font-style: normal; font-weight: 400;">開啟ChangeServerActivity</span><br><span style="font-style: normal; font-weight: 400;">activity.startActivity(activity.changeServerIntent (serverUrl))</span><br><br><span style="font-style: normal; font-weight: 400;">presenter.loadServer(serverUrl, chatRoomId)</span><br><br><span style="font-style: normal; font-weight: 400;">開chatRooms列表</span><br><span style="font-style: normal; font-weight: 400;">navigator.toChatRooms(chatRoomId)</span></td>
				<td>LoginView.tsx<br><span style="font-style: normal; font-weight: 400;">登入完成</span><br><span style="font-style: normal; font-weight: 400;">yield put(appStart({ root: RootEnum.ROOT_INSIDE }));</span><br><span style="font-style: normal; font-weight: 400;">顯⽰InsideStack UI</span></td>
				<td>&nbsp;</td>
			</tr>
			<tr>
				<td><span style="font-style: normal; font-weight: 400;">切換畫⾯到 ChatRooms</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">navigator.toChatRooms(chatRoomId)</span><br><span style="font-style: normal; font-weight: 400;">導向MainActivity</span><br><span style="font-style: normal; font-weight: 400;">掛上ChatRoomsFragment</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">RootEnum.ROOT_INSIDE</span><br><span style="font-style: normal; font-weight: 400;">UI是開 InsideStack</span><br></td>
				<td>&nbsp;</td>
			</tr>
			<tr>
				<td><span style="font-style: normal; font-weight: 400;">列出Chat Room List</span></td>
				<td><span style="font-style: normal; font-weight: 400;">ChatRoomsFragment</span><br><span style="font-style: normal; font-weight: 400;">subscribeUi()裡⾯拿取chatRoomlist</span><br><span style="font-style: normal; font-weight: 400;">1.初始化Adapter</span><br><br><span style="font-style: normal; font-weight: 400;">2.監聽db-&gt;</span><span style="font-style: normal; font-weight: 400;">有房間list資料改變就更新畫⾯</span><br><span style="font-style: normal; font-weight: 400;">viewModel.getChatRooms().observe</span><br><br><span style="font-style: normal; font-weight: 400;">3.監聽連線狀態-&gt;有連線狀態改變就抓roomlist</span><br><span style="font-style: normal; font-weight: 400;">viewModel.getStatus().observe</span><br><br><span style="font-style: normal; font-weight: 400;">4.拿rooms list並更新db的room清單</span><br><span style="font-style: normal; font-weight: 400;">fetchRooms()-&gt;interactor.refreshChatRooms()</span></td>
				<td><span style="font-style: normal; font-weight: 400;">RoomsListView</span><br><span style="font-style: normal; font-weight: 400;">getSubscriptions裡⾯拿取 rooms list</span><br><br><span style="font-style: normal; font-weight: 400;">1.監聽db有rooms list資料改變就更新畫⾯</span><br><span style="font-style: normal; font-weight: 400;">this.querySubscription = observable.subscribe</span><br><br><span style="font-style: normal; font-weight: 400;">2.下拉以更新,拿rooms list並且更新db</span><br><span style="font-style: normal; font-weight: 400;">onRefresh -&gt; dispatch(roomsRequest({ allData: true })) -&gt;</span><br><span style="font-style: normal; font-weight: 400;">handleRoomsRequestgetRooms&nbsp;</span></td>
				<td>拿rooms跟subscriptions⽤ okHttpClient<br><br><span style="font-style: normal; font-weight: 400;">RestMultiResult.create(update, remove)</span><br><br><span style="font-style: normal; font-weight: 400;">val urlBuilder = requestUrl(rest Url, "rooms.get") 或"subscriptions.get"</span><br><br><span style="font-style: normal; font-weight: 400;">Path: baseUrl+API+v1 +rooms.get</span><br><br><span style="font-style: normal; font-weight: 400;">有帶header</span><br><span style="font-style: normal; font-weight: 400;">builder</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("X-AuthToken", it.authToken)</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("XUser-Id", it.userId)</span></td>
			</tr>
			<tr>
				<td><span style="font-style: normal; font-weight: 400;">切換畫⾯到 Room內</span></td>
				<td><span style="font-style: normal; font-weight: 400;">ChatRoomFragment</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; RoomsAdapter</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Click listener</span><br><span style="font-style: normal; font-weight: 400;">presenter.loadChatRoom(room)</span><br><br><span style="font-style: normal; font-weight: 400;">1.重新load⼀次 room list</span><br><span style="font-style: normal; font-weight: 400;">FetchChatRoomsInteractor(client, dbManager).refreshChatRooms( )</span><br><br><span style="font-style: normal; font-weight: 400;">2.跳轉navigator.toChatRoom</span><br><br><span style="font-style: normal; font-weight: 400;">3.進入ChatRoomActivity</span><br><span style="font-style: normal; font-weight: 400;">onCreate時</span><span style="font-style: normal; font-weight: 400;">進行socketConnection</span><br><span style="font-style: normal; font-weight: 400;">ConnectionmanagerFactory. create(serverUrl )?.connect()</span><br><br><span style="font-style: normal; font-weight: 400;">4.掛上ChatRoomFragment</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">RoomsListView</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; onPressItem</span><br><br><span style="font-style: normal; font-weight: 400;">goRoom({ item, isMasterDetail })&nbsp; &nbsp; &nbsp;Navigation to ‘RoomView’</span><br></td>
				<td><br></td>
			</tr>
			<tr>
				<td><span style="font-style: normal; font-weight: 400;">列出Room內的對話</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">ChatRoomFragment</span><br><span style="font-style: normal; font-weight: 400;">ChatRoomAdapter顯⽰對話內容</span><br><br><span style="font-style: normal; font-weight: 400;">1.從db讀取訊息</span><br><span style="font-style: normal; font-weight: 400;">presenter.setup</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; ChatRoomloadMessages</span><br><br><span style="font-style: normal; font-weight: 400;">2.塞入adapter 並且notifyChange view.showMessages</span><br><br><span style="font-style: normal; font-weight: 400;">3.打API撈新的訊息</span><br><span style="font-style: normal; font-weight: 400;">loadAndShowMessages</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; client.messages(SDK)</span><br><br><span style="font-style: normal; font-weight: 400;">4.塞入adapter並且notifyChange</span><br><span style="font-style: normal; font-weight: 400;">view.showMessages</span><br><br><span style="font-style: normal; font-weight: 400;">3-1.讀取參加者</span><br><span style="font-style: normal; font-weight: 400;">loadActiveMembers(roomId, chatRoomType, filterSelfOut = true)</span><br><span style="font-style: normal; font-weight: 400;">打API撈參加者</span><br><span style="font-style: normal; font-weight: 400;">client.getMembers</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">RoomView</span><br><span style="font-style: normal; font-weight: 400;">進入後註冊callback</span><br><span style="font-style: normal; font-weight: 400;">observable this.observeRoom(room);</span><br><br><span style="font-style: normal; font-weight: 400;">1.從db讀取訊息 this.list.current? .query()</span><br><br><span style="font-style: normal; font-weight: 400;">2.塞入this.list並且更新畫⾯ render() -&gt;</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; renderItem()</span><br><br><span style="font-style: normal; font-weight: 400;">3.打API撈新的訊息</span><br><span style="font-style: normal; font-weight: 400;">this.init() -&gt;</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; loadThreadMes sages</span><br><br><span style="font-style: normal; font-weight: 400;">4.塞入db更新this.list並且更新畫⾯</span><br><span style="font-style: normal; font-weight: 400;">db.get('thread_messages'); db.get('subscriptions')</span><br><br><span style="font-style: normal; font-weight: 400;">3.1讀取參加者</span><br><span style="font-style: normal; font-weight: 400;">constmember = await this.getRoomMember();&nbsp;</span><br></td>
				<td>&nbsp;拿對話打<span style="font-style: normal; font-weight: 400;">Get</span><br><span style="font-style: normal; font-weight: 400;">RocketChatClient.messages</span><br><br><span style="font-style: normal; font-weight: 400;">SDK</span><br><span style="font-style: normal; font-weight: 400;">val httpUrl = requestUrl( restUrl, getRestApiMethodNameByRoom</span><span style="font-style: normal; font-weight: 400;">Type(roomType, "messages”))</span><br><br><span style="font-style: normal; font-weight: 400;">Room type有三種</span><br><span style="font-style: normal; font-weight: 400;">channel/group/im</span><br><br><span style="font-style: normal; font-weight: 400;">Path: baseUrl+messages</span><br><br>有帶header<br><span style="font-style: normal; font-weight: 400;">builder</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("X-AuthToken", it.authToken)</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("XUser-Id", it.userId)</span><br><br><span style="font-style: normal; font-weight: 400;">ReactNative是打</span><br><span style="font-style: normal; font-weight: 400;">sdk.get(`${roomTypeToApiType(t)}.messages`, { roomId, query, offset, sort: { ts: -1 } });</span><br><br><span style="font-style: normal; font-weight: 400;">或是有threadId 的時候</span><br><span style="font-style: normal; font-weight: 400;">‘getThreadMessages’ 帶入tmid (threadId)</span><br><br><span style="font-style: normal; font-weight: 400;">type有三種</span><br><span style="font-style: normal; font-weight: 400;">SubscriptionType.DIRECT | SubscriptionTyp e.CHANNEL | SubscriptionTyp e.GROUP;</span><br><br><span style="font-style: normal; font-weight: 400;">拿參加者</span><br><span style="font-style: normal; font-weight: 400;">Android(找沒)</span><br><span style="font-style: normal; font-weight: 400;">ReactNative是</span><br><span style="font-style: normal; font-weight: 400;">sdk.get('users.info', { userId })</span></td>
			</tr>
			<tr>
				<td><span style="font-style: normal; font-weight: 400;">進房間後的socket⾏為</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">ChatRoomFragment</span><br><span style="font-style: normal; font-weight: 400;">建立socket連線, 訂閱房間更新訊息</span><br><span style="font-style: normal; font-weight: 400;">presenter.setupChatRoom</span><br><br><span style="font-style: normal; font-weight: 400;">有新訊息進來就更新</span><br><span style="font-style: normal; font-weight: 400;">subscribeRoomMessages -&gt;</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; client.subscribeRoomMessages -&gt;</span><br><span style="font-style: normal; font-weight: 400;">&nbsp; &nbsp; &nbsp; &nbsp; updateMessage(message)</span><br><br><span style="font-style: normal; font-weight: 400;">subscribeTypingStatus</span><br><span style="font-style: normal; font-weight: 400;">subscribeConnectionState</span><br></td>
				<td>RoomView<br><span style="font-style: normal; font-weight: 400;">this.sub?.subscribe?.();</span><br><span style="font-style: normal; font-weight: 400;">訂閱</span><br><span style="font-style: normal; font-weight: 400;">this.promises = sdk.subscribeRoom(this.rid);</span><br><br><span style="font-style: normal; font-weight: 400;">this.connectedLi stener = sdk.onStreamData('connected',</span><span style="font-style: normal; font-weight: 400;">this.handleConnection);</span><br><br><span style="font-style: normal; font-weight: 400;">this.disconnectedListener = sdk.onStreamData('close',</span><span style="font-style: normal; font-weight: 400;">this.handleConnection);</span><br><br><span style="font-style: normal; font-weight: 400;">this.notifyRoomListener = sdk.onStreamData('streamnotify-room',</span><span style="font-style: normal; font-weight: 400;">this.handleNotifyRoomReceived );</span><br><br><span style="font-style: normal; font-weight: 400;">this.messageReceivedListener = sdk.onStreamData('streamroommessages',<br></span><span style="font-style: normal; font-weight: 400;">this.handleMessageReceived);</span><br></td>
				<td><span style="font-style: normal; font-weight: 400;">Kotlin socket</span><br><span style="font-style: normal; font-weight: 400;">送訊息send(streamRoomMessages(id, roomId))</span><br><br><span style="font-style: normal; font-weight: 400;">訊息組成</span><br><span style="font-style: normal; font-weight: 400;">newSubscriptionMessage("stream-roommessages", id, "\"$roomId\", false")</span><br><br><span style="font-style: normal; font-weight: 400;">訂閱之後收到的訊息會透過下⾯配發 Socket.processSubscriptionsChanged</span><br><br><span style="font-style: normal; font-weight: 400;">處理房間內的對話訊息</span><br><span style="font-style: normal; font-weight: 400;">processRoomMessage</span><br><span style="font-style: normal; font-weight: 400;">再送去給sdk</span><br><span style="font-style: normal; font-weight: 400;">messagesChannel.send(message)</span><br><br><span style="font-style: normal; font-weight: 400;">ReactNative ‘stream-roommessages’</span><br></td>
			</tr>
			<tr>
				<td>傳訊<br></td>
				<td>ChatRoomFragment<br>sendMessage<br><br>1.打API送訊息<br>client.sendMessage(SDK)<br><br>2.存db<br>messagesRepository.save(newMessage.copy(synced = true))<br></td>
				<td>RoomView<br>handleSendMessage<br><br>1.存db<br>await
db.write(async
() =&gt; {
await
db.batch(...batch);
});<br><br>2.打API送訊息<br>sdk.post('chat.sendMessage',
{ message });<br></td>
				<td>Kotlin<br>送訊息打Post<br>RocketChatClient.sendMessage<br>SDK<br>val url =
requestUrl(restUrl,"chat.sendMessage").build()<br>Post json
message body<br><br>Path: baseUrl+"chat.sendMessage"<br><br>有帶header<br><span style="font-style: normal; font-weight: 400;">builder</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("X-AuthToken", it.authToken)</span><br><span style="font-style: normal; font-weight: 400;">.addHeader("XUser-Id", it.userId)</span><br><br>ReactNative
‘chat.sendMessage’<br></td>
			</tr>
		</tbody>
	</table>
