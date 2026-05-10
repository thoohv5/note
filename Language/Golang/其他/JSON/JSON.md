---
title: 概述
date: 2026-04-15
tags: [编程语言, Golang, 其他]
type: note
status: incomplete
---

## 概述

**`JSON**(JavaScript Object Notation)` 是一种轻量级的数据交换格式。基于 `JavaScript Programming Language, Standard ECMA-262 3rd Edition - December 1999` 的一个子集。

`JSON` 建构于两种结构

- “名称/值”对的集合（`A collection of name/value pairs`）
- 值的有序列表（`An ordered list of values`）

值（`value`）可以是双引号括起来的字符串（`string`）、数值 (`number`)、布尔 (`true/false`)、 `null`、对象（`object`）或者数组（`array`）。

## GO 语言中的 JSON

### **布尔型**

`Boolean values encode as JSON booleans.`

```go
type Regex struct {
	T bool
	F bool
}

func main() {
	var r *Regex
	r = &Regex{
		T: true,
		F: false,
	}
	bs, err := json.Marshal(r)
	// {"T":true,"F":false} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

## **数值型**

`Floating point, integer, and [Number] values encode as JSON numbers.`

`NaN and +/-Inf values will return an [UnsupportedValueError].`

```go
type Regex struct {
	A int32
	B float64
}

func main() {
	var r *Regex
	r = &Regex{
		A: 1,
		B: 1.1,
	}
	bs, err := json.Marshal(r)
	// {"A":1,"B":1.1} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

## **字符串**

`String values encode as JSON strings coerced to valid UTF-8, replacing invalid bytes with the Unicode replacement rune. So that the JSON will be safe to embed inside HTML <script> tags, the string is encoded using [HTMLEscape], which replaces "<", ">", "&", U+2028, and U+2029 are escaped to "\\u003c","\\u003e", "\\u0026", "\\u2028", and "\\u2029". This replacement can be disabled when using an [Encoder], by calling [Encoder.SetEscapeHTML](false).`

反斜杠 (`U+005C`)、回车 (`U+000D`)、行分隔符 (`U+2028`)、段分隔符 (`U+2029`)、换行符 (`U+000A`)

```go
type Regex struct {
	S string
}

func main() {
	var r *Regex
	r = &Regex{
		S: `< > & \\`,
	}
	bs, err := json.Marshal(r)
	// {"S":"\\u003c \\u003e \\u0026 \\\\"} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
	
	// {"S":"< > & \\\\"}
	je := json.NewEncoder(os.Stdout)
	je.SetEscapeHTML(false)
	je.Encode(r)
}
```

## **数组型**

`Array and slice values encode as JSON arrays, except that []byte encodes as a base64-encoded string, and a nil slice encodes as the null JSON value.`

```go
type Regex struct {
	A []byte
}

func main() {
	var r *Regex
	r = &Regex{
		A: bytes.Repeat([]byte("A"), 100),
	}
	bs, err := json.Marshal(r)
	// {"A":"QUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQQ=="} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
	// QUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQUFBQQ==
	fmt.Println(base64.StdEncoding.EncodeToString(bytes.Repeat([]byte("A"), 100)))
}
```

## 结构体

`Struct values encode as JSON objects. Each exported struct field becomes a member of the object, using the field name as the object key, unless the field is omitted for one of the reasons given below.`

可见字段可导出，默认按照字段的名称导出。指定 `json` 标签可指定导出字段 `key` 。

```go
type Regex struct {
	A string `json:"a"`
	B string `json:"b,"`
	c string `json:"c,"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: "1",
		B: "2",
		c: "3",
	}
	bs, err := json.Marshal(r)
	// {"a":"1","b":"2"} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

### **字段忽略**(`-`)

默认可见字段可导出，指定 `-` 标识可忽略导出字段。

```go
type Regex struct {
	A string `json:"-"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: "",
	}
	bs, err := json.Marshal(r)
	// {} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

### **空值忽略**(`omitempty`)

`defined as false, 0, a nil pointer, a nil interface value, and any array, slice, map, or string of length zero.`

```go
type Regex struct {
	A bool                   `json:"a,omitempty"`
	B int32                  `json:"b,omitempty"`
	C *Regex                 `json:"c,omitempty"`
	D interface{}            `json:"d,omitempty"`
	E []string               `json:"e,omitempty"`
	F map[string]interface{} `json:"f,omitempty"`
	G string                 `json:"g,omitempty"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: false,
		B: 0,
		C: nil,
		D: nil,
		E: make([]string, 0),            // 等同 nil
		F: make(map[string]interface{}), // 等同 nil
		G: "",
	}
	bs, err := json.Marshal(r)
	// {} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

### **零值忽略**(`omitzero`)

