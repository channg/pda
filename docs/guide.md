# 5分钟入门
---


>上一篇只是给我们介绍了coir最简单的配置，如果你想要了解更多的配置，可以阅读本篇内容。

**重复**的工作是让人厌倦的。

比如说每天都要完成这样的一个文本文件，来记录一下今天的心情。1

```
data: 2018-7-29

Are you happy today: happy

A word about what you did today : learning

Have a good Day!

```

那么，到了这个时候，我都需要先创建一个`txt`的文本文件

然后找到昨天的文本，复制进入文档内，然后再进行修改，最后得到如下的记录。

<img src="https://i.h2.pdim.gs/68ef33b1bb9bd576da309ac2eac3f1e5.gif" ></img>

我需要复制，粘贴内容，然后再删除掉昨天的错误的内容，虽然这很少，但是这却是重复性的工作，我希望这些操作能变得更高效。

为了更高效的完成这些，我们将内容整理成为三个`Questions`

* What's today's date?
* Are you happy today ?
* A word about what you did today.

我希望能通过完成这几个问题，从而直接生成一个日记文件。

首先，我们将这个问题放入一个json文件中。
```
{
	"1":{
		"question":"What's today's date?"
	},
	"2":{
		"question":"A word about what you did today."
	},
	"3":{
		"question":"A word about what you did today."
	}
}
```

有了问题，那么我们必须有回答了，回答，也就代表输出。那么我需要给每个问题加上`output`

```
{
	"1":{
		"question":"What's today's date?",
		"output":...
		},
	...
}
```
有了回答，我们就需要校验这个回答是否合理，加上一个`test`，校验一下日期格式
```
{
	"1":{
		"question":"What's today's date?",
		"output":{
			"test":"^[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}$"
			}
		}
}
```
一个问题，当回答之后应该得到一个输出，但是这个输出，并不一定是输入本身，有可能是经过拼接之后的字符。
所以我们必须要把输入用**特殊标记**表示。`coir`用`__this__`输入的标识。
最后我们在最外层加入一个字段`inquire`标识提问。

```json
{
  "inquire": {
    "1": {
      "question": "What's today's date?",
      "output": {
        "test": "^[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}$",
        "value": "__this__"
      }
    }
  }
}
```

这时候，如果你已经安装了`coir`或者系统中有安装`npx`(`npm 5.2.0`以上版本自动携带)，你可以将上面的json保存为`coir.json`并运行
```
coir use
```
或者使用`npx`
```
npx coir use
```
会在命令行直接得到这样的问题。
```
? What's today's date?
```

所以当用户输入完成所有的问题，程序就可以根据用户的输入去生成文件。

如何生成呢？

需要特殊的标识去替换文件里的内容，coir使用`__number__`的形式去替换文件`root`文件夹内的文件，文件名。

创建一个`root`文件夹，并与`coir.json`的位置同级，在`root`文件夹内创建一个`__1__.json`，内容如下

```
data: __1__

Are you happy today: __2__

A word about what you did today : __3__

Have a good Day!

```


coir.json内容最终如下

```
{
  "inquire": {
    "1": {
      "question": "What's today's date?",
      "output": {
        "test": "^[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}$",
        "value": "__this__"
      }
    },
    "2":{
      "type":"list",
      "question": "Are you happy today",
      "output": [{
        "test": "happy",
        "value": "__this__"
      },{
        "test": "not happy",
        "value": "__this__"
      }]
    },
    "3":{
      "question": "A word about what you did today",
      "output": {
        "test": ".*",
        "value": "__this__"
      }
    }
  }
}
```

此时我们在当前目录打开命令行，输入`coir use`，就可以生成日记了。

这样用户输入完成后`coir`就会自动帮我们生成一个文件了。
