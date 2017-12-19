# til jq

## Turn an object into key paths

```
$ echo '{"a": 1, "b": 2, "c": { "d": 4 }}' | jq 'paths | map(.|tostring) | join(".")'
```

```
"a"
"b"
"c"
"c.d"
```