**Go 1.24**

实现 `IsZero() bool` 可满足零值忽略导出。

```go
type Regex struct {
	A bool                   `json:"a,omitzero"`
	B int32                  `json:"b,omitzero"`
	C *Regex                 `json:"c,omitzero"`
	D interface{}            `json:"d,omitzero"`
	E []string               `json:"e,omitzero"`
	F map[string]interface{} `json:"f,omitzero"`
	G string                 `json:"g,omitzero"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: false,
		B: 0,
		C: nil,
		D: nil,
		E: make([]string, 0),            // 不等同 nil
		F: make(map[string]interface{}), // 不等同 nil
		G: "",
	}
	bs, err := json.Marshal(r)
	// {"e":[],"f":{}} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

### 与 omitempty 对比

| 特性            | omitempty       | omitzero      |
| ------------- | --------------- | ------------- |
| **忽略条件**      | 空值（empty）       | 零值（zero）      |
| **time.Time** | 不忽略零值时间         | 忽略零值时间        |
| **空切片**       | 忽略 `[]` 和 `nil` | 只忽略 `nil`     |
| **自定义判断**     | ❌ 不支持           | ✅ 支持 IsZero() |
| **适用场景**      | 常规空值忽略          | 精确零值控制        |

### **字符串化**(`string`)

仅支持 `string, floating point, integer, or boolean` 类型的字段

```go
type Regex struct {
	A bool    `json:"a,string"`
	B int32   `json:"b,string"`
	C float64 `json:"c,string"`
	D string  `json:",string"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: false,
		B: 0,
		C: 0.0,
		D: "",
	}
	bs, err := json.Marshal(r)
	// {"a":"false","b":"0","c":"0","D":"\\"\\""} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

### **内嵌结构**(`Embedded struct`)

`JSON` 字符串扁平化，需要

- 匿名结构体
- `inline` 标签（低版本需要此标签）

```go
type AStruct struct {
	B int32 `json:"b,string"`
}
type Regex struct {
	A bool `json:"a"`
	AStruct
	A2 AStruct `json:",inline"`
}

func main() {
	var r *Regex
	r = &Regex{
		A: true,
		AStruct: AStruct{
			B: 0,
		},
		A2: AStruct{
			B: 0,
		},
	}
	bs, err := json.Marshal(r)
	// {"a":true,"b":"0","A2":{"b":"0"}} <nil>
	fmt.Println(bytes.NewBuffer(bs).String(), err)
}
```

## **KV 字典**(`Map values`)

`Map values encode as JSON objects. The map's key type must either be a string, an integer type, or implement [encoding.TextMarshaler]. The map keys are sorted and used as JSON object keys by applying the following rules, subject to the UTF-8 coercion described for string values above:`

- `keys of any string type are used directly`
- `keys that implement [encoding.TextMarshaler] are marshaled`
- `integer keys are converted to strings`

## **空对象**(`null`)

`Pointer values encode as the value pointed to. A nil pointer encodes as the null JSON value.`

## 不支持类型

`Channel, complex, and function values cannot be encoded in JSON. Attempting to encode such a value causes Marshal to return an [UnsupportedTypeError].`

Channel, complex, function 等类型不能被 JSON 序列化，Marshal 会返回 UnsupportedTypeError 错误。循环引用的数据结构也会引起 Marshal 报错。对于不支持的类型 Marshal 报错 UnsupportedTypeError，对于支持的类型，但不支持的值会报错 UnsupportedValueError。

_**Marshal**_ 不支持的标准类型有 _**Complex64**_ ，_**Complex128**_ ，_**Chan**_ ，_**Func**_ ，_**UnsafePointer**_ ，这种情况下会返回 _**UnsupportedTypeError**_ 。

对于不支持的数据类型，需要实现 _**MarshalJSON**_ 或者 _**encoding.TextMarshaler**_ 接口。

对于不支持的值，会返回 _**UnsupportedValueError**_ 错误，如浮点数的无穷大，无穷小，NaN 和出现循环引用的 map、slice 和 pointer。

## 方法

### `json.Marshal`

```go
// Marshal returns the JSON encoding of v.
func Marshal(v any) ([]byte, error)

以下方法等同

// NewEncoder returns a new encoder that writes to w.
func NewEncoder(w io.Writer) *Encoder

// Encode writes the JSON encoding of v to the stream,
// with insignificant space characters elided,
// followed by a newline character.
//
// See the documentation for [Marshal] for details about the
// conversion of Go values to JSON.
func (enc *Encoder) Encode(v any) error

```

依次调用 `Marshaler.MarshalJSON` `encoding.TextMarshaler.MarshalText`

