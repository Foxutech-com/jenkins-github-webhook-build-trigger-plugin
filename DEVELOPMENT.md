### Tests

```bash
mvn compile && rm -rf work/ && mvn hpi:run
// configure GitHub Secret to `foobar23` via webui
```


**Testcase: Push Event**

```bash
curl -X POST \
    -H "Content-Type: application/json" \
    -H "x-hub-signature: sha1=5b8a54ee48efb1fbe06e3e4fc5d120680eaa2a22" \
    -d @test-webhook-payload.json \
    http://localhost:8080/jenkins/github-webhook-build-trigger/receive
```

&nbsp;


**Testcase: Ping Event**
 
Initial webhook created request. 
Only fired once when you setup the webhook on github. See: https://developer.github.com/webhooks/#ping-event

```bash
curl -X POST \
    -H "Content-Type: application/json" \
    -H "x-hub-signature: sha1=c203f51e9317264c6716ee0c6fed59d604674885" \
    -d @test-webhook-init-payload.json \
    http://localhost:8080/jenkins/github-webhook-build-trigger/receive
```

&nbsp;

**x-hub-signature**

Create an sha1 hash for a payload.json:

```bash
cat test-webhook-init-payload.json | openssl dgst -sha1 -hmac "foobar23" 
```

### Build hpi

```
mvn clean && mvn compile && mvn hpi:hpi
```


### Notes

Howto manually checkout certain branch at specific revision

```
# Clone develop branch
git clone --single-branch --branch develop https://github.com/codeclou/test-webhook.git foo
# Switch to revision
git reset --hard 495140cf2b763b8bc3821e25b4c26dd1273d6206
```


### Webhook Payload Examples


##### Tag Push Payload

```bash
# on head of master
git tag -a 0.0.1 -m "0.0.1"
git push origin 0.0.1
```

Webhook payload:

```json
{
  "ref": "refs/tags/0.0.1",
  "before": "0000000000000000000000000000000000000000",
  "after": "80aa5698b8688df969d56678d5304f4450adda84",
  "created": true,
  "deleted": false,
  "forced": false
}
```

##### Branch Push Payload

```bash
# on head of master
git branch develop
git push origin develop
```

Webhook payload:

```json
{
  "ref": "refs/heads/develop",
  "before": "0000000000000000000000000000000000000000",
  "after": "495140cf2b763b8bc3821e25b4c26dd1273d6206",
  "created": true,
  "deleted": false,
  "forced": false
}
```

##### Normal commit Push Payload

```bash
# on head of master
echo "foo" > newfile.txt
git add . -A
git commit -m "foo"
git push
```

Webhook payload:

```json
{
  "ref": "refs/heads/master",
  "before": "495140cf2b763b8bc3821e25b4c26dd1273d6206",
  "after": "3be1cb4b6b86533b5dab2b0083fa9fb8b401b430",
  "created": false,
  "deleted": false,
  "forced": false
}
```
