---
layout: defaults/page
permalink: Demos.html
narrow: true
title: ArcMap使用示例
---

<div id="Authorization"/>

### 认证（Authorization）

```text
public static bool AuthorizationSetting()
{
    RuntimeManager.Bind(ProductCode.EngineOrDesktop);
    AoInitialize aoi = new AoInitializeClass();
    esriLicenseProductCode productCode = esriLicenseProductCode.esriLicenseProductCodeEngine;
    if (aoi.IsProductCodeAvailable(productCode) == esriLicenseStatus.esriLicenseAvailable)
    {
       aoi.Initialize(productCode);
       return true;
    }
    else
    {
        return false;
    }
}
```

>引用的ArcMap程序集
>+ ESRI.ArcGIS.System
>+ ESRI.ArcGIS.Version

>注：在使用ArcMap任何接口之前需要使用以上方法进行注册


### 连接个人空间数据库（GDB）

```text
public static IWorkspace ConnectAccessDataBaseRun()
{
    string file = Environment.CurrentDirectory + "\\北京HY_3DNew.mdb";
    IWorkspaceFactory pWorkspaceFactory = new AccessWorkspaceFactoryClass();
    IWorkspace pWorkSpace = pWorkspaceFactory.OpenFromFile(file, 0);
    return pWorkSpace;
}
```

>引用的ArcMap程序集
>+ ESRI.ArcGIS.DataSourcesGDB(AccessWorkspaceFactoryClass)
>+ ESRI.ArcGIS.Geodatabase(IWorkspaceFactory、IWorkspace)

>相关接口类型说明  
>`1. `IWorkspaceFactory:Provides access to members that create and open workspaces and supply workspace factory information.  
>`2. `AccessWorkspaceFactoryClass:Esri Access Workspace Factory.  
>`3. `IWorkspace:Provides access to members that have information about the workspace.

### 读取数据库某一要素数据

```text
public static IFeatureClass ReadDBData(IWorkspace workSpace,string tableName,string aliasName)
{
    IFeatureWorkspace pFeatureWorkspace = workSpace as IFeatureWorkspace;
    IWorkspace2 pWorkspace2 = workSpace as IWorkspace2;

    if (!pWorkspace2.get_NameExists(esriDatasetType.esriDTFeatureClass, tableName)) return null;

    IFeatureClass pFeatureClass = pFeatureWorkspace.OpenFeatureClass(tableName);

    return pFeatureClass;
}
```


>引用的ArcMap程序集
>+ ESRI.ArcGIS.Geodatabase

>相关接口类型说明  
>`1. `IFeatureClass:Provides access to members that control the behavior and properties of a feature class.  
>`2. `IFeatureWorkspace:Provides access to members that create and open various types of datasets and other workspace level objects.  







