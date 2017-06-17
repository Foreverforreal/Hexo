title: java 网络编程（五）Cookie管理
id: 1495597015438
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 网络编程
date: 2017-05-24 11:37:00
---
Java SE为cookie管理提供一个主要的类——java.net.CookieHandler,以及一些相关的支持类和接口：java.net.CookieManager,java.net.CookiePolicy,java.net.CookieStore和java.net.HttpCookie。
***
# 使用Cookie的HTTP状态管理
***
HTTP状态管理机制指定了一种使用HTTP请求和响应创建有状态会话的方法。

通常，HTTP请求/响应是彼此独立的。然而状态管理机制使客户端和服务器能够交换状态信息，来将一个请求/相应对放在一个大的被称为会话（session）上下文中。这种用来创建和维持会话的状态信息就被称为cookie。

<!-- more -->

Cookie是可以存储在浏览器缓存中的一段数据。如果您访问网站，然后重新访问，则可以使用Cookie数据将您标识为再次访问者。Cookie可以记录状态信息，例如在线购物车。Cookie可以是短期保存单个Web会话的数据，直到您关闭浏览器它都会有效。同时Cookie也可以长期有效，在一周或一年的期限内保存数据。
***
# CookieHandler回调机制
***
HTTP状态管理在Java SE中是通过java.net.CookieHandler来实现的。一个CookieHandler对象提供了一个回调机制，来在HTTP协议的处理程序中提供了HTTP状态管理策略实现。也即是说，URL使用HTTP作为协议的话，比如（URL("http ://example.com")），它会使用HTTP协议处理程序。这个协议处理程序会调用CookeiHandler对象（如果我们设置了的话）来处理状态管理。

CookieHandler是一个抽象类，它提供了两对相关的方法。第一对是getDefalut()和setDefault(coookieHandler),它们是静态方法使我们能获取和设置当前要使用的cookieHandler。
默认情况下是没有设置cookieHandler的，并且如果我们安装一个cookieHandler的话是基于全系统安装。对于一个在安全环境下运行的应用程序，他们都有安装一个安全管理器，因此你必须有指定的权限来获取或设置这个cookieHandler.
第二对相关方法是put(uri,responseHeaders)和get(uri,requestHeaders),这两个方法使我们能分别在在cookie缓存中为请求/响应头中的指定的URI，设置和获取所有的合适的cookies。这些方法都是抽象方法，需要我们在CookieHandler实现类中来实现。

Java Web Start 和 Java Plug-in 中都有默认安装了一个CookieHandler。但是，如果您运行一个独立应用程序并要启用HTTP状态管理，则必须设置一个全系统范围的处cookieHandler。
***
# 默认CookieManager
***
java.net.CookieManager提供了一个CookieHandler的具体实现类，它对大多数用户来说已经足够来处理HTTP状态管理。CookieManager将Cookie的存储与周边，接受和拒绝的策略分开。CookieManager通过使用一个java.net.CookieStore和java.net.CookiePolicy来初始化。CookieStore管理cookies的储存。CookiePolicy对cookie接受和拒绝做出决策。

下面代码展示了如果创建和设置一个全系统的CookieManager.
```java
CookieManager cm = new Cookie Manager();
CookieHander.setDefault(cm)
```

默认的CookieManager构造器通过一个默认的cookie储存和接收策略来创建一个新的cookieManager实例。CookieStore是存储任何被接受的HTTP cookie的地方。如果我们创建CookieManager的时候没有指定的话，CookieManager的实例就会使用一个内部内存实现。该实现不是长久的，只能在JVM运行的生命周期内存在。用户需要一持久存储的话必须有自己的存储实现。

CookieManeger使用的默认cookie政策是CookiePolicy.ACCEPT_ORIGINAL_SERVER，它只接受来之原始服务器的cookie。因此，来自服务器的Set-Cookie响应必须有一个“domain”属性集，并且它必须和URL中的主机域名匹配。用户需要一个不同的策略的话，必须实现CookiePolicy接口，并且将它作为CookieManager构造器的参数传递，或者使用一个已经初始化的CookieManager实例的setCookiePolicy(cookiePolicy)方法。

从Cookie存储区检索Cookie时，CookieManager也会强制执行路径匹配规则 。因此，cookie也必须设置其“path”属性，以便可以在从存储的cookie中检索到指定的cookie之前，能够执行路径匹配规则。

