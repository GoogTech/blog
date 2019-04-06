---
title: Simple Factory Pattern
date: 2019-04-03 12:29:16
tags: [Java,Design Pattern]
---

## 简单工厂模式结构与实现
### 简单工厂模式结构
1. `Factory`(工厂角色) : 工厂角色既工厂类,它是简单工厂模式的核心,负责实现创建所有产品实例的内部逻辑.工厂类还可以被外界直接调用,创建所需的产品对象.在工厂类中提供了静态的工厂方法`factoryMethod()`,它的返回类型为抽象产品类型`Product`.
2. `Product`(抽象产品角色) : 它是工厂类创建的所有对象的父类,封装了各种产品对象的公有方法,它的引入将提高系统的灵活性,使得在工厂类中只需定义一个通用的工厂方法,因为所有创建的具体产品对象都是其子类对象.
3. `ConcreteProduct`(具体产品角色) : 它是简单的工厂模式的创建目标,所有被创建的对象都充当这个角色的某个具体类的实例,每一个具体产品角色都继承了抽象产品的角色.需要实现在抽象产品中声明的抽象方法.
   

### 实例说明
```
某软件公司要基于Java语言开发一套图表库,该图表库可以为应用程序提供多种不同外观的图表,例如柱状图(HistogramChart),饼状图(PieChart)
折线图(LineChart)等..该软件公司图表库设计人员希望为应用系统人员提供一套灵活易用的图标库,通过设置不同的参数即可得到不同类型的图表.
而且可以较为方便的对图表库进行扩展,以便能够在将来增加一些新类型的图表.
```
#### 下述用简单工厂模式来设计该图表库
  
- *(1)Chart : 抽象图表接口,充当抽象产品类.*
```java
public interface Chart {

	public void display();
}
```

- *(2)HistogramChart : 柱状图类,充当具体产品类.*
```java
public class HistogramChart implements Chart {

	public HistogramChart() {
		System.out.println("Create the histogram chart .");
	}

	public void display() {
		System.out.println("Display the histogram chart .");
	}

}
```

- *(3)PieChart : 饼状体类,充当具体产品类.*
```java
public class PieChart implements Chart {

	public PieChart() {
		System.out.println("Create the pie chart .");
	}

	public void display() {
		System.out.println("Display the pie chart .");
	}

}
```

- *(4)LineChart : 折线图类,充当具体产品类.*
```java
public class LineChart implements Chart {

	public LineChart() {
		System.out.println("Create the line chart .");
	}

	public void display() {
		System.out.println("Display the line chart .");
	}

}
```

- *(5)ChartFactory : 图表工厂类,充当工厂类.*
```java
public class ChartFactory {

	public static Chart getChart(String chartType) {

		Chart chart = null;
		if (chartType.equalsIgnoreCase("histogram")) {
			chart = new HistogramChart();
			System.out.println("Initialize the histogram chart .");
		} else if (chartType.equalsIgnoreCase("pie")) {
			chart = new PieChart();
			System.out.println("Initialize the pie chart .");
		} else if (chartType.equalsIgnoreCase("line")) {
			chart = new LineChart();
			System.out.println("Initialize the line chart .");
		} else {
			System.err.println("error : Please chakout your object name. ");
		}
		return chart;
	}
}
```

- *(6)SimpleFactoryPatternTest : 客户端测试类*
```java
public class SimpleFactoryPatternTest {

	public static void main(String[] args) {

		Chart chart = ChartFactory.getChart("histogram");
		chart.display();
		Chart chart2 = ChartFactory.getChart("pie");
		chart2.display();
		Chart chart3 = ChartFactory.getChart("line");
		chart3.display();

		/*##The result be shown as followed:##

		 Create the histogram chart . 
          Initialize the histogram chart . 
          Display the histogram chart .
		  
		  Create the pie chart .  
		  Initialize the pie chart .
          Display the pie chart .
         
		  Create the line chart .  
          Initialize the line chart .
          Display the line chart .

		*/
	}
}

```

