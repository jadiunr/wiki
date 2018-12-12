<!-- TITLE: Credentials -->
<!-- SUBTITLE: A quick summary of Credentials -->

# credentials.yml.encに改行が入ってると終わる

- 諸事情ありmaster.keyとcredentials.yml.encを別の環境にコピーして`rails credentials:edit`したら死ぬほど怒られた

```
/usr/local/bundle/gems/activesupport-5.2.1/lib/active_support/message_encryptor.rb:206:in `rescue in _decrypt': ActiveSupport::MessageEncryptor::InvalidMessage (ActiveSupport::MessageEncryptor::InvalidMessage)
```

## Solution

- credentials.yml.encの改行を消す

`perl -pe 's/\n//g' -i config/credentials.yml.enc`

- なんなんだよマジで