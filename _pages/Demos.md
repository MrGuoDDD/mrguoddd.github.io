---
layout: defaults/page
permalink: Demos.html
narrow: true
title: ArcMap使用示例
---

<div id="Authorization"/>

### 认证（Authorization）
```text
//ESRI.ArcGIS.System
//ESRI.ArcGIS.Version
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






