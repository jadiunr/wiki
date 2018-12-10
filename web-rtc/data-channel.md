<!-- TITLE: Data Channel -->
<!-- SUBTITLE: Peer to Peer Messaging -->

# 失敗例

```
const dataChannel = peer.createDataChannel("label");
dataChannel.onopen = (ev) => {
  console.log(ev);
	dataChannel.send("Hello");
};
dataChannel.onmessage = (ev) => console.log(ev.data);
dataChannel.onerror = (ev) => console.log(ev);
dataChannel.onclose = (ev) => console.log(ev);
```

- これだけ書けば動くだろうと思ってたら動かんかった
- `onmessage`生えてるからメッセージ返ってくると信じて疑わなかったよね

## createDataChannel & ondatachannel

- Offer側で`RTCPeerConnection.createDataChannel`を実行するとPeerに対するDataChannelを作成できる
- Offer側でcreateDataChannelを実行するとAnswer側では`RTCPeerConnection.ondatachannel`が発火する

- Offer側では作成したdataChannelに生えている`onopen`, `onmessage`, `onerror`, `onclose`等でイベントの処理をする
- Answer側ではcreateDataChannelは実行しない
	- ondatachannelが発火するためそこで処理をする

```
peer.ondatachannel = (ev) => {
  const dataChannel = ev.channel;
  dataChannel.onopen = (ev) => {
	  console.log(ev);
		dataChannel.send("Hola.");
	};
  dataChannel.onmessage = (ev) => console.log(ev.data);
  dataChannel.onerror = (ev) => console.log(ev);
  dataChannel.onclose = (ev) => console.log(ev);
};
```

- 両方のPeerでcreateDataChannelだけを行った場合、作成したDataChannelをどちらもondatachannelで拾わないのでメッセージが飛ばない
- Offer側でDataChannelを作成、Answer側でondatachannelを待ち受けることでメッセージの交換が可能になる