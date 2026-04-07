# strings.split

```go
strings.FieldsFunc(input, Split)
func Split(r rune) bool {
    return r == ':' || r == '.'
}
```