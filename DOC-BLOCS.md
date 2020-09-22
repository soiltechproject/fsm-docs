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

@shaz, should we include the dates in these?


#### NDVIInputFile

```scala
/**
  * NDVIInputFile - Special input that holds information for calculating the NDVI from the red and nir bands
  * @param id:String - identifier for the NDVIInputFile
  * @param red:GridInputFile - contains the red band data
  * @param nir:GridInputFile - contains the near infrared band data 
  * @param bounds:Polygon - The boundary of the paddock @shaz not sure if this is needed as the paddock object also takes bounds 
  */
case class NDVIFile(id:String, red:GridInputFile, nir:GridInputFile)
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
  * PointDataset - For holding information about a soil sample
  * @param id:String - identifier for the PointDataset
  * @param alignedGridValues:Option[Map[String,Double]] - The gridInput data that is closest to the PointDataset location. @shaz Should we make a formal method?
  * @param attributes:Seq[PointAttribute] - Array of PointAttribute(s) that were measured from this sample
  * @param location:Coordinate - @shaz should we change to (lat,lon) so we dont have to force the user to use import org.locationtech.jts.geom.Coordinate?
  * @param depthFromTo:(Double,Double) - The depth range starting from shallower to deeper
  * @param dateTaken:Date - The date that the sample was taken (java.util.Date)
  * @param paddockId:Option[String] = None - @shaz Is this relevent as this will be inputs into the paddock object
  */
case class PointDataset(id:String,  alignedGridValues:Option[Map[String,Double]], attributes:Seq[PointAttribute], location:Coordinate, depthFromTo:(Double,Double), dateTaken:Date, paddockId:Option[String] = None)
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

@shaz i noticed that the ds:Dataset input is missing from this one

~Talk about the method ie Paddock.getDataAsRDD~

---

## Methods

### Generators

The generators are the methods that generate outputs. Depening on the user defined setting, the generators will produce the corrisponding outputs.

Typically, generators take an Array of paddocks in their constructor.

The config object will define how the generator will run. Therefore, before running a generator, the user needs to build a corisponding config object.

#### SampleLocationGenerator


~Needs a bit of a refactor~

Config:

```scala
/**
  * SampleLocationConfig - 
  * @param zones number of KMeans zones to created
  * @param metrics set of the metrics (Dimensions) used in this process
  * @param subSample When set perform a stratified sub sampling on each of the zones
  */
case class SampleLocationGenerator(zones:Int,metrics:Seq[KMeansMetricConfig],subSample:Option[LHCConfig] = None,outputTiff:Boolean = false) 
```

Generator:

```scala
/**
  * SampleLocationGenerator
  * @param paddocks:Iterable[Paddock]
  * @param config:LocationGeneratorConfig
  * @param ds:Dataset
  */
SampleLocationGenerator(paddocks:Iterable[Paddock])
```

Methods:

```scala
/**
  * build
  * @param 
  */

```

#### DSMGenerator

Config:

```scala

/**
  * DSMGeneratorConfig
  * @param 
  * @param 
  * @param 
  */
case class DSMGeneratorConfig (sampleField:String,
                            samples:Option[Seq[SamplePoint]],
                            depths:List[(Double,Double)],
                            metrics:List[String],

                            // extra things to include in regression model
                            includeLatLong:Option[Boolean],
                            includePaddockId:Option[Boolean],

                           // user supplied range to use for results (colours on map)
                            clipRange:Option[(Double,Double)],
                           // remove samples that are outside of
                            filterExtremeValues:Option[Boolean],

                           // colour style
                            renderStyle:Option[String],

                           // mixing result with another map
                            mixRatio:Double = 0.5,
                            mixMetric:Option[String],

                           // krigin method (advanced)
                            krigMethod:Option[String],
                           // depths to project results to
                            splineDepths:Option[List[(Double,Double)]],
                            groupNearPaddocks:Option[Boolean],
                            sumProfile:Option[Boolean]
                           )
```

Generator:

```scala
/**
  * DSMGenerator
  * @param paddocks:Iterable[Paddock]
  */
