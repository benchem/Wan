# Wan Framework
Wan是一款基于XML描述的数据处理框架，它基于XML文件描述驱动，结合ActionScope处理单元简化数据事务处理。

## Mode XML Config
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
            <joinModel>
                <item relation="left join" dataSource="client" aliasName="mClient" condition="t1.FClientID=mClient.FRowID"/>
            </joinModel>
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
        <viewSource name="demoBillListView" tableName="t_model_demobillentry" aliasName="t1" whereBase="t1.FIsDelete=0 AND t2.FIsDelete=0" orderBy="t2.FCreateTime desc">
            <joinModel>
                <item relation="inner join" dataSource="demoBillHead" aliasName="t2" condition="t1.FBillID=t2.FRowID"/>
            </joinModel>
            <join>
                <item relation="left join" tableName="t_model_client" aliasName="mClient" condition="t2.FClientID=mClient.FRowID"/>
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
    </dataSource>
</model>
```

## WanMode

## WanObject / WanObjectList

## ActionScope
