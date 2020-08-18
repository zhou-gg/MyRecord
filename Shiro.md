[toc]

# Shiro 的学习

## Shiro 的 介绍

>  Shiro是一种功能强大且易于使用的Java安全框架，它执行身份验证，授权，加密和会话管理，可用于保护任何应用程序的安全-从命令行应用程序，移动应用程序到最大的Web和企业应用程序。

### Shiro 的四个功能：

> - 身份验证-证明用户身份，通常称为用户“登录”。
> - 授权-访问控制
> - 密码学-保护或隐藏数据以防窥视
> - 会话管理-每个用户的时间敏感状态
>
> ![shirodesc](C:\Users\18354\Desktop\学习的记录\引用的图片\ShiroFeatures.png)
>
> ![核心图片](C:\Users\18354\Desktop\学习的记录\引用的图片\ShiroArchitecture.png)

### Shiro的优点：

> - **易于使用** -易于使用是该项目的最终目标。
> - **全面** 
> - **灵活** -Apache Shiro可以在任何应用程序环境中工作。尽管它可以在Web，EJB和IOC环境中运行，但并不需要它们。Shiro也不要求任何规范，甚至没有很多依赖性。
> - **具有Web功能** -Apache Shiro具有出色的Web应用程序支持，允许您基于应用程序URL和Web协议（例如REST）创建灵活的安全策略。

### Shiro的核心

> - Subject
> - SecurityManager
> - Realms

#### Subject

> ``` java
> import org.apache.shiro.subject.Subject;
> import org.apache.shiro.SecurityUtils;
> ...
> Subject currentUser = SecurityUtils.getSubject();
> ```
>
> > Subject 您可以立即访问当前用户想要使用Shiro进行的所有操作的90％，例如登录，注销，访问其会话，执行授权检查等等-但稍后会介绍更多。这里的关键是Shiro的API在很大程度上是直观的，因为它反映了开发人员在“每用户”安全控制中进行思考的自然趋势。在代码中的任何地方访问主题也很容易，从而可以在需要的地方进行安全操作。
>
> - com.apache.shiro.subject.Subject
>
>   > It is Shiro's primary mechanism for single-user security functionality.
>   >
>   > ```java
>   > SecurityUtils.getSubject()
>   >     //几乎所有安全操作都应使用Subject此方法返回的操作。
>   >     //此接口中有许多* Permission方法被重载以接受String参数而不是 Permission实例。它们是一种便利，允许调用方在需要时使用String的String表示形式Permission。底层的Authorization子系统实现通常将简单地将这些String值转换为Permission实例，然后仅调用相应的类型安全的方法。（Shiro的默认实现使用PermissionResolvers 对这些方法进行字符串到权限的转换 。）
>   > ```
>
> - com.apache.shiro.subject.support.DelegatingSubject
>
>   > `Subject`将方法调用委托给基础[`SecurityManager`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/mgt/SecurityManager.html)实例进行安全检查的接口的实现。它本质上是`SecurityManager`代理。

#### SecurityManager

