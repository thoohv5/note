# mitchellh/mapstructure

[https://www.jb51.net/article/271451.htm](https://www.jb51.net/article/271451.htm)

mapstructure用于将通用的map[string]interface{}解码到对应的 Go 结构体中，或者执行相反的操作。很多时候，解析来自多种源头的数据流时，我们一般事先并不知道他们对应的具体类型。只有读取到一些字段之后才能做出判断

+

### 目录

在数据传递时，需要先编解码；常用的方式是JSON编解码（参见《[golang之JSON处理](https://www.jb51.net/article/255438.htm)》）。但有时却需要读取部分字段后，才能知道具体类型，此时就可借助mapstructure库了。

## mapstructure库

mapstructure可方便地实现`map[string]interface{}`与`struct`间的转换；使用前，需要先导入库：

> go get github.com/mitchellh/mapstructure
> 

### 字段标签

默认情况下，mapstructure使用字段的名称做匹配映射（即在map中以字段名为键值查找字段值）；注意匹配时是忽略大小写的。也可通过标签来设定字段映射名称：

| 123 | `type` `Person struct` `{Name string` ``mapstructure:"userName"`}` |
| --- | --- |

### 内嵌结构

go中结构体是可以任意嵌套的；嵌套后即认为拥有对应的字段。但是，默认情况下mapstructure只处理当前结构定义的字段，若要自动处理内嵌字段需要添加标签`squash`：

| 1234 | `type` `Student struct` `{Person `mapstructure:",squash"`Age int}` |
| --- | --- |

### 未映射字段

若源数据中有未映射的值（即结构体中无对应的字段），mapstructure默认会忽略它。可以在结构体中定义一个特殊字段（类型为`map[string]interface{}`，且标签要设置为`mapstructure:",remain"`），来存放所有未能映射的字段中。

| 12345 | `type` `Student struct` `{Name  stringAge   intOther map[string]interface{} `mapstructure:",remain"`}` |
| --- | --- |

### Metadata

mapstructure中可以使用Metadata收集一些解码时会产生的有用信息。

| 123456 | `// mapstructure.gotype` `Metadata struct` `{Keys   []string` `// 解码成功的键Unused []string` `// 源数据中存在，但目标结构中不存在的键Unset  []string` `// 未设定的（源数据中缺失的）键}` |
| --- | --- |

为了获取这些信息，需要使用DecodeMetadata来解码：

> var metadata mapstructure.Metadata
> 
> 
> err := mapstructure.DecodeMetadata(m, &p, &metadata)
> 

### 弱类型输入

有时候，并不想对结构体字段类型和`map[string]interface{}`的对应键值做强类型一致的校验。这时可以使用WeakDecode/WeakDecodeMetadata方法，它们会尝试做类型转换：

- 布尔转字符串：true = “1”, false = “0”;
- 布尔转数字：true = 1, false = 0；
- 数字转布尔：true if value != 0;
- 字符串转布尔：可接受，
- 真：1, t, T, TRUE, true, True
- 假：0, f, F, FALSE, false, False
- 数字转字符串：自动base10转换；
- 负数转为无符号数（上溢）；
- 字符串转数字：根据前缀（如0x等）转换；
- 空数组与空map间互转；
- 单个值转为切片；

### 逆向转换

除将map转换为结构体外，mapstructure也可以将结构体反向解码为`map[string]interface{}`。在反向解码时，我们可以为某些字段设置mapstructure:“,omitempty”，当这些字段为默认值时，就不会出现在map中：

| 123456 | `p := &Student{Name: "Mike",Age:  12,}var` `m map[string]interface{}mapstructure.Decode(p, &m)` |
| --- | --- |

## 解码器

mapstructure提供了解码器（Decoder），可灵活方便地控制解码：

| 123456789101112131415161718192021222324252627282930 | `type` `DecoderConfig struct` `{// 若设定，则在任何解码或类型转换（设定了WeaklyTypedInput）前调用；对于设定了squash的内嵌字段，整体调用一次；若返回错误，则整个解码失败DecodeHook DecodeHookFunc// 若设定，则源数据中存在未使用字段时，报错ErrorUnused bool// 若设定，则有字段未设定时，报错ErrorUnset bool// 若设定，则在设定字段前先清空（对于map等类型会先清理掉旧数据）ZeroFields bool// 若设定，支持若类型间的转换WeaklyTypedInput bool// Squash will squash embedded structs. Squash bool// Metadata is the struct that will contain extra metadata about// the decoding. If this is nil, then no metadata will be tracked.Metadata *Metadata// Result is a pointer to the struct that will contain the decoded// value.Result interface{}// The tag name that mapstructure reads for field names. This// defaults to "mapstructure"TagName string// IgnoreUntaggedFields ignores all struct fields without explicit// TagName, comparable to `mapstructure:"-"` as default behaviour.IgnoreUntaggedFields bool// MatchName is the function used to match the map key to the struct// field name or tag. Defaults to `strings.EqualFold`. This can be used// to implement case-sensitive tag values, support snake casing, etc.MatchName func(mapKey, fieldName string) bool}` |
| --- | --- |

一个支持弱类型转换的示例：要获取的结果放到config的result中

| 123456789101112131415161718192021222324252627 | `Name stringAge  int}func` `decoderConfig() {m := map[string]interface{}{"name": 123,"age":  "12","job":  "programmer",}var` `p Personvar` `metadata mapstructure.Metadatadecoder, err := mapstructure.NewDecoder(&mapstructure.DecoderConfig{WeaklyTypedInput: true,Result:           &p,Metadata:         &metadata,})if` `err != nil` `{log.Fatal(err)}err = decoder.Decode(m)if` `err == nil` `{log.Printf("Result: %#v", p)log.Printf("keys:%#v, unused:%#v\n", metadata.Keys, metadata.Unused)} else` `{log.Println("decode fail:", err)}}` |
| --- | --- |

## 示例

通过一个messageData结构，action会指示最终的data类型。接收到数据后，先解析出atcion，再根据action转换为真实的类型。

因time.Time是一个结构体（json序列化时会转换为时间字符串），mapstructure无法正确处理，所以推荐使用时间戳。

为了能正确解析内嵌的DataBasic，需要标记为squash。

| 1234567891011121314151617181920212223242526272829303132333435363738394041424344454647 | `import` `"github.com/mitchellh/mapstructure"type` `DataBasic struct` `{DataId     string` ``json:"dataId"`UpdateTime int64` ``json:"updateTime"`}type` `AddedData struct` `{DataBasic `mapstructure:",squash"`Tag string` ``json:"tag"`AddParams map[string]any `json:"addParams"`}type` `messageData struct` `{Action    int` ``json:"action"`SeqId     uint64` ``json:"seqId"`Data      any    `json:"data"`}func` `decodeData() {add := &AddedData{DataBasic: DataBasic{DataId:     "a2",UpdateTime: time.Now().UnixMilli(),},Tag: "tag",AddParams:  map[string]any{"dataId": "c2", "otherId": "t2"},}data := &messageData{Action: 1,Data:   add,}js, err := json.Marshal(data)if` `err != nil` `{log.Printf("marshal fail: %v", err)return}got := &messageData{}err = json.Unmarshal(js, got)if` `err != nil` `{log.Printf("unmarshal fail: %v", err)return}param := new(AddedData)err = mapstructure.Decode(got.Data, param)if` `err != nil` `{log.Printf("unmarshal fail: %v", err)return}log.Printf("param: %+v", param)}` |
| --- | --- |