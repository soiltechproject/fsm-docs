# Soiltech objects and methods (with doc blocs)

## Objects

#### GridInputFile

``` scala
/**
  * GridInputFile - For holding information related to the input file 
  * @param uid:String - A unique identifier 
  * @param metricType:String - The property that has been maeured
  * @param file:URL - the location of the file
  * @param fileType:MetricFormat.Value - can be Tiff, NetCDF or GDAL_VRT. eg use MetricFormat.Tiff
  * @param depth:Option[(Double,Double)] = None - Use if the input has a depth range (eg clay 10 to 30 cm)
  * @param isModel:Boolean = false - Use if the input is maddled and not meadured directly. @shaz perhaps this should be changed to isIndirect, or something
  * @param category:Option[String] = None - @shaz, not sure
  */

case class GridInputFile(uid:String, metricType:String, file:URL, fileType:MetricFormat.Value, depth:Option[(Double,Double)] = None, isModel:Boolean = false, category:Option[String] = None)
```


#### NDVIInputFile

```scala
/**
  * NDVIInputFile - Special input that holds information for calculating the NDVI from the red and nir bands
  * @param id:String
  * @param red:GridInputFile - contains the red band data
  * @param nir:GridInputFile - contains the near infrared band data 
  * @param bounds:Polygon - The boundary of the paddock @shaz not sure if this is needed as the paddock object also takes bounds 
  */
case class NDVIFile(id:String, red:MetricFile, nir:MetricFile, bounds:Polygon)
```

#### Paddock

```scala
/**
  * Paddock - For holding information about the paddock and fit the input data to the bounds and gird. Used for inputs into the Soiltech generators
  * @param GridFiles:Iterable[GridInputFile] - Array of all the GridInputFile(s) for the paddock
  * @param bounds:Polygon - The boundary of the paddock
  * @param soilPointDataArray:Iterable[PointDataset] - Array of PointDataset within the paddock bounds
  * @param ndviFile:Iterable[NDVIInputFile]=List() - Array of all the NDVIInputFile(s) for the paddock
  * @param id:Optional[String] = None - Optional identifier 
  */
case class Paddock(otherGridFiles:Iterable[MetricFile], bounds:Polygon, soilPointDataArray:Iterable[PointDataset],ndviFile:Iterable[NDVIFile],  = List(); id:Optional[String] = None)
```

#### PointAtribute


```scala
/**
  * PointAtribute - For holding information about a reading/ test sone to measure a particulat soil attribute  
  * @param attribute:String - name of the soil attribute. Either number or string depending on result (eg soil colour: "brown", pH: 8.3)
  * @param value:Any - associated value of the attribute
  * @param `type`:Option[String] = None - either string or number. @shaz not sure, was this going to be static or dynamic?
  * @param units:Option[String] = None - associated units of the value (eg %, mg/mol)
  * @param other:Option[String] = None - other description that may have come with the result 
  */
case class Result(attribute:String,value:Any,`type`:Option[String] = None, units:Option[String] = None,other:Option[String] = None)
```

#### PointDataset

```scala
/**
  * PointDataset 
  * @param 
  * @param 
  * @param 
  * @param 
  * @param 
  * @param 
  * @param 
  */
case class PointDataset(id:String,  alignedGridValues:Option[Map[String,Double]], attributes:Seq[PointAttribute], location:Coordinate, depthFromTo:(Double,Double), dateTaken:Date, paddockId:Option[String] = None)
```
