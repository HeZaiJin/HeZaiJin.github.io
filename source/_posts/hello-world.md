     ���ű��������ļ�������������ļ����ǳ��е��£�����ͨ��setDataSource()����������һ���ļ�·�������ǲ����ģ��²���ܸ�·����ת���йء�
�ⷽ���ݲ�׷��������취�ܼ򵥣���������ϵͳ��Uri�����ࡣ
     android.net.Uri�࣬�ڹȸ�ٷ��ĵ����������ܵģ�
     Immutable URI reference. A URI reference includes a URI and a fragment, the component of the URI following a '#'. Builds and parses URI references which conform to RFC 2396.
In the interest of performance, this class performs little to no validation. Behavior is undefined for invalid input. This class is very forgiving--in the face of invalid input, it will return garbage rather than throw an exception unless otherwise specified.

     ����������Ҫ��������ת��File��path:
     Uri uri =Uri.fromFile(new File(path));
     // �õ��淶��path ·������������������ļ�
     String uri_path = uri.getEncodedPath();
     // Android ��MediaPlayer ֧��uri·��
     mediaplayer.setDataSource(context,Uri.fromFile(new File(path)));

     �����ܹ�����ļ������������ַ��� "+" , "#" , "$" ���޷����ŵ����⡣

```

More info: [Deployment](http://hexo.io/docs/deployment.html)
