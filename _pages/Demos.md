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


<div id="connectGDB"/>

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

<div id="openFeature"/>

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

<div id="displayFeatrue"/>

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

<div id="createTableField"/>

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

<div id="insertFeatrueRecord"/>

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

<div id="updateFeatureFieldValue"/>

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

<div id="drawTempLine"/>

### 绘制临时辅助线
```text

static INewLineFeedback pNewLineFeedback = null;
static INewPolygonFeedback pNewPolygonFeedback = null;
static INewEnvelopeFeedback pNewEnvelopFeedback = null;

/// <summary>
/// FBMouseDown,FBMouseMove,FBEnd三个函数完成鼠标跟踪效果
/// </summary>
/// <param name="type">种类：0代表NewLineFeedback,1代笔NewPolygonFeedback,2代表NewEnvelopFeedback</param>
/// <param name="mapCtrl"></param>
/// <param name="point">当前鼠标位置坐标</param>
public static void FBMouseDown(short type, MapControl mapCtrl, IPoint point)
{
    //this.MapCtrlMain.Object as MapControl
    if (type > 2 || type < 0)
        throw new Exception("类型错误!");
    if (point == null)
        throw new Exception("终止点不允许为空!");
    if (mapCtrl == null)
        throw new Exception("地图控件不允许为空!");
    

    if (type == 0)
    {
        if (pNewLineFeedback == null)
        {
            pNewLineFeedback = new NewLineFeedbackClass();
            //给NewLineFeedback赋颜色
            IRgbColor pRGB = new RgbColorClass();
            pRGB.Red = 255; pRGB.Blue = 0; pRGB.Green = 0;
            var pSlSymbol = pNewLineFeedback.Symbol as ISimpleLineSymbol;
            if (pSlSymbol != null)
            {
                pSlSymbol.Color = pRGB;
                pSlSymbol.Style = esriSimpleLineStyle.esriSLSSolid;
            }
            //pNewLineFeedback.Symbol.ROP2 = esriRasterOpCode.esriROPNotXOrPen;

            pNewLineFeedback.Display = mapCtrl.ActiveView.ScreenDisplay;
            pNewLineFeedback.Start(point);
        }
        else
        {
            pNewLineFeedback.AddPoint(point);

        }
    }
    else if (type == 1)
    {
        if (pNewPolygonFeedback == null)
        {
            pNewPolygonFeedback = new NewPolygonFeedbackClass();
            pNewPolygonFeedback.Display = mapCtrl.ActiveView.ScreenDisplay;
            pNewPolygonFeedback.Start(point);
        }
        else
        {
            pNewPolygonFeedback.AddPoint(point);
        }
    }
    else if (type == 2)
    {
        if (pNewEnvelopFeedback == null)
        {
            pNewEnvelopFeedback = new NewEnvelopeFeedbackClass();
            pNewEnvelopFeedback.Display = mapCtrl.ActiveView.ScreenDisplay;
            pNewEnvelopFeedback.Start(point);
        }
    }
}

/// <summary>
/// FBMouseDown,FBMouseMove,FBEnd三个函数完成鼠标跟踪效果
/// </summary>
/// <param name="type">种类：0代表NewLineFeedback,1代笔NewPolygonFeedback,2代表NewEnvelopFeedback</param>
/// <param name="point">当前鼠标位置世纪坐标</param>
public static void FBMouseMove(short type, IPoint point)
{
    if (type > 2 || type < 0)
        throw new Exception("类型错误!");
    if (point == null)
        throw new Exception("终止点不允许为空!");
    if (type == 0)
    {
        if (pNewLineFeedback != null)
            pNewLineFeedback.MoveTo(point);
    }
    else if (type == 1)
    {
        if (pNewPolygonFeedback != null)
            pNewPolygonFeedback.MoveTo(point);
    }
    else if (type == 2)
    {
        if (pNewEnvelopFeedback != null)
            pNewEnvelopFeedback.MoveTo(point);
    }
}

/// <summary>
/// FBMouseDown,FBMouseMove,FBEnd三个函数完成鼠标跟踪效果
/// </summary>
/// <param name="type">种类：0代表NewLineFeedback,1代笔NewPolygonFeedback,2代表NewEnvelopFeedback</param>
/// <param name="point">当前鼠标位置世纪坐标</param>
/// <returns>IGeometry对象</returns>
public static IGeometry FBEnd(short type, IPoint point)
{
    if (type > 2 || type < 0)
        throw new Exception("类型错误!");
    if (point == null)
        throw new Exception("终止点不允许为空!");
    IGeometry pGeo = default(IGeometry);
    if (type == 0)
    {
        if (pNewLineFeedback != null)
        {
            pNewLineFeedback.AddPoint(point);
            pGeo = pNewLineFeedback.Stop();
            pNewLineFeedback = default(INewLineFeedback);
        }
    }
    else if (type == 1)
    {
        if (pNewPolygonFeedback != null)
        {
            pGeo = pNewPolygonFeedback.Stop();
            pNewPolygonFeedback = default(INewPolygonFeedback);
        }
    }
    else if (type == 2)
    {
        if (pNewEnvelopFeedback != null)
        {
            pGeo = pNewEnvelopFeedback.Stop();
            pNewEnvelopFeedback = default(INewEnvelopeFeedback);
        }
    }
    return pGeo;
}

```
>引用的ArcMap程序集
>+ ESRI.ArcGIS.Controls
>+ ESRI.ArcGIS.Display

