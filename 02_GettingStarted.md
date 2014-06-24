#QAFE Book

![qafelogo](http://www.qafe.com/wp-content/themes/qafe2013/img/logo.png)

## 2. Getting Started with QAFE

### 2.1 Setting up the Development Environment
QAFE Development is done in Eclipse. For the purpose of having a quickstart, we’ve provided an installer which installs Eclipse (Based on Oracle Enterprise Pack for Eclipse, OEPE 12, based on Eclipse 4.3) with all the necessary plug-ins. This installers are to be found on http://www.qafe.com/download


#### 2.1.1 Hardware requirements

To run QAFE the hardware requirements depends significantly on the number of users, applications expected and on the maximum number of required concurrent requests that the system will experience during peak hours. In addition, more CPU power, HD or memory will be needed as more QAFE components are added to the system configuration.

| Scenario      | CPU  | Memory  |Harddisk |
|---------------|------|---------|---------|
| QAFE Tutorial | Any modern CPU| 2 Gb |1,7 Gb |
| Developing in QAML| Any modern CPU | 1,5 Gb |50 Mb Per render engine (GWT, etc.) this amount of diskspace is used. |
| Running QAFE apps | Depending on the chosen server | 2 Gb | 50 Mb Per render engine (GWT, etc.) this amount of diskspace is used |

#### 2.1.2 Software requirements


| Scenario |Operating System |Java Virtual Machine |Web/App server |Database |
|----------|-----------------|---------------------|---------------|---------|
|QAFE Tutorial | Compatible with JVM |JDK7 or higher |Cross-platform |Oracle XE|
|Developing in QAML |Compatible with JVM |JDK7 or higher |Cross-platform |Optional|
|Running QAFE apps| Compatible with JVM |JDK7 or higher |Cross-platform|Optional|
