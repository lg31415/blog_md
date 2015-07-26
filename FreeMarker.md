title: "FreeMarker"
date: 2015-04-19 09:12:01
tags: 静态化
categories: 页面静态化
---

##Jar包
`freemarker-2.3.16.jar`
##FreeMarker数据源(java代码)
```java
//配置类  Freemarker类  ftl  freemarker template 
Configuration  conf = new Configuration();
// 模板 +  数据模型 = 输出
String path = "C:\\Users\\kai\\workspace\\freemarker\\ftl\\";
//模板位置
conf.setDirectoryForTemplateLoading(new File(path));

//文件夹下的哪个文件是模板   freemarker.html  模板对象
Template template = conf.getTemplate("freemarker.html");

//数据
Map root = new HashMap();

//实例
Writer out = new FileWriter(new File(path + "hello.html"));
template.process(root, out);
out.close();
```

## FreeMarker模板(语法)
``` html
<#list persons as person>
    ${person}
</#list>  <br/>

第一种写法
        Map mx = new HashMap();
        mx.put("fbb","范冰冰");
        mx.put("lbb","李冰冰");
        root.put("mx",mx); <br/>
<#list mx?keys as key>
    ${mx[key]}
</#list>
<br/>
第二种写法
${mx.fbb}/${mx.lbb}

<br/>

<!--        
        List<Map> maps = new ArrayList<Map>();
        Map pms1 = new HashMap();
        pms1.put("id1", "范冰冰");
        pms1.put("id2", "李冰冰");
        Map pms2 = new HashMap();
        pms2.put("id1", "曾志伟");
        pms2.put("id2", "何炅");
        maps.add(pms1);
        maps.add(pms2);
        root.put("maps", maps); -->
        
        <#list maps as map>
            ${map.id1}/${map.id2}
        </#list><br/>
        <#list maps as map>
            <#list map?keys as key>
                ${map[key]}
            </#list>
        </#list><br/>
        
        <#list persons as p>
            ${p_index}
        </#list><br/>
        
        <#assign x>世界太好了</#assign>
        ${x}
        
        <#assign x>
           <#list ["星期一", "星期二", "星期三", "星期四", "星期五", "星期六", "星期天"] as n>
              ${n}
           </#list>
        </#assign>
        ${x} <br/>
        
        <#list ["星期一", "星期二", "星期三", "星期四", "星期五", "星期六", "星期天"] as n>
            <#if n != "星期一">
                   ${n}
            </#if>
        </#list>
        <br/>
        
        <#list ["星期一", "星期二", "星期三", "星期四", "星期五", "星期六", "星期天"] as n>
            <#if n_index != 0>
               ${n}
            </#if>
        </#list> <br/>
        
        <#list ["星期一", "星期二", "星期三", "星期四", "星期五", "星期六", "星期天"] as n>
            <#if (n_index == 1) || (n_index == 3)>
                   ${n} --红色
            <#else>
                ${n} --绿色
            </#if>
        </#list>
        <br/>
        
        ${cur_time?datetime}
        
        
        <#macro table u>
            ${u} 
            <#if u == 8>
                世界你好
            </#if>
        </#macro>
        <@table u=8 /><br/>
        
        <#macro table u>
            ${u}
            <#nested/>
        </#macro>
        <@table u=8 >这是8</@table>
```

## FreeMarker结合Spring

###`freemarker.xml`
```xml
!-- Freemarker Service层配置 -->
    <bean id="staticPageService" class="cn.dishui.core.service.staticpage.StaticPageServiceImpl">
        <!-- springmvc 提供的 freemare配置类 -->
        <property name="freeMarkerConfigurer">
            <bean class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
                <!-- 希望Springmvc帮我解决相对路径的问题 -->
                <property name="templateLoaderPath" value="/WEB-INF/ftl/"/>
                <!-- 配置UTF-8 -->
                <property name="defaultEncoding" value="UTF-8"/>
            </bean>
        </property>
    </bean>
```