DSMGenerator(paddocks:Iterable[Paddock])
```

Methods:

```scala
/**
  * build
  * @param config:DSMGeneratorConfig
  */

```

#### YLFGenerator

Config:

```scala
/**
  * YLFGeneratorConfig
  * @param 
  * @param 
  * @param 
  */
case class YLFGeneratorConfig(yieldField:String,
                             boundaryFields:List[String],
                             degrees:Option[Int],
                             filterMode:Option[String],
                             filterAmount:Option[Double],
                             subtractionField:Option[String],
                             regressionConfig:Option[RegressionConfig]
                            ) extends ProcessingConfig
```

Generator:

*WIP*

```scala
/**
  * DSMGenerator
  * @param paddocks:Iterable[Paddock]
  * @param config:DSMGeneratorConfig
  */
YLFGenerator(paddocks:Iterable[Paddock])

```

Methods:

```scala
/**
  * build
  * @param YLFGeneratorConfig
  * @returns ds: YLFResult[RDD,trendline:Array[Array[]],Array[Array[]]]
  */
YLFGenerator(paddocks:Iterable[Paddock]).buildYielfGap(config:YLFGeneratorConfig)

YLFGenerator(paddocks:Iterable[Paddock]).buildBLA(config:Array[YLFGeneratorConfig])
  * @returns ds: (RDD[Map[(lat,,lon),]], Legend)
```

#### ImageGenerator

Config

```scala

// EG defaults for AWC
input = "AWC"
colorGradient = gradient.awc
nSegments = 6 // if set to 0 output the continious
range = (5, 60)

LinearImageConfig(input, colorGradient, nSections = 7, range)

input = "YLF"
palette = palette.ylf(clay = "clay", awc = "AWC", ph = "pH")

CatorigoricalImageConfig(input, palette)

input = "U%"
symbolPack = symbolPacks.confidence
inverted = true

SymbolImageConfig(input, symbolPack, inverted)


clay = "clay"
sand = "sand"
textureClass = textureClass.
clayColorGradient = gradient.textureClay
sandColorGradient = gradient.textureSand
siltColorGradient = gradient.textureSilt


CatogoricalImageConfig(clay, sand, silt, clayColorGradient, sandColorGradient, siltColorGradient, textureClass, colorGradient)



// This is wherer @chris should have psrsets for generating image. also, png, jpg etc
ImageGeneratorConfig(Iterable[ImageConfig])
```

Generator:

```scala
/**
  * ImageGenerator
  * Responsible for rasterising and colouring the original metrics as well as generating statistics for the dataset
  * Async updates to individual paddocks are sent via @org.soiltech.Processor#statusReceiver
  * @param paddocks:Iterable[Paddock]
  * @param config:DSMGeneratorConfig
  * @param ds:Dataset
  */
ImageGenerator(paddocks:Iterable[Iterable[Paddock]])
```

Methods:

```scala
/**
  * build
  * @param config:ImageGeneratorConfig
  */

```

### Processors

#### DataProcessor

#### SampleAlignProcessor

#### KMeansProcessor

```python
def KMeansProcessor(kMeansInputRDD: RDD, numClusters: int):
```

#### CLHCProcessor

In python:

```python
def CLHCProcessor(clhcInputRDD: RDD, 
                numSamples: int, 
                existingSamples: SamplesList = [], 
                numIterations: int = 10, 
                excludeLocations: SamplesList = []) -> Tuple[float,List[Dict[str,float]],List[Dict[str,float]]]:
    """
    Recommends sample locations via the clhc method
    
    Attributes:
        clhcInputRDD -- RDD[Tuple[Tuple[float,float], Dict[str,float]]]
        numSamples -- The number of sample locations to genrate
        existingSamples -- List[Tuple[float,float]] a list of existing soil sample locations
        numIterations -- Number of times to iterate
        excludeLocations -- List[Tuple[LAT,LON]] Locations to exclure (not to choose)

    Returns:
        a list of lat, lon locations: List[Tuple[float, float]]
    """

```

#### PTFProcessor

AWC

#### SplineProcessor

#### krigingProcessor

#### RegressionProcessor

#### QMProcessor
