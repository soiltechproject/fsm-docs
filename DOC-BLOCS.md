# Soiltech objects and methods (with doc blocs)

## Objects

#### GridInputFile

``` scala
/**
  * GridInputFile - For holding information related to the input file 
  * @param uid:String - A unique identifier 
  * @param metricType:String
  * @param file:URL
  * @param fileType:MetricFormat.Value
  * @param depth:Option[(Double,Double)] = None
  * @param isModel:Boolean = false
  * @param category:Option[String] = None
  */

case class GridInputFile(uid:String, metricType:String, file:URL, fileType:MetricFormat.Value, depth:Option[(Double,Double)] = None, isModel:Boolean = false, category:Option[String] = None)
```


#### NDVIInputFile

```
/**
  * NDVIInputFile - Special input that holds information for calculating the NDVI from the red and nir bands
  * @param id:String
  * @param red:GridInputFile - contains the red band data
  * @param nir:GridInputFile - contains the near infrared band data 
  * @param bounds:Polygon - The boundary of the paddock @shaz not sure if this is needed as the paddock object also takes bounds 
  */
case class NDVIFile(id:String, red:MetricFile, nir:MetricFile, bounds:Polygon)
```
