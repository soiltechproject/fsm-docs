# Soiltech objects and methods (with doc blocs)

## Objects

#### GridInputFile

``` scala
/**
  * A representation of a sample, this would be something with a lat/long and also a depth from and depth to.
  * @param uid:String - A unique identifier 
  * @param metricType:String
  * @param file:URL
  * @param fileType:MetricFormat.Value
  * @param depth:Option[(Double,Double)] = None
  * @param isModel:Boolean = false
  * @param depth:Option[(Double,Double)] = None
  * @return category:Option[String] = None
  */

case class GridInputFile(uid:String, metricType:String, file:URL, fileType:MetricFormat.Value, depth:Option[(Double,Double)] = None, isModel:Boolean = false, category:Option[String] = None)
```

