     播放本地音乐文件，遇到奇葩的文件名是常有的事，而普通的setDataSource()，仅仅传入一个文件路径，这是不够的，猜测可能跟路径的转意有关。
这方面暂不追究，解决办法很简单，善于利用系统的Uri工具类。
     android.net.Uri类，在谷歌官方文档是这样介绍的：
     Immutable URI reference. A URI reference includes a URI and a fragment, the component of the URI following a '#'. Builds and parses URI references which conform to RFC 2396.
In the interest of performance, this class performs little to no validation. Behavior is undefined for invalid input. This class is very forgiving--in the face of invalid input, it will return garbage rather than throw an exception unless otherwise specified.

     这里我们需要利用它来转换File的path:
     Uri uri =Uri.fromFile(new File(path));
     // 得到规范的path 路径，方便软件库引用文件
     String uri_path = uri.getEncodedPath();
     // Android 的MediaPlayer 支持uri路径
     mediaplayer.setDataSource(context,Uri.fromFile(new File(path)));

     以上能够解决文件名包含特殊字符如 "+" , "#" , "$" 等无法播放的问题。

```

More info: [Deployment](http://hexo.io/docs/deployment.html)