#### 不难发现,上述示例中在创建具体`Chart`对象时必须通过修改客户端代码中静态工厂方法的参数来更换具体产品对象.客户端代码需要重新编译,这对于客户端而言违反了`开闭原则`.下面介绍一种常用的解决方案,可以实现在不修改客户端代码的前提下能让客户端能够更换具体产品对象.

- *首先可以将静态的工厂方法的参数存储在`XML`格式的配置文件中(`config.xml`).如下所示 :*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<config>
	<chartType>histograma</chartType>
</config>
```

- *再通过一个工具类`XML Util`来读取配置文件中的字符串参数,`XMLUtil`类的代码如下 :*
```java
public class XMLUtil {

	private final static String path = "src\\objctConfig.xml";

	// 该方法用于从XML配置文件中提取图表类型,并返回类型名.
	public static String getChartType() {

		// 创建文档对象.
		DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
		try {
			DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
			Document document;
			document = documentBuilder.parse(new File(path));

			// 获取包含图标类型的文本节点.
			NodeList nodeList = document.getElementsByTagName("chartType");
			Node node = nodeList.item(0).getFirstChild();

			String chartType = node.getNodeValue().trim();
			return chartType;

		} catch (ParserConfigurationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SAXException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		return null;
	}
}
```

- **在引入配置文件和工具类`XMLUtil`之后,客户端代码`SimpleFactoryPatternTest.java`修改如下 :**
```java
public class SimpleFactoryPatternTest {

	public static void main(String[] args) {
		/*
		 * 通过读取配置文件中的参数来初始化指定的对象.
		 */
		String chartType = XMLUtil.getChartType();
		Chart chart = ChartFactory.getChart(chartType);

		chart.display();

		/*##The result be shown as followed:##

		   Create the histogram chart .  
           Initialize the histogram chart .
		   Display the histogram chart .

		 */
	}
}

```
- **不难发现,在上述客户端代码中并不包含任何与具体图标对象相关的信息,如果需要更换具体图标对象,只需要修改配置文件`config.xml`,无须修改任何原代码,既而符合`开闭原则`**
  
- *`开闭原则` : 是面向对象的可复用设计的第一块基石,它是最重要的面向对象设计原则.开闭原则由`Bertrand Meyer`于1998年提出,其定义如下 :*
```
开闭原则 : 软件实体应当对扩展开放,对修改关闭.
Open-Closed Principle(OCP) : Software entities should be open for extension,but closed for modification.

在开闭原则的定义中,软件实体可以指一个软件模块,一个由多个类组成的局部结构或一个独立的类,开闭原则就是指软件实体应尽量在不修改
原有代码的情况下进行扩展.
```


### 简单工厂模式优/缺点与适用环境

#### 简单工厂模式的优点
1. 工厂类包含必要的判断逻辑,可以决定在什么时候创建哪一个产品类的实例,客户端可以避免直接创建产品对象的职责.而仅仅'消费'产品,简单工厂模式实现了对象创建和使用的分离.
2. 客户端无须知道所创建的具体产品的类名.只需要知道具体产品类所对应的参数即可,对于一些复杂的类名,通过简单工厂模式可以在一定程度上减少使用者的记忆量.
3. 通过引入配置文件,可以在不修改任何客户端代码的情况下更换和增加新的具体产品类,在一定程序上提高了系统的灵活性.

#### 简单工厂模式的缺点
1. 由于工厂类集中了所有产品的创建逻辑,职责过重,一旦不能正常工作,整个系统都要受到影响.
2. 使用简单工厂模式势必会增加系统中类的个数(引入了新的工厂类),增加了系统的复杂度和理解难度.
3. 系统扩展困难,一旦添加新产品就不得不修改工厂逻辑,在新产品类型较多时有可能造成工厂逻辑过于复杂,不利于系统的扩展和维护.
4. 简单工厂模式由于使用了静态工厂方法,造成工厂角色无法形成基于继承的等级结构.

#### 简单工厂模式的适用环境
1. 工厂类负责创建的对象比较少,由于创建的对象比较少,不会造成工厂方法中的业务逻辑过于复杂.
2. 客户端只知道传入工厂类的参数,对于如何创建对象并不关心.


- *参考书籍《 Java设计模式 》 作者: 刘伟 (可以说本书的代码示例写的非常用心,很适合初学者参考)*