---
title: Azure API 管理策略表达式
description: 了解 Azure API 管理中的策略表达式。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/28/2017
ms.author: v-yiso
ms.date: 07/16/2018
ms.openlocfilehash: 6c01b86a06e49c3c391a4167d145cb0a570a7a08
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674158"
---
# <a name="api-management-policy-expressions"></a>API 管理策略表达式
本文讨论策略表达式语法 C# 7。 每个表达式都可以访问隐式提供的[上下文](api-management-policy-expressions.md#ContextVariables)变量以及允许的 .NET Framework 类型[子集](api-management-policy-expressions.md#CLRTypes)。  

更多相关信息：

- 了解如何向后端服务提供上下文信息。 使用[设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter)和[设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader)策略来提供此信息。
- 了解如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明预先授予操作访问权限。   
- 了解如何使用 [API 检查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)跟踪查看策略求值方法和这些求值的结果。  
- 了解如何对[从缓存获取](api-management-caching-policies.md#GetFromCache)和[存储到缓存](api-management-caching-policies.md#StoreToCache)策略使用表达式，以便配置 API 管理响应缓存。 设置持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。  
- 了解如何进行内容筛选。 删除使用[控制流](api-management-advanced-policies.md#choose)和[设置正文](api-management-transformation-policies.md#SetBody)策略从后端接收的响应中的数据元素。 
- 若要下载此视频中使用的策略语句，请参阅 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github 存储库。  
  
  
##  <a name="Syntax"></a> 语法  
 单一语句表达式括在 `@(expression)` 中，其中 `expression` 是格式正确的 C# 表达式语句。  
  
 多语句表达式括在 `@{expression}` 中。 多语句表达式中的所有代码路径必须以 `return` 语句结尾。  
  
##  <a name="PolicyExpressionsExamples"></a> 示例  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> 用法  
 在任何 API 管理[策略](api-management-policies.md)中，表达式都可用作属性值或文本值（除非策略引用另行指定）。  
  
> [!IMPORTANT]
>  使用策略表达式定义策略时，只能对策略表达式进行有限的验证。 在运行时，表达式由网关执行，策略表达式生成的任何异常将导致运行时错误。  
  
##  <a name="CLRTypes"></a> 策略表达式中允许的 .NET Framework 类型  
 下表列出了策略表达式中允许的 .NET Framework 类型及其成员。  
  
|CLR 类型|受支持的成员|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|全部|  
|Newtonsoft.Json.Linq.JArray|全部|  
|Newtonsoft.Json.Linq.JConstructor|全部|  
|Newtonsoft.Json.Linq.JContainer|全部|  
|Newtonsoft.Json.Linq.JObject|全部|  
|Newtonsoft.Json.Linq.JProperty|全部|  
|Newtonsoft.Json.Linq.JRaw|全部|  
|Newtonsoft.Json.Linq.JToken|全部|  
|Newtonsoft.Json.Linq.JTokenType|全部|  
|Newtonsoft.Json.Linq.JValue|全部|  
|System.Collections.Generic.IReadOnlyCollection<T\>|全部|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|全部|  
|System.Collections.Generic.ISet<TKey, TValue>|全部|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|键、值|  
|System.Collections.Generic.List<TKey, TValue>|全部|  
|System.Collections.Generic.Queue<TKey, TValue>|全部|  
|System.Collections.Generic.Stack<TKey, TValue>|全部|  
|System.Convert|全部|  
|System.DateTime|全部|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|全部|  
|System.Decimal|全部|  
|System.Double|全部|  
|System.Guid|全部|  
|System.IEnumerable<T\>|全部|  
|System.IEnumerator<T\>|全部|  
|System.Int16|全部|  
|System.Int32|全部|  
|System.Int64|全部|  
|System.Linq.Enumerable<T\>|全部|  
|System.Math|全部|  
|System.MidpointRounding|全部|
|System.Net.WebUtility|全部|
|System.Nullable<T\>|全部|  
|System.Random|全部|  
|System.SByte|全部|  
|System.Security.Cryptography. HMACSHA384|全部|  
|System.Security.Cryptography. HMACSHA512|全部|  
|System.Security.Cryptography.HashAlgorithm|全部|  
|System.Security.Cryptography.HMAC|全部|  
|System.Security.Cryptography.HMACMD5|全部|  
|System.Security.Cryptography.HMACSHA1|全部|  
|System.Security.Cryptography.HMACSHA256|全部|  
|System.Security.Cryptography.KeyedHashAlgorithm|全部|  
|System.Security.Cryptography.MD5|全部|  
|System.Security.Cryptography.RNGCryptoServiceProvider|全部|  
|System.Security.Cryptography.SHA1|全部|  
|System.Security.Cryptography.SHA1Managed|全部|  
|System.Security.Cryptography.SHA256|全部|  
|System.Security.Cryptography.SHA256Managed|全部|  
|System.Security.Cryptography.SHA384|全部|  
|System.Security.Cryptography.SHA384Managed|全部|  
|System.Security.Cryptography.SHA512|全部|  
|System.Security.Cryptography.SHA512Managed|全部|  
|System.Single|全部|  
|System.String|全部|  
|System.StringSplitOptions|全部|  
|System.Text.Encoding|全部|  
|System.Text.RegularExpressions.Capture|Index、Length、Value|  
|System.Text.RegularExpressions.CaptureCollection|Count、Item|  
|System.Text.RegularExpressions.Group|Captures、Success|  
|System.Text.RegularExpressions.GroupCollection|Count、Item|  
|System.Text.RegularExpressions.Match|Empty、Groups、Result|  
|System.Text.RegularExpressions.Regex|(Constructor)、IsMatch、Match、Matches、Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled、IgnoreCase、IgnorePatternWhitespace、Multiline、None、RightToLeft、Singleline|  
|System.TimeSpan|全部|  
|System.Tuple|全部|  
|System.UInt16|全部|  
|System.UInt32|全部|  
|System.UInt64|全部|  
|System.Uri|全部|  
|System.Xml.Linq.Extensions|全部|  
|System.Xml.Linq.XAttribute|全部|  
|System.Xml.Linq.XCData|全部|  
|System.Xml.Linq.XComment|全部|  
|System.Xml.Linq.XContainer|全部|  
|System.Xml.Linq.XDeclaration|全部|  
|System.Xml.Linq.XDocument|全部|  
|System.Xml.Linq.XDocumentType|全部|  
|System.Xml.Linq.XElement|全部|  
|System.Xml.Linq.XName|全部|  
|System.Xml.Linq.XNamespace|全部|  
|System.Xml.Linq.XNode|全部|  
|System.Xml.Linq.XNodeDocumentOrderComparer|全部|  
|System.Xml.Linq.XNodeEqualityComparer|全部|  
|System.Xml.Linq.XObject|全部|  
|System.Xml.Linq.XProcessingInstruction|全部|  
|System.Xml.Linq.XText|全部|  
|System.Xml.XmlNodeType|全部|  
  
##  <a name="ContextVariables"></a> 上下文变量  
 在每个策略[表达式](api-management-policy-expressions.md#Syntax)中均可隐式使用名为 `context` 的变量。 其成员提供与 `\request` 相关的信息。 所有 `context` 成员均为只读的。  
  
|上下文变量|允许的方法、属性和参数值|  
|----------------------|-------------------------------------------------------|  
|上下文|Api: IApi<br /><br /> 部署<br /><br /> Elapsed: TimeSpan - 时间戳值和当前时间之间的时间间隔<br /><br /> LastError<br /><br /> 操作<br /><br /> 产品<br /><br /> 请求<br /><br /> RequestId: Guid - 唯一请求标识符<br /><br /> 响应<br /><br /> 订阅<br /><br /> Timestamp: DateTime - 接收到请求的时间点<br /><br /> Tracing: bool - 指示跟踪是为打开还是关闭 <br /><br /> User<br /><br /> Variables: IReadOnlyDictionary<string, object><br /><br /> void Trace(message：string)|  
|context.Api|Id：string<br /><br /> IsCurrentRevision：bool<br /><br />  Name：string<br /><br /> Path：string<br /><br /> Revision: string<br /><br /> ServiceUrl：IUrl<br /><br /> Version: string |  
|context.Deployment|Region：string<br /><br /> ServiceName：string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|  
|context.LastError|Source：string<br /><br /> Reason：string<br /><br /> Message：string<br /><br /> Scope：string<br /><br /> Section：string<br /><br /> Path：string<br /><br /> PolicyId：string<br /><br /> 有关 context.LastError 的详细信息，请参阅[错误处理](api-management-error-handling-policies.md)。|  
|context.Operation|Id：string<br /><br /> Method: 字符串<br /><br /> Name：string<br /><br /> UrlTemplate：string|  
|context.Product|Apis：IEnumerable<IApi\><br /><br /> ApprovalRequired：bool<br /><br /> Groups：IEnumerable<IGroup\><br /><br /> Id：string<br /><br /> Name：string<br /><br /> State：enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit：int?<br /><br /> SubscriptionRequired：bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate：System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers：IReadOnlyDictionary<string, string[]><br /><br /> IpAddress：string<br /><br /> MatchedParameters：IReadOnlyDictionary<string, string><br /><br /> Method：string<br /><br /> OriginalUrl：IUrl<br /><br /> Url：IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName：string, defaultValue：string)|headerName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的请求标头值或 `defaultValue`。|  
|context.Response|Body：IMessageBody<br /><br /> Headers：IReadOnlyDictionary<string, string[]><br /><br /> StatusCode：int<br /><br /> StatusReason：string|  
|string context.Response.Headers.GetValueOrDefault(headerName：string, defaultValue：string)|headerName: 字符串<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的响应标头值或 `defaultValue`。|  
|context.Subscription|CreatedTime：DateTime<br /><br /> EndDate：DateTime?<br /><br /> Id：string<br /><br /> Key：string<br /><br /> Name：string<br /><br /> PrimaryKey：string<br /><br /> SecondaryKey：string<br /><br /> StartDate：DateTime?|  
|context.User|Email：string<br /><br /> FirstName：string<br /><br /> Groups：IEnumerable<IGroup\><br /><br /> Id：string<br /><br /> Identities：IEnumerable<IUserIdentity\><br /><br /> LastName：string<br /><br /> Note：string<br /><br /> RegistrationDate：DateTime|  
|IApi|Id：string<br /><br /> Name：string<br /><br /> Path：string<br /><br /> Protocols：IEnumerable<string\><br /><br /> ServiceUrl：IUrl<br /><br /> SubscriptionKeyParameterNames：ISubscriptionKeyParameterNames|  
|IGroup|Id：string<br /><br /> Name: 字符串|  
|IMessageBody|As<T\>(preserveContent：bool = false)：其中 T 为：string、JObject、JToken、JArray、XNode、XElement、XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Response.Body.As<T>` 方法用于以指定的类型 `T` 读取请求和响应消息正文。 该方法默认使用原始消息正文流，并在返回后将其呈现为不可用。 要通过让该方法在正文流的副本上执行操作而避免这种情况，请将 `preserveContent` 参数设置为 `true`。 请转到[此处](api-management-transformation-policies.md#SetBody)查看示例。|  
|IUrl|Host：string<br /><br /> Path：string<br /><br /> Port：int<br /><br /> Query：IReadOnlyDictionary<string, string[]><br /><br /> QueryString：string<br /><br /> Scheme：string|  
|IUserIdentity|Id：string<br /><br /> Provider：string|  
|ISubscriptionKeyParameterNames|Header：string<br /><br /> Query：string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName：string, defaultValue：string)|queryParameterName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到参数，则会返回逗号分隔的查询参数值或 `defaultValue`。|  
|T context.Variables.GetValueOrDefault<T\>(variableName：string, defaultValue：T)|variableName：string<br /><br /> defaultValue：T<br /><br /> 如果找不到变量，则会返回强制转换为 `T` 或 `defaultValue` 类型的变量值。<br /><br /> 如果指定的类型与已返回变量的实际类型不符，此方法会引发异常。|  
|BasicAuthCredentials AsBasic(input：this string)|input：string<br /><br /> 如果输入参数包含有效的 HTTP Basic Authentication 授权请求标头值，此方法会返回类型为 `BasicAuthCredentials` 的对象；否则，此方法会返回 null。|  
|bool TryParseBasic(input：this string, result：out BasicAuthCredentials)|input：string<br /><br /> result：out BasicAuthCredentials<br /><br /> 如果输入参数包含请求标头中的有效 HTTP Basic Authentication 授权值，此方法会返回 `true` 且结果参数会包含类型为 `BasicAuthCredentials` 的值；否则，此方法会返回 `false`。|  
|BasicAuthCredentials|Password：string<br /><br /> UserId：string|  
|Jwt AsJwt(input：this string)|input：string<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回类型为 `Jwt` 的对象；否则，此方法会返回 `null`。|  
|bool TryParseJwt(input：this string, result：out Jwt)|input：string<br /><br /> result：out Jwt<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回 `true` 且结果参数包含类型为 `Jwt` 的值；否则，此方法会返回 `false`。|  
|Jwt|Algorithm：string<br /><br /> Audience：IEnumerable<string\><br /><br /> Claims：IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime：DateTime?<br /><br /> Id：string<br /><br /> Issuer：string<br /><br /> NotBefore：DateTime?<br /><br /> Subject：string<br /><br /> Type：string|  
|string Jwt.Claims.GetValueOrDefault(claimName：string, defaultValue：string)|claimName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的声明值或 `defaultValue`。|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要解密的密文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 要解密的密文<br /><br />alg - 加密算法<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - input - 要解密的密文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policies.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   