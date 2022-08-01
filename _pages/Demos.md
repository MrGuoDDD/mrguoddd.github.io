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

### 打开数据库某一要素数据

```text
public static IFeatureClass OpenDBData(IWorkspace workSpace,string tableName,string aliasName)
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
>`3. `IWorkspace2:Provides access to members that have information about the workspace.


### 显示数据库二维要素数据到指定图层

```text
public static void DisplayDBData(IFeatureClass pFeatureClass, IMap pMap)
{
    string sLyrName = "";
    double maxScale = 100;
    double minScale = 100;
    bool bVis = true;
    bool bSel = true;

    IFeatureLayer pFeatureLayer2D = null;
    if (pFeatureClass.FeatureType == ESRI.ArcGIS.Geodatabase.esriFeatureType.esriFTAnnotation)
    {
        pFeatureLayer2D = new FDOGraphicsLayerClass();
    }
    else if (pFeatureClass.FeatureType == ESRI.ArcGIS.Geodatabase.esriFeatureType.esriFTSimple)
    {
        pFeatureLayer2D = new FeatureLayerClass();
    }

    pFeatureLayer2D.FeatureClass = pFeatureClass;
    pFeatureLayer2D.Name = sLyrName;
    pFeatureLayer2D.MaximumScale = maxScale;
    pFeatureLayer2D.MinimumScale = minScale;
    pFeatureLayer2D.Visible = bVis;
    pFeatureLayer2D.Selectable = bSel;
    pFeatureLayer2D.ShowTips = true;
    pFeatureLayer2D.DisplayField = "ToolTip";

    pMap.AddLayer(pFeatureLayer2D);
}
```

>引用的ArcMap程序集
>+ ESRI.ArcGIS.Carto
>+ ESRI.ArcGIS.System
>+ ESRI.ArcGIS.Display

>相关接口类型说明  
>`1. `IFeatureLayer: Provides access to members that control common aspects of a feature layer.  

>属性方法字段说明  
>IFeatureClass
>+ esriFeatureType FeatureType { get; } //The type of features in this feature class.
>+ int FindField(string Name); //The index of the field with the specified name.
>
>IFeatureLayer
>+ IFeatureClass FeatureClass { get; set; } //The layer's feature class.
>+ string Name { get; set; } //Layer name.
>+ double MaximumScale { get; set; } //Maximum scale (representative fraction) at which the layer will display.
>+ double MinimumScale { get; set; } //Minimum scale (representative fraction) at which the layer will display.
>+ bool Visible { get; set; } //Indicates if the layer is currently visible.
>+ bool Selectable { get; set; } //Indicates if layer is selectable.
>+ bool ShowTips { get; set; } //Indicates if the layer shows map tips.
>+ string DisplayField { get; set; } //Primary display field.
>
>IMap  
>+ void AddLayer(ILayer Layer); //Adds a layer to the map.


### 创建要素表以及表字段

```text
public static IFeatureClass CreateDbTableData(IWorkspace workSpace)
{
    IFields pFields = new FieldsClass();

    string tableName = "";
    
    IFieldsEdit pFieldsEdit = pFields as IFieldsEdit;

    #region OID字段
    IField pField = new FieldClass();
    IFieldEdit pFieldEdit = pField as IFieldEdit;
    pFieldEdit.Name_2 = "OID";
    pFieldEdit.Type_2 = esriFieldType.esriFieldTypeOID;
    pFieldEdit.IsNullable_2 = false;
    pFieldsEdit.AddField(pField);
    #endregion

    #region Shape字段
    pField = new FieldClass();
    pFieldEdit = pField as IFieldEdit;

    IGeometryDef pGeometryDef = new GeometryDefClass();
    IGeometryDefEdit pGeometryDefEdit = pGeometryDef as IGeometryDefEdit;

    ISpatialReference pSpatialReference = new UnknownCoordinateSystemClass();
    pSpatialReference.SetDomain(pEnvelop.XMin - 0.00001, pEnvelop.XMax + 0.00001, pEnvelop.YMin - 0.00001, pEnvelop.YMax + 0.00001);
    pGeometryDefEdit.SpatialReference_2 = pSpatialReference;
    pGeometryDefEdit.GeometryType_2 = esriGeometryType.esriGeometryPolyline;

    pFieldEdit.Name_2 = "Shape";
    pFieldEdit.Type_2 = esriFieldType.esriFieldTypeGeometry;
    pFieldEdit.GeometryDef_2 = pGeometryDef;
    pFieldEdit.IsNullable_2 = true;
    pFieldsEdit.AddField(pField);
    #endregion

    #region 自定义属性
    pField = new FieldClass();
    pFieldEdit = pField as IFieldEdit;
    pFieldEdit.Name_2 = "自定义属性";
    pFieldEdit.Type_2 = esriFieldType.esriFieldTypeString;
    pFieldEdit.IsNullable_2 = true;
    pFieldsEdit.AddField(pField);
    #endregion
    
    IFeatureClass pFClass = null;
    
    var pFeatureWorkspace = workSpace as IFeatureWorkspace;

    pFClass = pFeatureWorkspace.CreateFeatureClass(tableName, pFields, null, null, esriFeatureType.esriFTSimple, "shape", "");

    return pFClass;
}  
```

>引用的ArcMap程序集
>+ Assembly ESRI.ArcGIS.Geometry

>相关接口类型说明  
>`1. `IField: Provides access to members that return information about the field.  
>`2. `IFields: Provides access to members that return information about the fields.  
>`3. `IFieldEdit: Provides access to members that edit the field properties.  
>`4. `IFieldsEdit: Provides access to members that modify a fields collection.  
>`5. `IGeometryDef: Provides access to members that return information about the geometry definition.  
>`6. `IGeometryDefEdit: Provides access to members that modify the geometry definition.  
>`7. `ISpatialReference: Provides access to members that control a SpatialReference.

>属性方法字段说明  
>IFeatureClass
>+ void AddField(IField Field); //Adds a field to this object class.
>
>ISpatialReference
>+ void SetDomain(double XMin, double XMax, double YMin, double YMax); //The XY domain extent.


### 向要素表里插入数据

```text
public static void InsertDb(IFeatureClass pFC)
{
    int index = 0;
    IFeature pFture = pFC.CreateFeature();

    IPoint pPoint = new PointClass();
    pPoint.PutCoords(0, 0);
    pFture.Shape = pPoint;
    
    index = pFture.Fields.FindField("物探点号");
    pFture.set_Value(index,"1");

    index = pFture.Fields.FindField("附属物");
    pFture.set_Value(index, "2");

    index = pFture.Fields.FindField("特征");
    pFture.set_Value(index, "3");

    pFture.Store();

}
```

>引用的ArcMap程序集
>+ Assembly ESRI.ArcGIS.Geometry

>属性方法字段说明  
>IFeature
>+ void Store(); //Stores the row.



### 更新要素自定义属性值

```text
public static void UpdateDbData(IFeatureClass pFeatureClass)
{
    IField pFld = new FieldClass();
    IFieldEdit pFldEdit = pFld as IFieldEdit;
    pFldEdit.Name_2 = "ToolTip";
    pFldEdit.Type_2 = esriFieldType.esriFieldTypeString;
    pFldEdit.Length_2 = 100;
    pFldEdit.IsNullable_2 = true;
    pFeatureClass.AddField(pFld);
    
    IFeatureCursor pCursor = pFeatureClass.Update(null, false);
    IFeature pFture = pCursor.NextFeature();

    while (pFture != null)
    {
        string tipText = string.Empty;
        int index = 0;

        index = pFture.Fields.FindField("物探点号");
        string code = pFture.get_Value(index).ToString();
        index = pFture.Fields.FindField("附属物");
        string addition = pFture.get_Value(index).ToString();
        index = pFture.Fields.FindField("特征");
        string charactor = pFture.get_Value(index).ToString();

        index = pFture.Fields.FindField("物探点号");
        pFture.set_Value(index, tipText);

        pCursor.UpdateFeature(pFture);
        pFture = pCursor.NextFeature();
    }
}
```

>引用的ArcMap程序集
>+ Assembly ESRI.ArcGIS.Geodatabase

>相关接口类型说明  
>`1. `IField: Provides access to members that return information about the field.  
>`2. `IFieldEdit: Provides access to members that edit the field properties.  
>`3. `IFeatureCursor: Provides access to members that hand out enumerated features, field collections and allows for the updating, deleting and inserting of features.  
>`4. `IFeature: Provides access to members that return and set properties of a feature.

>属性方法字段说明  
>IFeatureClass
>+ void AddField(IField Field); //Adds a field to this object class.
>+ IFeatureCursor Update(IQueryFilter filter, bool Recycling); //TReturns a cursor that can be used to update features selected by the specified query.
>
>IFeatureCursor
>+ IFeature NextFeature(); //Advance the position of the cursor by one and return the Feature object at that position.
>+ void UpdateFeature(IFeature Object); //Update the existing Feature in the database corresponding to the current position of the cursor.



