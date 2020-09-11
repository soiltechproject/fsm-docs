# Soiltech Usage Docs

## Inrto

~Talk about the basics~

---

## Core Usage

## Step 1: Create Your Paddocks 

Before creating your paddocks, you will need to prepare your datasets using the `GridInputFile` object. This may be done with any type of measurements such as elevation or gamma radiometric readings. 


```scala
import java.io.File
import org.soiltech.Dataset

val inputDir = Some(new File("path/to/input/dir"))
val nirInput = new File(inputDir.get, "nir.tif").toURI.toURL
val nirFile = GridInputFile(uid = “nir”, metricType = “ndvi”, file = nirInput, fileType = MetricFormat.Tiff)
```

Some soil observations, by nature, come with a depth range and will be needed to be taken into account when processing the data. This should be included by:


```scala
val inputDir = Some(new File("path/to/input/dir"))
val clayInput = new File(inputDir.get, "clay_10_30.tif").toURI.toURL
val clayFile_10_30 = GridInputFile(uid=“clay_10_30”, metricType=“clay”, file=clayInput, fileType=MetricFormat.Tiff, depth=(10,30), isModel=true)
```

Notice `isModel = true`. This is set to true if the input data is not directly measured. 

We've also included an `NDVIInputfile` for you to help group the data that is needed (*nir* and *red*) to calculate the *ndvi*. Create your *nir* and *red* GridInputFile's as you did above then:

```scala
import org.geotools.geojson.geom.GeometryJSON

val boundInput = new File("boundary.json")
val bounds  = new GeometryJSON().readPolygon(boundInput)

val ndviFile1 =  NDVIInputfile(id=boundInput.getName, red=redFile, nir=nirFile, bounds=bounds)
```

~need to talk about the boundary file~

Next we create a Paddock:

```scala
Paddock(otherGridFiles=[clayFile_10_30, clayFile_30_60], bounds=bounds, soilPointDataArray=Seq(), ndviFile=[ndviFile1])
```

Now we have created a paddock that we can perform some processing with. In this case, we did not include anything in our `soilPointDataArray`. We will learn how to prepare the `soilPointDataArray` in the next section.

### Step 2: Prepare the soilPointDataArray

### Step 3: SSLRGenerator

1. make config
2. use SSLRGenerator

### Step 4: DSMGenerator

### Step 5: YLFGenerator

### Step 6: ImageGenerator

---

## Advanced Usage

### ZoneProcessor

### CLHCProcessor
