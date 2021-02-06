# Wan Framework
Wan是一款解决业务动态建模的数据存储与业务逻辑处理框架，业务对象通过XML文件进行描述，可在运行时实现发布与变更；每个业务模型绑定一个ActionScope（业务处理单元），ActionScope除了提供基础的增删改查方法外，还可注册扩展方法满足不同业务场景的需要；当业务场景涉足多个ActionScope时，框架能支持事务串联，实现事务的统一提交或回滚。

# 模型配置
## 主结构
```xml
<?xml version="1.0" encoding="utf-8"?>

<model name="wanConfig" zh-CN="Wan配置示例">
    <include>
        <item path="./client.xml" />
    </include>
    <config>
        <item key="actionScope" value="Wan.ModelScope.ActionScope, Wan"/>
    </config>
    <dataSource>    
        <storeSource />
        <viewSource />
        <sqlSource />
    </dataSource>
</model>
```

### model 根节点

|  属性  |  描述  | 
| ---- | ---- | 
| name | 标识，应用内唯一，标识需与xml文件名相同 | 
| zh-CN | 多语言标识的名称 | 

### model 子节点

| 节点 | 必须 | 描述 | 
| --- | --- | --- | 
| include | False |  引入其他业务模型 | 
| config | False | 前端模型配置参数 | 
| dataSource | True | 模型数据源，目前支持三类 storeSource（存储模型） / viewSource (对象查询模型) / sqlSource(SQL查询模型) |

## dataSource/storeSource (存储模型)

系统检测到 storeSource 模型时会自动根据描述创建或更新对应的数据库表

```XML
<storeSource name="demoBillEntry" tableName="t_model_demobillentry" aliasName="t1" whereBase="t1.FIsDelete=0" orderBy="t1.FOrder" zh-CN="示例表单表体">
    <columns>
        <column name="FRowID" rawName="t1.FRowID" dataType="System.String" dbType="varchar" length="255" zh-CN="主键" isPrimaryKey="True" defaultValue="{GUID}"/>
        <column name="FIsDelete" dataType="System.Boolean" dbType="int" defaultValue="0"/>
        <column name="FRowVersion" rawName="t1.FRowVersion" dataType="System.Int64" dbType="bigint"/>
        <column name="FBillID" rawName="t1.FBillID" isNotNull="True" zh-CN="表头主键"/>
        <column name="FOrder" dataType="System.Int32" dbType="int" zh-CN="行号" isUnique="True" uniqueGroup="FBillID"/>
        <column name="FMaterialName" isNotNull="True" zh-CN="物料名称" isIndex="True"/>
    </columns>
</storeSource>
<storeSource name="demoBillHead" tableName="t_model_demobill" aliasName="t1" whereBase="t1.FIsDelete=0" orderBy="t1.FCreateTime desc">
    <join>
        <item relation="left join" dataSource="client" aliasName="mClient" condition="t1.FClientID=mClient.FRowID"/>
    </join>
    <columns>
        <column name="FRowID" rawName="t1.FRowID" dataType="System.String" dbType="varchar" length="255" zh-CN="主键" isPrimaryKey="True" defaultValue="{GUID}"/>
        <column name="FIsDelete" dataType="System.Boolean" dbType="int" defaultValue="0"/>
        <column name="FRowVersion" rawName="t1.FRowVersion" dataType="System.Int64" dbType="bigint"/>
        <column name="FBillNumber" zh-CN="单据号" />
        <column name="FClientID" />
        <column name="FClientNumber" rawName="mClient.FNumber" zh-CN="客户编码"/>
        <column name="FClientName" rawName="mClient.FName" zh-CN="客户名称"/>
        <column name="FCreateTime" dataType="System.DateTime" defaultValue="{NOW}" zh-CN="创建时间"/>
    </columns>
    <childSources>
        <childSource dataSource="demoBillEntry">
            <relation>
                <item parentKey="FRowID" foreignKey="FBillID" />
            </relation>
        </childSource>
    </childSources>
</storeSource>
```

### dataSource/viewSource (对象查询模型)
```XML
<viewSource name="demoBillListView" tableName="t_model_demobillentry" aliasName="t1" whereBase="t1.FIsDelete=0 AND t2.FIsDelete=0" orderBy="t2.FCreateTime desc">
    <join>
        <item relation="inner join" dataSource="demoBillHead" aliasName="sDemoBillHead" condition="t1.FBillID=sDemoBillHead.FRowID"/>            
        <item relation="left join" tableName="t_model_client" aliasName="mClient" condition="sDemoBillHead.FClientID=mClient.FRowID"/>
    </join>
    <columns>
        <column name="FRowID" rawName="t1.FRowID"/>
        <column name="FOrder" dataType="System.Int32" zh-CN="行号"/>
        <column name="FBillNumber" zh-CN="单据号"/>
        <column name="FClientID" />
        <column name="FClientNumber" rawName="mClient.FNumber" zh-CN="客户编码"/>
        <column name="FClientName" rawName="mClient.FName" zh-CN="客户名称"/>
        <column name="FMaterialName" zh-CN="物料名称"/>                
    </columns>
</viewSource>
```

### dataSource/sqlSource (SQL查询模型)
```XML
<sqlSource name="demoBillListView2" aliasName="t1">
    <selectCommand>
        <![CDATA[
        select * from t_model_demobill
        ]]>
    </selectCommand>
    <columns>
        <column name="FRowID"/>
        <column name="FBillNumber" zh-CN="单据号" />
    </columns>
</sqlSource>
```

## WanMode


## WanObject / WanObjectList

## ActionScope