>相关接口类型说明  
>`1. `INewLineFeedback: Provides access to members that control the new line display feedback.  
>`2. `INewPolygonFeedback: Provides access to members that control the new polygon display feedback.  
>`3. `INewEnvelopeFeedback: Provides access to members that control the creation of a new envelope.  
>`4. `IRgbColor: Provides access to members that control the RGB color values.
>`5. `ISimpleLineSymbol: Provides access to members that control the simple line symbol.
>`6. `IGeometry: Provides access to members that describe properties and behavior of all geometric objects.
>`7. `IPoint: Provides access to members that define two dimensional points.
>`8. `IActiveView: Provides access to members that control the active view - the main application window.


>属性方法字段说明  
>INewLineFeedback
>+ ISymbol Symbol { get; set; } //The symbol the feedback object will use.
>+ IScreenDisplay Display { set; } //The display the feedback object will use.
>+ void Start(IPoint Point); //Begins a normal feedback at the given point.
>+ void AddPoint(IPoint Point); //Creates a node at the given point.
>+ void MoveTo(IPoint Point); //Move to the new point.
>+ IPolyline Stop(); //Stops the feedback and returns the shape.
>
>ISimpleLineSymbol
>+ IIColor Color { get; set; } //Line symbol color.
>+ esriSimpleLineStyle Style { get; set; } //The style of the line symbol.
>
>MapControl
>+ IActiveView ActiveView { get; } //The active view of the Map contained by the MapControl.
>
>IActiveView
>+ IScreenDisplay ScreenDisplay { get; } //The screen display used by the view.

<div id="searchElement"/>

### 查询图层满足条件的要素

```text
public void MapSearch(IFeatureLayer pLyr, IGeometry pGeometry, string whereClause)
{
    var pTopologicalOperator = pGeometry as ITopologicalOperator;
    if (pTopologicalOperator != null) pTopologicalOperator.Simplify();

    IQueryFilter pQuerFilter = null;
    ISpatialFilter pSFilter = new SpatialFilterClass();

    pSFilter.Geometry = pGeometry;
    if (!string.IsNullOrEmpty(whereClause))
    {
        pSFilter.WhereClause = whereClause;
    }

    pSFilter.SpatialRel =esriSpatialRelEnum.esriSpatialRelCrosses;
    pSFilter.GeometryField = pLyr.FeatureClass.ShapeFieldName;
    pQuerFilter = pSFilter;

    IFeatureCursor pFCursor = pLyr.FeatureClass.Search(pQuerFilter, false);
    IFeature pfeature = pFCursor.NextFeature();
    while (pfeature != null)
    {
        pfeature = pFCursor.NextFeature();
    }
}
```

>相关接口类型说明  
>`1. `ITopologicalOperator: Provides access to members for constructing new geometries based upon topological relationships between existing geometries.  
>`2. `IQueryFilter: Provides access to members that filter data based on attribute values and or relationships.  
>`3. `ISpatialFilter: Provides access to members that return and modify the type of spatial relationship that the filter will use.  
>`4. `IFeatureLayer: Provides access to members that control common aspects of a feature layer.


>属性方法字段说明  
>ITopologicalOperator
>+ void Simplify(); //Makes this geometry topologically correct.
>
>IFeatureLayer
>+ IFeatureClass FeatureClass { get; set; } //The layer's feature class.
> 
> esriSpatialRelEnum
> + esriSpatialRelEnum.esriSpatialRelCrosses //Query Geometry Crosses Target Geometry.
> 
>ISpatialFilter
>+ IGeometry Geometry { get; set; } //The query geometry used to filter results.
>+ string WhereClause { get; set; } //The where clause for the filter.
>+ esriSpatialRelEnum SpatialRel { get; set; } //The spatial relationship checked by the filter.
>+ string GeometryField { get; set; } //The name of the Geometry field to which the filter applies.
>
>IFeatureClass
>+ string ShapeFieldName { get; } //The name of the default sShape field.
>+ IFeatureCursor Search(IQueryFilter filter, bool Recycling); //Returns an object cursor that can be used to fetch feature objects selected by the specified query.



