---
title: IDEA Settings
entitle: IDEA-Settings
tags: [IDEA]
categories:
  - IDEA
date: 2019-04-14 22:51:07
---
记录IDEA的配置。
<!--more-->

## Plugins

CodeGlance
FindBugs
Grep Console
GsonFormat
Key Promoter X
Lombok
RestfulToolkit
String Manipulation
Translation
Free Mybatis plugin
Mybatis Log Plugin
## Keymap
name|Short cut
:-|:-
Toggle Full Screen mode|Ctrl + Alt + `
## Live Tempkates
```xml
<template name="&lt;assert&gt;" value="Assert.isTrue(Objects.nonNull(), &quot;&quot;);" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;column&gt;" value="@Column(name = &quot;&quot;)" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;debug&gt;" value="if(log.isDebugEnabled()){&#10;    log.debug(&quot;$class$- $method$:{}&quot;,);&#10;}&#10;" description="" toReformat="false" toShortenFQNames="true">
  <variable name="class" expression="className()" defaultValue="" alwaysStopAt="true" />
  <variable name="method" expression="methodName()" defaultValue="" alwaysStopAt="true" />
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;getMapping&gt;" value="@GetMapping(&quot;/&quot;)&#10;public ResponseEntity  method(){&#10;&#10;    return ResponseEntity.ok(new BaseEntity&lt;&gt;());&#10;}" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;gson&gt;" value="private static Gson gson = new Gson();" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;info&gt;" value="log.info(&quot;$class$- $method$:{}&quot;,);" description="" toReformat="false" toShortenFQNames="true">
  <variable name="class" expression="className()" defaultValue="" alwaysStopAt="true" />
  <variable name="method" expression="methodName()" defaultValue="" alwaysStopAt="true" />
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;log&gt;" value="private static final Logger log = LoggerFactory.getLogger($className$.class);" description="" toReformat="false" toShortenFQNames="true">
  <variable name="className" expression="className()" defaultValue="" alwaysStopAt="true" />
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;logAgson&gt;" value="private static final Logger log = LoggerFactory.getLogger($className$.class);&#10;&#10;private static Gson gson = new Gson();" description="" toReformat="false" toShortenFQNames="true">
  <variable name="className" expression="className()" defaultValue="" alwaysStopAt="true" />
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;omap&gt;" value="Map&lt;String,Object&gt; param = new HashMap&lt;&gt;();" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;pf&gt;" value="private final " description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;postMapping&gt;" value="@PostMapping(&quot;/&quot;)&#10;public ResponseEntity  method(){&#10;&#10;    return ResponseEntity.ok(new BaseEntity&lt;&gt;());&#10;}" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
<template name="&lt;smap&gt;" value="Map&lt;String,String&gt; param =  new HashMap&lt;&gt;();" description="" toReformat="false" toShortenFQNames="true">
  <context>
    <option name="JAVA_CODE" value="true" />
  </context>
</template>
```
## File and Code Templates
### File Header
```
/**
* ${PROJECT_NAME}: ${NAME}
*
* @author NoPainAnymore
* @version 1.0.0, ${YEAR}-${MONTH}-${DAY} ${HOUR}:${MINUTE}
* @since 1.0.0, ${YEAR}-${MONTH}-${DAY} ${HOUR}:${MINUTE}
*/
```
