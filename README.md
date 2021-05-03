# Analyze-orthomosaic-images-from-agricultural-field-trials

## 1. Load library
```
library(devtools)
library(sp)
library(raster)
library(rgdal)
library(maptools)
library(scales)
library(xml2)
library(git2r)
library(usethis)
library(fftwtools)

devtools::install_github("filipematias23/FIELDimageR")
library(FIELDimageR)
```

## 2. Load image
```
EX1 <- stack("EX1_RGB.tif")
EX1
```
### 2.1 Plot RGB
```
plotRGB(EX1, r = 1, g = 2, b = 3)
```
![](EX1_RGB.png)<!-- -->

### 2.2 Crop image
```
EX1.Crop <- fieldCrop(mosaic = EX1)
```
![](EX1_Cropped.png)<!-- -->

## 3. Rotating the image
```
EX1.Rotated<-fieldRotate(mosaic = EX1.Crop, clockwise = F, h=F)
```
![](EX1_Rotated.png)<!-- -->

## 4. Removing soil using vegetation indices
```
EX1.RemSoil<- fieldMask(mosaic = EX1.Rotated, Red = 1, Green = 2, Blue = 3, index = "HUE")
```
![](EX1_RemSoil.png)<!-- -->

## 5. Building the plot shape file
```
EX1.Shape<-fieldShape(mosaic = EX1.RemSoil,ncols = 14, nrows = 9)
```
![](EX1_Shape.png)<!-- -->

## 6. Join information to shapefile
```
DataTable <- read.csv("DataTable.csv",header = T)  
fieldMap <- fieldMap(fieldPlot=DataTable$Plot, fieldColumn=DataTable$Row, fieldRow=DataTable$Range, decreasing=T)
fieldMap
```

```
EX1.Shape<-fieldShape(mosaic = EX1.RemSoil, ncols = 14, nrows = 9, fieldMap = fieldMap, fieldData = DataTable, ID = "Plot")

head(EX1.Shape$fieldShape@data)
```
![](Joined.PNG)<!-- -->

```
plotRGB(EX1.RemSoil$newMosaic)
plot(EX1.Shape$fieldShape,add=T)
```
![](grid.png)<!-- -->


## 7. Vegetation Indices

![](Vegetation_Indices.PNG)<!-- -->
```
EX1.Indices<- fieldIndex(mosaic = EX1.RemSoil$newMosaic, Red = 1, Green = 2, Blue = 3, 
                          index = c("NGRDI","BGI"), 
                          myIndex = c("(Red-Blue)/Green"))
```
![](NGRDI_BGI.png)<!-- -->