> > SecurityManager管理*所有*用户的安全操作。它是Shiro体系结构的核心，并充当一种“伞”对象，引用了许多内部嵌套的安全组件，这些安全组件构成了一个对象图。但是，一旦配置了SecurityManager及其内部对象图，通常就不理会它，应用程序开发人员几乎将所有时间都花在Subject API上。
> >
> > 那么如何设置SecurityManager？好吧，这取决于您的应用程序环境。例如，Web应用程序通常将在web.xml中指定Shiro Servlet过滤器，这将设置SecurityManager实例。如果您运行的是独立应用程序，则需要以其他方式对其进行配置。但是有许多配置选项。
> >
>> 每个应用程序几乎总是有一个SecurityManager实例。它本质上是一个应用程序单例（尽管不必是*静态*单例）。像Shiro中的几乎所有东西一样，默认的SecurityManager实现是[POJO](http://en.wikipedia.org/wiki/Plain_Old_Java_Object)，并且可以使用任何与POJO兼容的配置机制-普通的Java代码，Spring XML，YAML，.properties和.ini文件等进行配置。基本上，任何能够实例化的东西可以使用类和与JavaBeans兼容的调用方法。

#### Realms

> Shiro的第三个也是最后一个核心概念是*领域*。领域充当Shiro与应用程序安全数据之间的“桥梁”或“连接器”。也就是说，当需要真正与安全性相关的数据（例如用户帐户）进行交互以执行身份验证（登录）和授权（访问控制）时，Shiro会从为应用程序配置的一个或多个Realms中查找许多此类内容。
>
> 从这个意义上说，领域本质上是特定于安全性的[DAO](http://en.wikipedia.org/wiki/Data_access_object)：它封装了数据源的连接详细信息，并根据需要使关联数据可用于Shiro。在配置Shiro时，您必须至少指定一个领域用于身份验证和/或授权。可以配置多个Realm，但至少需要一个。
>
> Shiro提供了开箱即用的领域，可以连接到许多安全数据源（又名目录），例如LDAP，关系数据库（JDBC），文本配置源（例如INI和属性文件）等。如果默认的Realms不能满足您的需求，那么您可以插入自己的Realm实现以表示自定义数据源。
>
> ```java
> package org.apache.shiro.realm;
> 
> import org.apache.shiro.authc.AuthenticationException;
> import org.apache.shiro.authc.AuthenticationInfo;
> import org.apache.shiro.authc.AuthenticationToken;
> 
> public interface Realm {
>     //这个name是Realm的唯一接口
>     String getName();
> 	//如果此领域希望验证给定实例表示的Subject，则返回true，否则 返回false。
>     boolean supports(AuthenticationToken var1);
> 	//返回指定令牌的帐户特定于身份验证的信息；如果无法根据令牌找到帐户，则返回null。
>     AuthenticationInfo getAuthenticationInfo(AuthenticationToken var1) throws AuthenticationException;
> }
> ```
>
> > - org.apache.shiro.realm.CachingRealm  //关于缓存的抽象类，实现Realm
> >
> > - org.apache.shiro.realm.AuthenticatingRealm  //继承CachingRealm  （抽象类）
> >
> >   - ```java
> >     protected abstract AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token)
> >         //从实现特定的数据源（RDBMS，LDAP等）中检索给定认证令牌的认证数据。用于身份的认证。
> >     ```
> >
> > - org.apache.shiro.realm.AuthorizingRealm  //继承AuthenticatingRealm  （抽象类）
> >
> >   - ```java
> >     protected abstract AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals)
> >         //从基础数据存储中检索给定主体的AuthorizationInfo。从此方法返回实例时，您可能要考虑使用的实例 SimpleAuthorizationInfo，因为在大多数情况下它是合适的。
> >     ```
> >
> >   > 下面的类都可以直接使用也可以继承之后进行修改。一般来说自己自己都是实现AuthorizingRealm来实现自己的realm
> >
> >   > - org.apache.shiro.realm.ldap.AbstractLdapRealm     //LDAP（Light Directory Access Portocol）是一个目录数据库
> >   >
> >   > - org.apache.shiro.realm.jdbc.JdbcRealm   //Jdbc  
> >   >
> >   >   > 允许通过JDBC调用进行身份验证和授权的领域。默认查询建议一个潜在的模式，该模式可用于检索用户的密码以进行身份验证，以及查询用户的角色和权限。可以通过设置领域的查询属性来覆盖默认查询。
> >   >   >
> >   >   > 如果身份验证和授权的默认实现无法处理您的架构，则可以将该类作为子类，并覆盖适当的方法。
> >   >
> >   > - org.apache.shiro.realm.SimpleAccountRealm   //简单的账号realm
> >
> > - org.apache.shiro.authc.credential.CredentialsMatcher   //**接口凭证匹配器** 
> >
> >   >  可以确定AuthenticationToken提供的凭据是否与系统中存储的相应帐户的凭据匹配。简单的直接比较可以很好地处理 [`SimpleCredentialsMatcher`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/authc/credential/SimpleCredentialsMatcher.html)。如果在将用户的凭据存储在领域之前（通常的做法）对用户的凭据进行哈希处理，请查看 [`HashedCredentialsMatcher`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/authc/credential/HashedCredentialsMatcher.html)实现，因为它们支持此方案。
> >   >
> >   > ```java
> >   > doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info)
> >   >     //true如果提供的令牌凭证与存储的帐户凭证匹配，false则返回； 否则返回。
> >   >     //token- AuthenticationToken身份验证尝试期间提交的
> >   > 	//info- AuthenticationInfo存储在系统中。
> >   > //可以通过在realm中的方法：来进行覆盖凭证匹配器
> >   >     public void setCredentialsMatcher(CredentialsMatcher credentialsMatcher)
> >   > ```
> >
> > - org.apache.shiro.authc.credential.SimpleCredentialsMatcher   //简单的凭证匹配器
> >
> > > > ```java
> > > > 支持对byte []，char []和String类型的凭证进行直接（普通）比较，如果参数与这些类型不匹配，则恢复为简单 Object.equals比较。
> > > > ```
> >
> > - org.apache.shiro.authc.credential.HashedCredentialsMatcher   //密码哈希处理
> >
> > > - 此类（及其子类）的功能如下：
> > >   1. Hash the `AuthenticationToken` credentials supplied by the user during their login.
> > >   2. Compare this hashed value directly with the `AuthenticationInfo` credentials stored in the system
> > >   3. 如果这两个值为[`equal`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/authc/credential/SimpleCredentialsMatcher.html#equals-java.lang.Object-java.lang.Object-)，则提交的凭据匹配，否则不匹配。
> > > - 加盐（salt）：
> > >   1. 由于简单哈希通常不足以保护安全的应用程序，因此此类还支持“迭代”和多个哈希迭代。请阅读这篇出色的 [Hashing Java文章，](http://www.owasp.org/index.php/Hashing_Java)以了解盐析和多次迭代以及为什么要使用它们。（注意第5节“为什么要加盐？”和第6节“加强对攻击者的攻击”）。我们还应该注意到这里所有的Shiro的哈希实现（例如，[`Md5Hash`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/crypto/hash/Md5Hash.html)， [`Sha1Hash`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/crypto/hash/Sha1Hash.html)等）的支持和盐腌通过重载构造多个哈希迭代。
> > > - 确保这项工作有效的最后一件事是您的`Realm`实现必须返回[SaltedAuthenticationInfo](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/SaltedAuthenticationInfo.html)实例，而不是普通实例`AuthenticationInfo`。该`SaltedAuthenticationInfo`界面确保您可以在创建用户帐户（例如`user.setPasswordSalt(salt);`上面的调用）时使用您所使用的盐`HashedCredentialsMatcher`。
> >
> > 

#### Authenticator(公共接口身份验证器)

> > 负责执行和用户反应，以验证（登录）的尝试的组件。当用户尝试登录时，该逻辑由执行`Authenticator`。该`Authenticator`知道如何与一个或多个协调`Realms`该商店相关的用户/帐户信息。从这些数据中获得的数据`Realms`用于验证用户的身份，以确保用户确实是他们所说的真实身份。
> >
> > - 它是Shiro API的主要入口点之一。
> > - <u>启用可插拔身份验证模块（PAM）行为（两阶段提交等）通常是通过单次`Authenticator`协调和与应用程序配置的[`Realm`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/realm/Realm.html)s 组交互来实现的。</u>
> >
> > - 大多数Shiro用户不会`Authenticator`**直接与实例进行交互**。Shiro的默认体系结构基于`SecurityManager`通常包装`Authenticator`实例的整体。
> >
> >   ```java
> >   AuthenticationInfo	authenticate(AuthenticationToken authenticationToken);
> >   //根据提交的验证用户身份AuthenticationToken。
> >   ```
>
> - org.apache.shiro.authc.pam.ModularRealmAuthenticator
>
>   - 如果仅配置一个领域（对于大多数应用程序通常是这种情况），那么身份验证成功自然仅取决于调用该领域的 [`Realm.getAuthenticationInfo(org.apache.shiro.authc.AuthenticationToken)`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/realm/Realm.html#getAuthenticationInfo-org.apache.shiro.authc.AuthenticationToken-)方法。
>
>   - 但是，如果配置了两个或多个领域，则可以通过遍历领域的集合并在身份验证尝试过程中与每个领域进行交互来实现PAM行为。由于这更加复杂，此身份验证器允许使用自定义行为来解释与多个领域进行交互时发生的情况-例如，您可能要求所有领域在尝试过程中都必须成功，或者也许至少一个必须成功，或者进行其他解释。可以使用来执行此自定义行为 [`AuthenticationStrategy`](https://shiro.apache.org/static/1.3.2/apidocs/org/apache/shiro/authc/pam/ModularRealmAuthenticator.html#setAuthenticationStrategy-org.apache.shiro.authc.pam.AuthenticationStrategy-)，您可以将其作为此类的属性插入。
>
>     ```java
>     //在ModularRealmAuthenticator中
>     public class ModularRealmAuthenticator extends AbstractAuthenticator {
>         private AuthenticationStrategy authenticationStrategy = new AtLeastOneSuccessfulStrategy();
>         
>         public void setAuthenticationStrategy(AuthenticationStrategy authenticationStrategy) {
>             this.authenticationStrategy = authenticationStrategy;
>         }
>         
>         //通过setAuthenticationStrategy来进行更改多realm的认证操作
>         //由于大多数多领域应用程序至少需要一个Realm成功进行身份验证，因此默认实现为AtLeastOneSuccessfulStrategy。
>         //三种方式在下方 AuthenticationStrategy
>     
>     ```
>
>     

#### Authorizer

> > 是部件负责确定用户在该应用程序的访问控制。它是最终表明是否允许用户做某事的机制。像一样`Authenticator`，`Authorizer`还知道如何与多个后端数据源进行协调以访问角色和权限信息。在`Authorizer`使用该信息来准确确定是否允许用户执行特定的操作。

#### Authentication

> > - 收集用户的标识信息（称为**principals**）和支持身份的*凭证*（称为**credentials**）。
> >
> > - Submit
> >
> > - 如果提交的凭据与系统对该用户身份（本金）的期望匹配，则认为该用户已通过身份验证。如果不匹配，则不认为用户已通过身份验证。

> > 身份验证是验证用户身份的过程。也就是说，当用户通过应用程序进行身份验证时，他们在证明自己实际上就是他们所说的身份。有时也称为“登录”。
>
> ```java
> AuthenticationToken token = new UsernamePasswordToken(username,password);
> token.setRememberMe(true);
> Subject currentUser =  SecurityUtils.getSubject();
> currentUser.login(token);
> ```
>
> 我们使用[UsernamePasswordToken](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/UsernamePasswordToken.html)，它支持最常见的用户名/密码身份验证方法。这是Shiro的[org.apache.shiro.authc.AuthenticationToken](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/AuthenticationToken.html)接口的实现，该接口是Shiro的身份验证系统用来表示提交的主体和凭据的基本接口。

##### Remembered vs. Authenticated

> > > `Remembered` *记住`Subject`的不是匿名的，并且具有已知的身份（即`subject.`[`getPrincipals()`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/subject/Subject.html#getPrincipals--)非空）。但是，在**先前的**会话期间，通过先前的身份验证会记住此身份。如果`subject.`[`isRemembered()`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/subject/Subject.html#isRemembered--)返回true，则认为该主题已被记住`true`。*
>> >
> > > `authenticated` *`Subject`（已**认证）**：**Authenticated**（已认证`login`）是*在Subject的当前会话期间*已成功认证（即，调用该方法而未引发异常）的认证。如果`subject.`[`isAuthenticated()`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/subject/Subject.html#isAuthenticated--)返回，则认为该主题已认证`true`。*
> > >
> > > `记住状态和已认证状态是互斥的-一个`true`值表示另一个`false`值，反之亦然。`
> > >
> > > > “认证”一词具有非常强烈的*证明*含义。也就是说，有一个预期*保证*了`Subject`已被证明他们是谁。
> > > >
>> > > 当仅通过与应用程序的先前交互来记住用户时，证明状态将不复存在：记住的身份使系统了解该用户可能是谁，但实际上，它无法绝对*保证*是否记住了该主题代表预期的用户。主题通过身份验证后，就不再认为它们仅被记住，因为它们的身份在当前会话中已经过验证。
> > > >
>> > > 因此，尽管应用程序的许多部分仍可以基于记住的原则（例如自定义视图）执行特定于用户的逻辑，但通常不应执行高度敏感的操作，直到用户通过执行成功的身份验证尝试合法地验证了其身份。
> > > >
>> > > 例如，检查是否`Subject`可以访问财务信息的检查几乎总是取决于`isAuthenticated()`，而不是`isRemembered()`，以保证期望的和经过验证的身份。
> > > >
> > > > `以下是一个相当常见的场景，有助于说明为什么记住和已认证之间的区别很重要。`
> > > >
> > > > 假设您正在使用[Amazon.com](https://www.amazon.com/)。您已成功登录，并已在购物车中添加了几本书。但是您必须参加会议，但忘记注销。会议结束时，该回家了，您离开办公室了。
> > > >
> > > > 第二天上班时，您发现自己还没有完成购买，因此回到amazon.com。这次，亚马逊“记住”您的身份，用名字向您打招呼，并仍然为您提供一些个性化的书本推荐。到亚马逊，`subject.isRemembered()`会回来的`true`。
> > > >
> > > > 但是，如果您尝试访问帐户以更新信用卡信息以购买图书，会发生什么情况？当亚马逊“记住”您（`isRemembered()`== `true`）时，它不能保证您实际上就是您（例如，某个同事正在使用您的计算机）。
> > > >
> > > > 因此，在您执行敏感操作（如更新信用卡信息）之前，亚马逊会强迫您登录，以便他们保证您的身份。登录后，您的身份已通过验证，并且到，`isAuthenticated()`现在为`true`。
> > > >
> > > > 这种情况在许多类型的应用程序中经常发生，因此该功能是Shiro内置的，因此您可以将其用于自己的应用程序。现在，是否使用`isRemembered()`或`isAuthenticated()`自定义视图和工作流取决于您自己，但是Shiro将保留此基本状态，以备不时之需。
> 
> 如您所见，Shiro的API轻松反映了通用的工作流程。您将继续把这种简单性作为主题操作的所有主题。当调用登录方法时，SecurityManager将接收AuthenticationToken并将其分发给一个或多个配置的领域，以允许每个领域根据需要执行身份验证检查。每个领域都可以根据需要对提交的AuthenticationToken做出反应。
> 
> ```java
> try{
>     currentUser.login(token);
>     currentUser.logout();
> }catch(IncorrectCredentialsException ice){
>     //不正确
> }catch(LockedAccountException lae){
>     
> }
> ...
>catch(AuthenticationException exception){
>     
>}
> ```
> 
> > 如果该`login`方法安静地返回，就可以了-我们完成了！在`Subject`已经通过认证。应用程序线程可以不间断地继续运行，对的所有后续调用`SecurityUtils.getSubject()`将返回经过身份验证的`Subject`实例，对的所有调用都`subject.` [`isAuthenticated()`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/subject/Subject.html#isAuthenticated--)将返回`true`。
> >
> > 当您调用时`logout`，任何现有的`Session`都将无效，并且所有身份都将被取消关联（例如，在Web应用程序中，RememberMe cookie也将被删除）。由于Web应用程序中记住的身份通常与cookie保持在一起，并且cookie只能在提交响应正文之前删除，因此强烈建议在调用后立即将最终用户重定向到新视图或页面`subject.logout()`。这保证了所有与安全性有关的cookie均会按预期删除。这是HTTP cookie的功能限制，而不是Shiro的限制。
> >
> > ![认证顺序](C:\Users\18354\Desktop\学习的记录\引用的图片\认证顺序shiro.jpg)
> >
> > ##### authenticator
> >
> > > Shiro `SecurityManager`实现默认使用[`ModularRealmAuthenticator`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/pam/ModularRealmAuthenticator.html)实例。它们`ModularRealmAuthenticator`同样支持具有单个领域以及具有多个领域的应用程序。
> >
> > > 在单一领域的应用程序中，`ModularRealmAuthenticator`将`Realm`直接调用单一领域。如果配置了两个或更多领域，它将使用一个`AuthenticationStrategy`实例来协调尝试的发生方式。我们将在下面介绍AuthenticationStrategies。

##### AuthenticationStrategy

> > > 当为一个应用程序配置两个或多个领域时，`ModularRealmAuthenticator`依赖于内部[`AuthenticationStrategy`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/pam/AuthenticationStrategy.html)组件来确定认证尝试成功或失败的条件。
>>>
> > > 例如，如果只有一个Realm认证`AuthenticationToken`成功，而其他所有认证都失败，那么认证尝试是否被视为成功？还是必须所有领域都成功进行身份验证才能将整体尝试视为成功？或者，如果某个领域成功通过身份验证，是否有必要进一步咨询其他领域？一种`AuthenticationStrategy`使基于应用程序的需要作出适当的决定。
> >>
> > > AuthenticationStrategy是一个无状态组件，在身份验证尝试期间会被查询4次（这4种交互所需的任何必要状态都将作为方法参数给出）
> >>
> > > 1. 在任何领域被调用之前
> >> 2. 在`getAuthenticationInfo`调用单个Realm 方法之前
>> > 3. 在`getAuthenticationInfo`调用单个Realm 方法之后
> >> 4. 在所有领域都被调用之后
>> >
> >> 此外，`AuthenticationStrategy`负责将每个成功Realm的结果汇总并“捆绑”成一个单一的[`AuthenticationInfo`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/AuthenticationInfo.html)表示形式。这个最终的聚合`AuthenticationInfo`实例是该`Authenticator`实例返回的内容，也是Shiro用来表示`Subject`的最终身份（又称为Principals）的东西。
>> >
> >> | `AuthenticationStrategy` 类                                  | 描述                                                         |
> > > | :----------------------------------------------------------- | :----------------------------------------------------------- |
> > > | [`AtLeastOneSuccessfulStrategy`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/pam/AtLeastOneSuccessfulStrategy.html) | 如果一个（或多个）领域成功认证，则整个尝试都被视为成功。如果没有成功进行身份验证，则尝试将失败。 |
> > > | [`FirstSuccessfulStrategy`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/pam/FirstSuccessfulStrategy.html) | 仅使用从第一个成功通过身份验证的领域返回的信息。所有其他领域将被忽略。如果没有成功通过身份验证，则尝试将失败。 |
> > > | [`AllSuccessfulStrategy`](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/authc/pam/AllSuccessfulStrategy.html) | 所有配置的领域都必须成功进行身份验证，才能将整体尝试视为成功。如果任何人未成功通过身份验证，则尝试将失败。 |

##### realm authentication order

> > > 
>>
> > 主题成功登录后，它们被认为已通过身份验证，通常您允许他们使用您的应用程序。但是，仅仅因为用户证明了自己的身份并不意味着他们可以在应用程序中做任何想做的事情。这就引出了下一个问题：Shiro如何启用授权。

#### Authorization

> > 授权本质上是访问控制-控制用户可以在应用程序中访问的内容（例如资源，网页等）。大多数用户通过使用角色和权限等概念来执行访问控制。也就是说，通常根据分配给他们的角色和/或权限，允许用户执行某项操作或不执行某项操作。然后，您的应用程序可以根据对这些角色和权限的检查来控制公开哪些功能。
> >
> > ```java
> > if ( subject.hasRole(“administrator”) ) {
> >     //show the ‘Create User’ button
> > } else {
> >     //grey-out the button?
> > } 
> > ```
> >
> > 权限检查是执行授权的另一种方法。如上例所示，检查角色存在一个重大缺陷：您不能在运行时添加或删除角色。您的代码使用角色名称进行了硬编码，因此，如果更改了角色名称和/或配置，则代码将被破坏！如果您需要能够在运行时更改角色的含义，或者根据需要添加或删除角色，则必须依赖其他内容。
> >
> > ```java
> > if ( subject.isPermitted(“user:create”) ) {
> >     //show the ‘Create User’ button
> > } else {
> >     //grey-out the button?
> > } 
> > ```
> >
> > “ user：create”字符串是遵循某些解析约定的权限字符串的示例。Shiro的WildcardPermission支持此约定。尽管不在本介绍文章的讨论范围之内，但是您会发现WildcardPermission在创建安全策略时可以非常灵活，甚至支持*实例级*访问控制之类的功能
> >
> > ```java
> > if ( subject.isPermitted(“user:delete:jsmith”) ) {
> >     //delete the ‘jsmith’ user
> > } else {
> >     //don’t delete ‘jsmith’
> > }
> > ```
> >
> > 此示例表明，如果需要，您可以控制甚至访问非常细致的实例级别。如果愿意，您甚至可以发明自己的权限语法。有关更多信息，请参见[Shiro权限](http://shiro.apache.org/permissions.html)文档。最后，与身份验证一样，上述调用最终也进入了SecurityManager，后者将咨询一个或多个Realms来做出访问控制决策。这使领域可以根据需要响应身份验证和授权操作。

#### Session Management([org.apache.shiro.session.mgt.SessionManager](http://shiro.apache.org/static/current/apidocs/org/apache/shiro/session/mgt/SessionManager.html))

> > *将`SessionManager`知道如何创建和管理用户`Session`生命周期，提供在所有环境中的用户强大的会话体验。这是安全框架领域中的一项独特功能-Shiro能够在任何环境中本地管理用户会话，即使没有Web / Servlet或EJB容器也可以。默认情况下，Shiro将使用现有的会话机制（例如Servlet容器）（如果可用），但是如果没有，例如在独立应用程序或非Web环境中，它将使用其内置的企业会话管理来提供相同的编程经验。的`SessionDAO`存在允许任何数据源被用来坚持的会议*
> >
> > `SessionDAO`执行`Session`持久代（CRUD）操作`SessionManager`。这允许将任何数据存储插入会话管理基础结构。
> >
> > Apache Shiro在安全框架领域提供了一些独特的功能：可在任何应用程序和任何体系结构层中使用的一致的Session API。也就是说，Shiro为任何应用程序启用了会话编程范例-从小型守护程序独立应用程序到最大的群集Web应用程序。这意味着希望使用会话的应用程序开发人员不再需要，则不再需要使用Servlet或EJB容器。果可用），但是如果没有，例如在独立应用程序或非Web环境中，它将使用其内置的企业会话管理来提供相同的编程经验。
> >
> > Shiro会话的另一个好处是，如果需要，会话数据可以在客户端技术之间共享。例如，如果需要，Swing桌面客户端可以参与相同的Web应用程序会话-如果最终用户同时使用这两者，则很有用。那么，您如何在任何环境中访问主题的会话？如下面的示例所示，有两种Subject方法。
> >
> > ```java
> > Session session = subject.getSession();
> > Session session = subject.getSession(boolean create);
> > ```
> >
> > 如您所见，这些方法在概念上与HttpServletRequest API相同。第一种方法将返回主题的现有会话，或者如果没有，则将创建一个新的会话并返回它。第二种方法接受一个布尔参数，该参数确定是否将创建一个新的会话（如果尚不存在）。一旦获得了主题的会话，就可以几乎像HttpSession一样使用它。Shiro团队认为HttpSession API最适合Java开发人员，因此我们保留了大部分感觉。当然，最大的区别是您可以在任何应用程序中使用Shiro Sessions，而不仅仅是Web应用程序。
> >
> > ```java
> > Session session = subject.getSession();
> > session.getAttribute("key", someValue);
> > Date start = session.getStartTimestamp();
> > Date timestamp = session.getLastAccessTime();
> > session.setTimeout(millis);
> > ```

##### [EHCache SessionDAO](http://shiro.apache.org/session-management.html#ehcache-sessiondao)

> 默认情况下，未启用EHCache，但是如果您不打算自己实施`SessionDAO`，则**强烈**建议您为Shiro的SessionManagement启用EHCache支持。EHCache SessionDAO将会话存储在内存中，如果内存受到限制，则支持向磁盘溢出。这对于生产应用程序非常需要，以确保您不会在运行时随机地“丢失”会话。

##### 会话验证和计划

##### Session Clustering

##### [EnterpriseCacheSessionDAO](http://shiro.apache.org/session-management.html#enterprisecachesessiondao)



#### Cryptography

> > 密码术是隐藏或混淆数据的过程，因此窥探眼睛无法理解它。Shiro的密码学目标是简化JDK的密码学并使之可用。
> >
> > 重要的是要注意，密码通常不是特定于Subjects 的，因此Shiro API的一个领域不是特定于 Subject-specific的。即使未使用“主题”，也可以在任何地方使用Shiro的加密支持。Shiro真正侧重于其加密支持的两个领域是加密哈希（又名消息摘要）和加密密码领域。

##### Hashing

> > 如果使用了JDK的[MessageDigest](http://download.oracle.com/javase/6/docs/api/java/security/MessageDigest.html)类，您很快就会意识到使用它有点麻烦。它具有笨拙的基于静态方法的基于工厂的API，而不是面向对象的API，因此您不得不捕获可能永远不需要捕获的已检查异常。如果需要十六进制编码或Base64编码的消息摘要输出，则由您自己决定-两者均不提供标准的JDK支持。Shiro通过干净直观的hashing API解决了这些问题。
> >
> > 例如，让我们考虑MD5-hashing 文件并确定该散列的十六进制值的相对常见的情况。称为“checksum”，在提供文件下载时经常使用它-用户可以对下载的文件执行自己的MD5hash，并断言其校验和与下载站点上的校验和匹配。如果它们匹配，则用户可以充分假设文件在传输过程中未被篡改。
> >
> > 1. 将文件转换为字节数组。JDK中没有任何东西可以帮助解决此问题，因此您需要创建一个帮助程序方法，该方法可以打开[FileInputStream](http://download.oracle.com/javase/6/docs/api/java/io/FileInputStream.html)，使用字节缓冲区并抛出适当的IOExceptions等。
> >2. 使用[MessageDigest](http://download.oracle.com/javase/6/docs/api/java/security/MessageDigest.html)类对字节数组进行哈希处理，以处理适当的异常，如下面的清单12所示。
> > 3. 将散列字节数组编码为十六进制字符。JDK中也没有任何东西可以帮助完成此任务，因此您将需要创建另一个帮助器方法，并且可能在实现中使用按位运算和移位。
> >
> > ```java
> >try {
> >     MessageDigest md = MessageDigest.getInstance("MD5");
> >     md.digest(bytes);
> >     byte[] hashed = md.digest();
> >} catch (NoSuchAlgorithmException e) {
> >     e.printStackTrace();
> > }
> > ```
> > 
> > 对于如此简单且相对常见的事情，这是大量的工作。现在，这就是**使用** Shiro 进行完全相同的操作的方法。
> > 
> > ```java
> > String hex = new Md5Hash(myFile).toHex(); 
> > ```
> >
> > 使用Shiro简化所有工作时，了解正在发生的事情非常简单和容易。SHA-512哈希和密码的Base64编码也很容易。
> >
> > ```java
> > String encodedPassword = new Sha512Hash(password, salt, count).toBase64();
> > ```

##### Ciphers

> > 密码是可以使用密钥可逆地转换数据的密码算法。我们使用它们来保护数据安全，尤其是在传输或存储数据时，尤其是在数据容易被撬开的时候。
> 
> > 如果您曾经使用过JDK密码API，尤其是javax.crypto.Cipher类，那么您就会知道，它是驯服极其复杂的野兽。对于初学者，每种可能的Cipher配置始终由javax.crypto.Cipher的实例表示。需要做公钥/私钥加密吗？您使用密码。是否需要使用分组密码进行流操作？您使用密码。是否需要创建AES 256位密码来保护数据？您使用密码。你明白了。
> 
> > Shiro试图通过引入其CipherService API来简化整个密码算法的概念。大多数开发人员在保护数据时都希望使用CipherService：一种简单，无状态，线程安全的API，可以在一个方法调用中完整地加密或解密数据。您所需要做的就是提供密钥，然后可以根据需要加密或解密。例如，可以使用256位AES加密，如下面的所示。
> 
> ```java
> AesCipherService cipherService = new AesCipherService();
> cipherService.setKeySize(256);
> //create a test key:
> byte[] testKey = cipherService.generateNewKey();
> 
> //encrypt a file’s bytes:
> byte[] encrypted =cipherService.encrypt(fileBytes, testKey);
> ```
> 
> 与JDK的Cipher API相比，Shiro示例更简单：
> 
> 1. 您可以直接实例化CipherService-没有奇怪或令人困惑的工厂方法。
> 2. 密码配置选项表示为与JavaBeans兼容的getter和setter-没有奇怪且难以理解的“转换字符串”。
> 3. 加密和解密在单个方法调用中执行。
> 4. 没有强制检查的异常。如果需要，请捕获Shiro的CryptoException。
> 
> Shiro的CipherService API还有其他好处，例如既支持基于字节数组的加密/解密（称为“块”操作）又支持基于流的加密/解密（例如，加密音频或视频）。

#### Web Support

> > Shiro随附了强大的Web支持模块，以帮助保护Web应用程序。为Web应用程序设置Shiro很简单。唯一需要做的就是在web.xml中定义Shiro Servlet过滤器。
> >
> > - org.apache.shiro.spring.web.ShiroFilterFactoryBean
> > 
> >      > ```java
> >      > public class ShiroFilterFactoryBean implements FactoryBean, BeanPostProcessor {
> >      >     //安全管理器
> >      >     private SecurityManager securityManager;
> >      >     //
> >      >     private Map<String, Filter> filters = new LinkedHashMap();
> >   >     private Map<String, String> filterChainDefinitionMap = new LinkedHashMap();
> >   >     private String loginUrl;
> >      >     private String successUrl;
> >      >     private String unauthorizedUrl;
> >   >     private AbstractShiroFilter instance;
> >   > ```
> >  >
> >   > 

#### Web Session Management

> > - Default HTTP session 
> >
> > 对于Web应用程序，Shiro缺省其会话基础结构使用我们都习惯的现有Servlet容器会话。也就是说，当您调用方法subject.getSession（）和subject.getSession（boolean）时，Shiro将返回由Servlet容器的HttpSession实例支持的Session实例。这种方法的优点在于，调用subject.getSession（）的业务层代码与Shiro Session实例进行交互-它不知道与基于Web的HttpSession对象一起工作。在跨架构层保持完全隔离时，这是一件非常好的事情。
> > 
> >    - shiro native session in the web tier 
> >    
> >    如果由于需要Shiro的企业会话功能（例如与容器无关的集群）而在Web应用程序中启用了Shiro的本机会话管理，那么您当然希望HttpServletRequest.getSession（）和HttpSession API与“本机”会话一起使用，并且而不是servlet容器会话。如果您必须重构任何使用HttpServletRequest和HttpSession API的代码来替代使用Shiro的Session API，那将非常令人沮丧。Shiro当然不会期望您这样做。相反，Shiro完全实现了Servlet规范的Session部分，以支持Web应用程序中的本机会话。这意味着无论何时调用相应的HttpServletRequest或HttpSession方法调用，Shiro都会将这些调用委派给其内部的本地Session API。
> 
> ### Additional Fealures
> 
> > 1. 线程和并发支持，用于跨线程维护主题（Executor和ExecutorService支持）
> > 2. 可调用和可运行支持将逻辑作为特定主题执行
> > 3. “运行方式”支持，用于假设另一个主题的身份（例如，在管理应用程序中很有用）
> > 4. 测试工具支持，使在单元测试和集成测试中对Shiro安全代码进行全面测试变得非常容易
> 
> ### Framework Limitations
> 
> > 尽我们所能，Apache Shiro并不是“银弹”-它不会轻松解决所有安全问题。Shiro无法解决的某些事情可能值得了解
> >
> > - Apache Shiro当前不处理虚拟机级别的安全性，例如基于访问控制策略阻止某些类加载到类加载器中的能力。但是，Shiro可以与现有的JVM安全操作集成在一起并不是不可想象的-只是没有人为该项目做出过这样的贡献。
> > - Shiro当前不支持“多阶段”身份验证，在这种身份验证中，用户可能通过一种机制登录，只是被要求然后使用另一种机制再次登录。这已在基于Shiro的应用程序中完成，但是该应用程序会先收集所有必需的信息，然后与Shiro进行交互。将来的Shiro版本中很可能会支持此功能。
> > - 当前，所有Realm实现都支持“读取”操作，以获取身份验证和授权数据以执行登录和访问控制。不支持“写入”操作，例如创建用户帐户，组和角色，或将用户与角色组和权限相关联。这是因为支持这些操作的数据模型在应用程序之间差异很大，并且很难在所有Shiro用户上强制执行“写入” API。

- @RequiresRoles()  shiro需要这个角色才可以访问