总之，CookieManager提供了处理Cookie的框架，并提供了一个很好的默认实现CookieStore。我们还可以通过设置CookieStore和CookiePolicy来高度可自定义一个CookieManager.

***
# 自定义CookieManger
***
CookieManger类有两个方面可以自定义：CookiePolicy和CookieStore。

## CookiePolicy

为方便起见，CookiePolicy定义以下预设的Cookie接受策略：

- **CookiePolicy.ACCEPT\_ORIGINAL\_SERVER**  只接受来自原始服务器的cookie
- **CookiePolicy.ACCEPT\_ALL**   接收所有的cookie
- **CookiePolicy.ACCEPT\_NONE**   不接受任何cookie.
- 也可以通过实现CookiePolicy的shouldAccept方法来第一自己的cookie接受策略。

以下是在应用CookiePolicy.ACCEPT_ORIGINAL_SERVER策略之前拒绝来自黑名单上的域的Cookie的Cookie策略的示例：

```java
import java.net.*;

public class BlacklistCookiePolicy implements CookiePolicy {
    String[] blacklist;

    public BlacklistCookiePolicy(String[] list) {
        blacklist = list;
    }

    public boolean shouldAccept(URI uri, HttpCookie cookie)  {
        String host;
        try {
            host =  InetAddress.getByName(uri.getHost()).getCanonicalHostName();
        } catch (UnknownHostException e) {
            host = uri.getHost();
        }

        for (int i = 0; i<blacklist.length; i++) {
	    if (HttpCookie.domainMatches(blacklist[i], host)) {
                return false;
            }
        }

        return CookiePolicy.ACCEPT_ORIGINAL_SERVER.shouldAccept(uri, cookie);
    }
}

```

当创建一个BlacklistCookiePolicy实例的时候，需要传入一个黑名单列表。然后将BlcacklistCookiePolicy实例作为cookie管理策略设置在CookieManager中。如例
```java
CookieManager cm = new CookieManager(null, new BlacklistCookiePolicy(list));
CookieHandler.setDefault(cm);
```

示例代码将不接受来自以下主机的Cookie，例如：  
>host.example.com 
>domain.example.com 

但是，此示例代码将接受来以下自主机的Cookie，如下所示：  
>example.com 
>example.org 
>myhost.example.org

## CookieStore

CookieStore是一个表示cookie存储区的接口。CookieManager为每个HTTP响应把cookie添加到CookieStore，并且在每个HTTP请求时，从CookieStore中取回cookie.

我们可以实现这个接口来提供我们自己的CookieStore,并且在创建CookieManager传递它作为构造器参数。我们无法在创建CookieManager实例后再来设置CookieStore。
然而我们可以通过调用CookieManager.getCookieStore()方法来获取cookie store的引用。这样做很有用，因为它可以利用CookieStoreJava SE提供的默认内存实现，并补充其功能。

例如，您可能需要创建一个保存Cookie的持久性Cookie存储，以便即使Java虚拟机重新启动也可以使用它们。您的实现将会以下类似。

1. 读入之前我们存储的所有cookie
2. 运行期间，cookie将会从内存中存储和检索cookie
3. 在退出之前将Cookie写入永久存储。

以下是此Cookie存储的不完整示例。此示例显示如何利用Java SE默认内存Cookie存储，以及如何扩展其功能。

```java
import java.net.*;
import java.util.*;

public class PersistentCookieStore implements CookieStore, Runnable {
    CookieStore store;

    public PersistentCookieStore() {
        // get the default in memory cookie store
        store = new CookieManager().getCookieStore();

        // todo: read in cookies from persistant storage
        // and add them store

        // add a shutdown hook to write out the in memory cookies
        Runtime.getRuntime().addShutdownHook(new Thread(this)); 
    }

    public void run() {
        // todo: write cookies in store to persistent storage
    }

    public void	add(URI uri, HttpCookie cookie) {
        store.add(uri, cookie);
    }

    public List<HttpCookie> get(URI uri) {
        return store.get(uri);
    }

    public List<HttpCookie> getCookies() {
        return store.getCookies();
    }
    
    public List<URI> getURIs() {
        return store.getURIs();
    }

    public boolean remove(URI uri, HttpCookie cookie) {
        return store.remove(uri, cookie);
    }

    public boolean removeAll()  {
        return store.removeAll();
    }
}

```