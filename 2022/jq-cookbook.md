[//title]: (jq-cookbook)
[//englishtitle]: (jq-cookbook)
[//category]: (linux,jq,tutorial,cookbook)
[//tags]: (linux,jq,tutorial,cookbook)
[//createtime]: (20220428)
[//updatetime]: (20220428)

## sample

取出数组中指定 key 的 value

```
$ echo '{
  "result": {
    "person": [
      {
        "name": "alice",
        "age": 20
      },
      {
        "name": "bob",
        "age": 30
      },
      {
        "name": "frank",
        "age": 40
      }
    ]
  }
}' | jq '.result.person | .[].name'

"alice"
"bob"
"frank"
```
