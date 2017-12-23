---
title: Spring代码实现读取properties值
date: 2016-07-20 17:35:42
tags: spring properties
---

读取properties这个工作基本都是项目必备。本人惯用的框架是spring，如果是bean的话，那就很简单，在xml中配置下，然后用@value取值，蛮方便的。但是很多时候，我们需要编程方式取得Spring上下文的Properties。废话不多说下面开始。

-------------------

实现的方法有很多种，我这里@value的读取方式我就不详说了，网上一大把。主要讲解下用编码方式实现读取properties。
### 方法一：
利用JDK 内置的java.util.Properties 类为我们操作properties 文件提供了便利。纯粹的Java语言来读取properties，任何的Java项目都可以使用。我通过静态块，当类加载的时候就运行读取properties文件，并一一读取里面的内容，并暴露出去。代码如下：
``` Java
public class PropertiesUtils {

	private static Properties properties;  
	
	static {
		properties = new Properties();
		String fileName = "config/qiniuConfig.properties";
		InputStream in = PropertiesUtils.class.getClassLoader().getResourceAsStream(fileName);
		try {
			properties.load(in);
		} catch (IOException e) {
			throw new ExceptionInInitializerError(e);
		}
	}
	
	/**
	 * 根据key，获取到相对应的Propertie。
	 * @param key
	 * @return
	 */
	public static Object getContextPropertie(String key){
		return properties.get(key);
	}

}
```

### 方法二
用什么方式来获取properties了，最方便的当然是直接读取文件，方法如上。但是程序用了Spring加载的properties的话，那我们的何不在原本的基础上做得更好。

在spring读取properties的xml配置中,如下：

``` Xml
<!-- 加载配置属性文件 -->
	<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="locations">
            <list>
                <!-- 标准配置 -->
                <value>classpath*:*.properties</value>
                 <!-- 本地开发环境配置 -->
                <value>file:/d:/mindconf/platform/*.properties</value>
                <!-- 服务器生产环境配置 -->
                <value>file:/etc/mindconf/platform/*.properties</value>
            </list>
        </property>
	</bean>
```
从上面代码可以看得出来PropertyPlaceholderConfigurer是承担properties读取任务的类。如果熟悉spring源码的同学肯定知道，spring启动的时候会调用处理器的postProcessBeanFactory()方法。
```
public abstract class PropertyResourceConfigurer extends PropertiesLoaderSupport
		implements BeanFactoryPostProcessor, PriorityOrdered {

	......

	/**
	 * {@linkplain #mergeProperties Merge}, {@linkplain #convertProperties convert} and
	 * {@linkplain #processProperties process} properties against the given bean factory.
	 * @throws BeanInitializationException if any properties cannot be loaded
	 */
	@Override
	public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
		try {
			......
			// Let the subclass process the properties.
			processProperties(beanFactory, mergedProps);
		}
		catch (IOException ex) {
			throw new BeanInitializationException("Could not load properties", ex);
		}
	}
	......
```
![PropertyPlaceholderConfigurer](http://img.blog.csdn.net/20160721182055874)
通过上面的图片和源代码，基本可以知道，当项目启动的时候将会调用processProperties(beanFactory, mergedProps);这个方法。有兴趣的同学可以看下源代码，我也是刚开始接触而已。

所以第二方法就从这里找到突破口（其实上面都是废话啦，哈哈），下面通过自建类继承PropertyPlaceholderConfigurer，通过重写processProperties方法把properties暴露出去了。
```Java
public class CustomPropertyConfigurer extends PropertyPlaceholderConfigurer {
	
	private static Properties properties;  

	@Override
	protected void processProperties(ConfigurableListableBeanFactory beanFactoryToProcess, Properties props)
			throws BeansException {
		super.processProperties(beanFactoryToProcess, props);
		// 把properties暴露出去
		properties = props;
	}
	
	/**
	 * 根据key，获取到相对应的Propertie。
	 * @param key
	 * @return
	 */
	public static Object getContextPropertie(String key){
		return properties.get(key);
	}
}
```
当然还有一步，我们需要把配置文件xml中的配置改为重写这个类。
``` Xml
<!-- 加载配置属性文件 -->
<bean class="com.fourwenwen.demo.CustomPropertyConfigurer">
	<property name="locations">
           <list>
               <value>classpath*:*.properties</value>
           </list>
       </property>
</bean>
```
本次博客就写到这里了。本人渣渣一枚，程序员的眼中都是追求完美的，如果写的不好或者写错了，请大神们体谅体谅，提出来，小弟尽快改进，谢谢。
如果你有更好的方法和建议，欢迎大家互相讨论。

