<!-- TITLE: Generation OfferSDP in broadcasting -->
<!-- SUBTITLE: It is necessary to do addTrack before generating OfferSDP. -->

# 単方向配信でのSDPの交換順序

- 映像を配信する側からOfferを投げる必要がある
	- **OfferSDP生成時点でpeerに対しての`addTrack`が完了していなければならない**
	- `addTrack`せずに生成したOfferSDPは殆ど何も入っていない

```
v=0
o=- 7090669051765485067 2 IN IP4 127.0.0.1
s=-
t=0 0
a=msid-semantic: WMS
```

- 殆ど空のOfferを受け取った対向PeerはICEによるネゴシエーションを行うかどうか判断できない
- 映像/音声コーデックに関する情報も無いため配信の合意も取れない
- 上記のSDPからは何も分からないので、Answer側からも同じようなSDPが返送される

```
v=0
o=- 2689592852498203735 2 IN IP4 127.0.0.1
s=-
t=0 0
a=msid-semantic: WMS
```

- この場合ICE Candidateの生成が行われないため`onicecandidate`は発火しない
- 視聴側からOfferを投げた場合に映像が流れてこないのはこのため(addTrackを一切しないので)
- 視聴側からはOfferの送信をリクエストし配信側からOfferを投げてもらえば正常に通信できる
- DataChannelを使う場合も、Offer生成前にcreateDataChannelしておく必要がある