```go
type Student struct {
	No string
}

func (s *Student) MarshalJSON() ([]byte, error) {
	return bytes.NewBufferString(s.No).Bytes(), nil
}

func (s *Student) MarshalText() (text []byte, err error) {
	return bytes.NewBufferString(fmt.Sprintf("%s1", s.No)).Bytes(), nil
}

func main() {
	bs, _ := json.Marshal(&Student{
		No: "1",
	})
	// 1
	fmt.Println(bytes.NewBuffer(bs).String())

	// 1
	json.NewEncoder(os.Stdout).Encode(&Student{No: "1"})
}
```

### `json.Unmarshal`

```go
// Unmarshal parses the JSON-encoded data and stores the result
// in the value pointed to by v. If v is nil or not a pointer,
// Unmarshal returns an [InvalidUnmarshalError].
func Unmarshal(data []byte, v any) error

以下方法等同

// NewDecoder returns a new decoder that reads from r.
//
// The decoder introduces its own buffering and may
// read data from r beyond the JSON values requested.
func NewDecoder(r io.Reader) *Decoder

// Decode reads the next JSON-encoded value from its
// input and stores it in the value pointed to by v.
//
// See the documentation for [Unmarshal] for details about
// the conversion of JSON into a Go value.
func (dec *Decoder) Decode(v any) error
```

依次调用 `Unmarshaler.UnmarshalJSON` `encoding.TextUnmarshaler.UnmarshalText`

```go
type Student struct {
	No string `json:"no"`
}

func (s *Student) UnmarshalJSON(bs []byte) error {
	s.No = bytes.NewBuffer(bs).String()
	return nil
}

func main() {
	s := &Student{}
	json.Unmarshal([]byte(`{"no": "1"}`), s)
	// {"no": "1"}
	fmt.Println(s.No)

	json.NewDecoder(bytes.NewBuffer([]byte(`{"no": "1"}`))).Decode(&s)
	// {"no": "1"}
	fmt.Println(s.No)
}
```

### `json.RawMessage`

```go
// RawMessage is a raw encoded JSON value.
// It implements [Marshaler] and [Unmarshaler] and can
// be used to delay JSON decoding or precompute a JSON encoding.
type RawMessage []byte
```

`RawMessage` 是 `[]byte` 的别名，被声明为 `RawMessage` 类型的字段，在 `Marshal` 和 `Unmarshal` 过程中将不被处理，因此如果我们将透传的信息存储在声明为 `RawMessage` 类型的字段中，就可以避免 `JSON` 的二次编码。

### `json.Number`

```go
// A Number represents a JSON number literal.
type Number string
```

被声明为 `Number` 类型的字段，在 `Unmarshal` 过程中将可接受 `Number` 类型的字符串。

```go
type Regex struct {
	A int32       `json:"a"`
	B json.Number `json:"b"`
}

func main() {
	r := &Regex{}
	json.Unmarshal([]byte(`{"a":"1", "b":"2"}`), r)
	// 0
	fmt.Println(r.A)
	// 2 <nil>
	fmt.Println(r.B.Int64())
}
```

检查 `Number` 类型的字符串

```go
// isValidNumber reports whether s is a valid JSON number literal.
func isValidNumber(s string) bool {
	// This function implements the JSON numbers grammar.
	// See <https://tools.ietf.org/html/rfc7159#section-6>
	// and <https://json.org/number.gif>

	if s == "" {
		return false
	}

	// Optional -
	if s[0] == '-' {
		s = s[1:]
		if s == "" {
			return false
		}
	}

	// Digits
	switch {
	default:
		return false

	case s[0] == '0':
		s = s[1:]

	case '1' <= s[0] && s[0] <= '9':
		s = s[1:]
		for len(s) > 0 && '0' <= s[0] && s[0] <= '9' {
			s = s[1:]
		}
	}

	// . followed by 1 or more digits.
	if len(s) >= 2 && s[0] == '.' && '0' <= s[1] && s[1] <= '9' {
		s = s[2:]
		for len(s) > 0 && '0' <= s[0] && s[0] <= '9' {
			s = s[1:]
		}
	}

	// e or E followed by an optional - or + and
	// 1 or more digits.
	if len(s) >= 2 && (s[0] == 'e' || s[0] == 'E') {
		s = s[1:]
		if s[0] == '+' || s[0] == '-' {
			s = s[1:]
			if s == "" {
				return false
			}
		}
		for len(s) > 0 && '0' <= s[0] && s[0] <= '9' {
			s = s[1:]
		}
	}

	// Make sure we are at the end.
	return s == ""
}
```

## 附录

[JSON](https://www.json.org/json-en.html)

[Undefined 包装类型](https://www.notion.so/Undefined-1efa733ddc10805aacc1e3ce83fb1c58?pvs=21)
