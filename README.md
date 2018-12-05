# CoastSat

This software enables the users to extract time-series of shoreline change over the last 30+ years at their site of interest. The software is described in:

*Vos K., Splinter K.D., Harley M.D., Simmons J.A., Turner I.L. (submitted). CoastSat: a Google Earth Engine-enabled software to extract shorelines from publicly available satellite imagery, Environmental Modelling and Software*. 

There are two main steps:
- retrieval of the satellite images of the region of interest from Google Earth Engine
- extraction of the shorelines from the images using a sub-pixel resolution technique

### Description

Satellite remote sensing can provide low-cost long-term shoreline data capable of resolving the temporal scales of interest to coastal scientists and engineers at sites where no in-situ measurements are available. Satellite imagery spanning the last 30 years with constant revisit periods is publicly available and suitable to extract repeated measurements of the shoreline position.
CoastSat is an open-source Python module that allows to extract shorelines from Landsat 5, Landsat 7, Landsat 8 and Sentinel-2 images.
The shoreline detection algorithm implemented in CoastSat combines a sub-pixel border segmentation and an image classification component, which refines the segmentation into four distinct categories such that the shoreline detection is specific to the sand/water interface.

## 1. Installation

CoastSat requires the following Python packages to run: 
```
conda-forge: python=3.6 | matplotlib | scikit-image | scikit-learn | gdal | earthengine-api | oauth2client | spyder | jupyter | simplekml
PyPi:        shapely
```
If you are not a regular Python user and are not sure how to install these packages from *conda-forge* and *PyPi*, the section below shows how to install them step-by-step using Anaconda. Otherwise, install the packages and go directly to section **1.2 Activating Google Earth Engine Python API**.

### 1.1 Installing the packages (Anaconda)

If Anaconda is not already installed on your PC, you can get it at https://www.anaconda.com/download/.
Open the *Anaconda prompt* (in Mac and Linux, open a terminal window) and drive to the folder where you have downloaded/cloned this repository.

Create a new environment named *coastsat*: 

```
conda create -n coastsat
```

Activate the new environment:

```
conda activate coastsat
```

Now you need to populate the environment with the packages needed to run CoastSat. All the necessary packages are contained in three platform specific files: `requirements_win64.txt`, `requirements_osx64.txt`, `requirements_linux64.txt`. To install the packages, run one of the following commands, depending on which platform you are operating:

#### Windows 64 bits (win64)

```
conda install --name coastsat --file requirements_win64.txt
```

#### Mac 64 bits (osx64)

```
conda install --name coastsat --file requirements_osx64.txt
```

#### Linux 64 bits (linux64)

```
conda install --name coastsat --file requirements_linux64.txt
```

This might take a few minutes... once it is finished run the following command:

```
pip install shapely
```

All the packages have now been install in an environment called `coastsat`.

### 1.2 Activating Google Earth Engine Python API

Go to https://earthengine.google.com and sign up to Google Earth Engine.

![gee_capture](https://user-images.githubusercontent.com/7217258/49348457-a9271300-f6f9-11e8-8c0b-407383940e94.jpg)

Once you have created a Google Earth Engine account, go back to Anaconda and link your GEE credentials to the Python API:

```
earthengine authenticate
```

A web browser will open, login with your GEE credentials, accept the terms and conditions and copy the authorization code into the Anaconda terminal.

Now you are ready to start using the CoastSat!

**Note**: remeber to always activate the `coastsat` environment with `conda activate coastsat`

## 2. Usage 

An example of how to run the software in a Jupyter Notebook is provided in the repository (`example_jupyter.ipynb`). To run it, first activate your `coastsat` environment with `conda activate coastsat` (if not already active), and then type:

```
jupyter notebook
```

A web browser window will open, drive to the directory where you downloaded/cloned this repository and click on `example_jupyter.ipynb`.
The following sections guide the reader through the different functionalities of CoastSat with an example at Narrabeen beach (Australia).

### 2.1 Retrieval of the satellite images

To retrieve the satellite images cropped around the the region of interest from Google Earth Engine servers the following user-defined variables are needed:
- `polygon`: the coordinates of the region of interest (longitude/latitude pairs)
- `dates`: dates over which the images will be retrieved (e.g., `dates = ['2017-12-01', '2018-01-01']`)  
- `sat_list`: satellite missions to consider (e.g., `sat_list = ['L5', 'L7', 'L8', 'S2']` for Landsat 5, 7, 8 and Sentinel-2 collections).
- `sitename`: name of the site (defines the name of the subfolder where the files will be stored)

The call `metadata = SDS_download.retrieve_images(inputs)` will launch the retrieval of the images and store them as .TIF files (under **.data\sitename**). The metadata contains the exact time of acquisition (UTC) and geometric accuracy of each downloaded image and is saved as `metadata_sitename.pkl`. If the images have already been downloaded previously and the user only wants to run the shoreline detection, the metadata can be loaded directly from this file. The screenshot below shows an example where all the images of Narrabeen-Collaroy (Australia) acquired in December 2017 are retrieved. 

![retrieval](https://user-images.githubusercontent.com/7217258/49353105-0037e280-f710-11e8-9454-c03ce6116c54.PNG)

### 2.2 Shoreline detection

It is finally time to map the shoreline changes at your local beach!  

The following user-defined settings are required:

- `cloud_thresh`: threshold on maximum cloud cover that is acceptable on the images (value between 0 and 1)
- `output_epsg`: epsg code defining the spatial reference system of the shoreline coordinates
- `check_detection`: if set to `True` allows the user to quality control each shoreline detection

See http://spatialreference.org/ to find the EPSG number corresponding to your local coordinate system. If the user wants to quality control the mapped shorelines and manually validate each detection, the parameter `check_detection` should be set to `True`.
An example of settings is provided here:

![settings](https://user-images.githubusercontent.com/7217258/49354499-4b092880-f717-11e8-9877-135393011a48.PNG)

When `check_detection` is set to `True`, a figure like the one below appears and asks the user to manually accept/reject the detection by clicking on `keep` or `skip`.

![output](https://user-images.githubusercontent.com/7217258/49354698-39745080-f718-11e8-878d-266d850519f7.jpg)

Once all the shorelines have been mapped, the output is available in two different formats (saved under *.\data\sitename*):
- `sitename_output.pkl`: contains a list with the shoreline coordinates and the exact timestamp at which the image was captured (UTC time) as well as the geometric accuracy and the cloud cover of the image. The list can be manipulated with Python, a snippet of code to plot the results is provided in the main script.
- `sitename_output.kml`: this output can be visualised in a GIS software (e.g., QGIS, ArcGIS).

The figure below shows how the satellite-derived shorelines can be opened in GIS software using the `.kml` output.

![gis_output](https://user-images.githubusercontent.com/7217258/49361401-15bd0480-f730-11e8-88a8-a127f87ca64a.jpeg)

## Issues and Contributions

Having a problem or looking to contribute to the code? Please see the [Issues page](https://github.com/kvos/coastsat/issues).
