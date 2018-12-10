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

### 通常のSDP

```
v=0
o=- 3664330853066981054 2 IN IP4 127.0.0.1
s=-
t=0 0
a=group:BUNDLE audio video
a=msid-semantic: WMS cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0
m=audio 9 UDP/TLS/RTP/SAVPF 111 103 104 9 0 8 106 105 13 110 112 113 126
c=IN IP4 0.0.0.0
a=rtcp:9 IN IP4 0.0.0.0
a=ice-ufrag:DVsE
a=ice-pwd:LH2pdWfz6IErv5NkfrsUFbV0
a=ice-options:trickle
a=fingerprint:sha-256 DD:AB:DF:D5:2F:4A:EB:11:B4:BF:DA:5F:F9:A7:F2:D5:B6:F3:49:05:6C:11:D0:68:0C:56:25:F0:14:86:99:34
a=setup:actpass
a=mid:audio
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level
a=sendrecv
a=rtcp-mux
a=rtpmap:111 opus/48000/2
a=rtcp-fb:111 transport-cc
a=fmtp:111 minptime=10;useinbandfec=1
a=rtpmap:103 ISAC/16000
a=rtpmap:104 ISAC/32000
a=rtpmap:9 G722/8000
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:106 CN/32000
a=rtpmap:105 CN/16000
a=rtpmap:13 CN/8000
a=rtpmap:110 telephone-event/48000
a=rtpmap:112 telephone-event/32000
a=rtpmap:113 telephone-event/16000
a=rtpmap:126 telephone-event/8000
a=ssrc:58207530 cname:SV5GHf9diodyGDBM
a=ssrc:58207530 msid:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0 5b535613-8959-4e9f-a28a-814821317e2e
a=ssrc:58207530 mslabel:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0
a=ssrc:58207530 label:5b535613-8959-4e9f-a28a-814821317e2e
m=video 9 UDP/TLS/RTP/SAVPF 96 97 98 99 100 101 102 123 127 122 125 107 108 109 124
c=IN IP4 0.0.0.0
a=rtcp:9 IN IP4 0.0.0.0
a=ice-ufrag:DVsE
a=ice-pwd:LH2pdWfz6IErv5NkfrsUFbV0
a=ice-options:trickle
a=fingerprint:sha-256 DD:AB:DF:D5:2F:4A:EB:11:B4:BF:DA:5F:F9:A7:F2:D5:B6:F3:49:05:6C:11:D0:68:0C:56:25:F0:14:86:99:34
a=setup:actpass
a=mid:video
a=extmap:2 urn:ietf:params:rtp-hdrext:toffset
a=extmap:3 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time
a=extmap:4 urn:3gpp:video-orientation
a=extmap:5 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay
a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type
a=extmap:8 http://www.webrtc.org/experiments/rtp-hdrext/video-timing
a=sendrecv
a=rtcp-mux
a=rtcp-rsize
a=rtpmap:96 VP8/90000
a=rtcp-fb:96 goog-remb
a=rtcp-fb:96 transport-cc
a=rtcp-fb:96 ccm fir
a=rtcp-fb:96 nack
a=rtcp-fb:96 nack pli
a=rtpmap:97 rtx/90000
a=fmtp:97 apt=96
a=rtpmap:98 VP9/90000
a=rtcp-fb:98 goog-remb
a=rtcp-fb:98 transport-cc
a=rtcp-fb:98 ccm fir
a=rtcp-fb:98 nack
a=rtcp-fb:98 nack pli
a=fmtp:98 x-google-profile-id=0
a=rtpmap:99 rtx/90000
a=fmtp:99 apt=98
a=rtpmap:100 H264/90000
a=rtcp-fb:100 goog-remb
a=rtcp-fb:100 transport-cc
a=rtcp-fb:100 ccm fir
a=rtcp-fb:100 nack
a=rtcp-fb:100 nack pli
a=fmtp:100 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42001f
a=rtpmap:101 rtx/90000
a=fmtp:101 apt=100
a=rtpmap:102 H264/90000
a=rtcp-fb:102 goog-remb
a=rtcp-fb:102 transport-cc
a=rtcp-fb:102 ccm fir
a=rtcp-fb:102 nack
a=rtcp-fb:102 nack pli
a=fmtp:102 level-asymmetry-allowed=1;packetization-mode=0;profile-level-id=42001f
a=rtpmap:123 rtx/90000
a=fmtp:123 apt=102
a=rtpmap:127 H264/90000
a=rtcp-fb:127 goog-remb
a=rtcp-fb:127 transport-cc
a=rtcp-fb:127 ccm fir
a=rtcp-fb:127 nack
a=rtcp-fb:127 nack pli
a=fmtp:127 level-asymmetry-allowed=1;packetization-mode=1;profile-level-id=42e01f
a=rtpmap:122 rtx/90000
a=fmtp:122 apt=127
a=rtpmap:125 H264/90000
a=rtcp-fb:125 goog-remb
a=rtcp-fb:125 transport-cc
a=rtcp-fb:125 ccm fir
a=rtcp-fb:125 nack
a=rtcp-fb:125 nack pli
a=fmtp:125 level-asymmetry-allowed=1;packetization-mode=0;profile-level-id=42e01f
a=rtpmap:107 rtx/90000
a=fmtp:107 apt=125
a=rtpmap:108 red/90000
a=rtpmap:109 rtx/90000
a=fmtp:109 apt=108
a=rtpmap:124 ulpfec/90000
a=ssrc-group:FID 3001871990 1170592489
a=ssrc:3001871990 cname:SV5GHf9diodyGDBM
a=ssrc:3001871990 msid:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0 e2c7476d-b57c-4cc3-aeea-655027a65902
a=ssrc:3001871990 mslabel:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0
a=ssrc:3001871990 label:e2c7476d-b57c-4cc3-aeea-655027a65902
a=ssrc:1170592489 cname:SV5GHf9diodyGDBM
a=ssrc:1170592489 msid:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0 e2c7476d-b57c-4cc3-aeea-655027a65902
a=ssrc:1170592489 mslabel:cAXpgsoy4vy2meOcCZKArnmoPxaWxwEPuur0
a=ssrc:1170592489 label:e2c7476d-b57c-4cc3-aeea-655027a65902
```