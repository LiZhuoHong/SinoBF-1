# Satellite Mapping of Every Building’s Function in Urban China Reveals Deep Built Environment Diesparities
<!-- **Author: Zhuohong Li, Linxin Li, Ting Hu, Mofan Cheng, Wei He, Tong Qiu, Liangpei Zhang, and Hongyan Zhang** -->
<!-- ****Affiliation: Duke University, Wuhan University, Nanjing University of Information Science and Technology, and China University of Geosciences**** -->
In this study, we present a building-level functional map of China, covering 110 million individual buildings across 109 cities using 69 terabytes of multi-modal satellite imagery. The national-scale map is validated by government reports and 5,280,695 observation points, showing strong agreement with external benchmarks. This enables the first nationwide, multi-dimensional assessment of inequality in the built environment across city tiers, geographical regions, and intra-city zones.

<img src="https://github.com/LiZhuoHong/Paraformer/blob/main/Fig/Background.png" width="70%">

* This project contains the complete protocol for downloading the building-level map products and reproducing the (1) building-level mapping and (2) multi-dimensional built environment analysis process.

If you have any questions or requests, contact me at ashelee@whu.edu.cn (Zhuohong Li)
* [**My homepage**](https://lizhuohong.github.io/lzh/)
  
* This code should be run in a Python environment.

## The first building-level functional map of urban China
### The complete map product and user guide are released at: [**https://doi.org/10.5281/zenodo.17844789**](https://doi.org/10.5281/zenodo.17844789)
<img src="https://github.com/LiZhuoHong/Paraformer/blob/main/Fig/Study_area.png" width="70%">
This version of the data includes (1) Building-level functional maps of 109 Chinese cities, and (2) In-situ validation point sets. The building-level functional maps of 109 Chinese cities are organized in the ESRI Shapefile format, which includes five components: “.cpg”, “.dbf”, “.shx”, “.shp”, and “.prj” files. These components are stored in “.zip” files. Each city is named “G_P_C.zip,” where “G” explains the geographical region (south, central, east, north, northeast, northwest, and southwest of China) information, “P” explains the provincial administrative region information, and “C” explains the city name.

The original data sources required to create the map product and analyse the multi-dimensional built environment are as follows: [**The 1-meter Google Earth optical imagery**](https://earth.google.com), [**the 10-meter nighttime lights (SGDSAT-1)**](https://sdg.casearth.cn/en), and [**the building height data (CNBH-10m)**](https://zenodo.org/records/7827315). Labels were derived from: (1) Building footprint data, including [**the CN-OpenData**](https://doi.org/10.11888/Geogra.tpdc.271702) and [**the East Asia Building Dataset**](https://zenodo.org/records/8174931); and (2) Land use and AOI data used for constructing urban functional annotation are retrieved from [**the OpenStreetMap**](https://www.openstreetmap.org) and [**the EULUC-China dataset**](https://doi.org/10.1016/j.scib.2019.12.007). The first 1-meter resolution national-scale land-cover map used to conduct the accessibility analysis is available in our previous study: [**SinoLC-1**](https://doi.org/10.5281/zenodo.7707461). The housing inequality and infrastructure allocation analysis was conducted based on the 100-meter gridded population dataset from [**China's seventh census**](https://figshare.com/s/d9dd5f9bb1a7f4fd3734?file=43847643).
## Multi-dimensional Built Environment Analysis


<table>
  <thead>
    <tr>
      <th>Assessment dimensions</th>
      <th>Indicators</th>
      <th>Explainations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3">Urbanization intensity</td>
      <td>Building height</td>
      <td rowspan="3">The indicators in this dimension include building height, density, and nighttime light intensity. These are directly derived from external data sources: the building height data (CNBH-10m)(https://zenodo.org/records/7827315) and the 10-meter nighttime lights (SGDSAT-1)(https://sdg.casearth.cn/en).</td>
    </tr>
    <tr>
      <td>Building density</td>
    </tr>
    <tr>
      <td>Nighttime light intensity</td>
    </tr>
    <tr>
      <td rowspan="3">Facility accessibility</td>
      <td>Accessibility of healthcare resources</td>
      <td rowspan="3"> Accessibility to healthcare, education, and public service facilities, which are measured by calculating the average minimum travel time from residential buildings to each facility type. The corresponding processing scripts are available in the <code>./access_process</code> directory.</td>
    </tr>
    <tr>
      <td>Accessibility of education resources</td>
    </tr>
    <tr>
      <td>Accessibility of public service resources</td>
    </tr>
    <tr>
      <td rowspan="3">Infrastructure sufficiency</td>
      <td>Neighborhood amenity diversity</td>
      <td>The diversity and availability of amenities in a 15-minute neighborhood circle for
 each residence. The corresponding processing scripts are available in the <code>./availability_diversity_process</code> directory.</td>
    </tr>
    <tr>
      <td>Inequality in residential capacity allocation</td>
      <td rowspan="2">  The inequality in residential capacity allocation is quantified using the Gini coefficient. The indicator of per capita infrastructure occupation evaluates the relationship between infrastructure provision and population size. The processing scripts for both indicators are available in the <code>./inequal_allocation_process/result</code> directory.</td>
    </tr>
    <tr>
      <td>Per capita infrastructure occupation</td>
    </tr>
  </tbody>
</table>


* Please run "pip install -r `requirements_analysis.txt`" to install the dependencies before executing any Python file.
* Please download the [**example dataset (Jiaxing City)**](https://duke.box.com/s/hjnwgccyzo13ha4u4d82k6ye41qxo3sz) to `./input_data`
* The deployment guide for running these three parts is provided below.



### 01 Facility accessibility
The code for calculating the accessibility of healthcare, education, and public service facilities
* **To conduct the analysis, follow these steps:**

1. Assign speeds to the OSM road network and convert to raster
    ```bash
    python access_process/1_OSM_process.py

2. Assign speeds to land cover types  
    ```bash
    python access_process/2_LC_process.py

3. Merge land cover and road network to generate the cost raster  
    ```bash
   python access_process/3_merge.py

4. Clip relevant facility types (Healthcare, Educational, Public Services)
    ```bash
    python access_process/4_clip_buildingtype.py

5. Run Cost Distance Analysis with WhiteboxTools
    ```bash
    python access_process/whitebox_tools/cost_distance.py

6. Calculate travel time from residential buildings to the nearest target facility
    ```bash
    python access_process/6_access_stats.py

* **Result**  
The results are saved in the `./access_process/result`. Below is an example for the Educational facility:
```
access_process/
└── result/
    ├── accum_Educational.xlsx    – Histogram data of accessibility from each residential building in the example city. The end of the table includes the city's mean and median accessibility.
    ├── accum_Educational.tif     – Accessibility raster map of the example city.
    └── backlink_Educational.tif  – Backlink raster for tracing the shortest path from each building.
```

### 02 Neighborhood amenity diversity and availability 
The code for calculating the neighborhood amenity diversity and availability.
* **To conduct the analysis, follow these steps:**  
1. Count the number and types of buildings within each neighborhood amenity 
    ```bash
    python availability_diversity_process/1_nearby_buildings_stats.py
2. Calculate the availability and diversity for each neighborhood amenity 
    ```bash
    python availability_diversity_process/2_cal_availability_diversity.py
* **Result**  
The results are saved in the `./availability_diversity_process/result`:
```
availability_diversity_process/
└── result/
    ├── Jiaxing_availability_diversity.csv    – The availability and diversity of each neighborhood amenity, and the mean values of the whole city.
    └── Jiaxing_buildings.csv                 – The number and types of buildings within each neighborhood amenity.
```

### 03 Inequality in residential capacity allocation and per capita infrastructure occupation
The code for calculating the inequality in residential capacity allocation and per capita infrastructure occupation.

* **To conduct the analysis, follow these steps:**

1. Clip building height and population data to the extent of the example city  
    ```bash
    python inequal_allocation_process/1_raster_clip.py
2. Calculate building-level attributes  
    ```bash
    python inequal_allocation_process/2_cal_attribute.py
    ```
3. Compute inequality in residential capacity allocation and per capita infrastructure occupation based on the statistical results  
    ```bash
    python inequal_allocation_process/3_cal_inequality.py

* **Result**  
The results are saved in the `./inequal_allocation_process/result`:
```
inequal_allocation_process/
└── result/
    └── Jiaxing_inequality.csv  – The `Gini_coefficient` indicates the degree of inequality in the allocation of residential capacity, whereas the other columns represent the per capita infrastructure occupation across different building categories.
```

* **To reproduce the analysis on any 109 cities contained in this study:**

1. Download the building height map and building function map to the `./input_data` folder.
2. Run the command above by changing the city name in `./config.py`

## Building functional mapping
The mapping process contains segmentation and object classification parts that are shown below:
<img src="https://github.com/LiZhuoHong/Paraformer/blob/main/Fig/Building_function-mapping-l.png" width="70%">
### 01 Training Instructions of Semantic Segmentation Model (Paraformer)
We provide our original training lists for all 109 cities in the ` ./Building_mapping_01_semantic_segmentation/All_109_cities_trainlists/`  directory.
* Please run "pip install -r `requirements_segmentation.txt`" to install the dependencies before executing any Python file.  
* **To train and conduct the mapping with the Paraformer, follow these steps:**
  
1. Download the imagenet21k ViT pre-train model at [**Pre-train ViT**](https://drive.google.com/file/d/10Ao75MEBlZYADkrXE4YLg6VObvR0b2Dr/view?usp=sharing) and put it at ` ./Building_mapping_01_semantic_segmentation/networks/pre-train_model/imagenet21k` 
   
2. Taking the urban building mapping of Jiaxing City as an example, download the processed [**Training dataset**](https://duke.box.com/shared/static/y5f0w731z7lf2rozdjd77pj0vopeizg7.zip) (approximately 80GB per city) and unzip all the files to ` ./dataset/`.
   
3. Run the "Train" command to train the Paraformer on the example city:
   ```bash
   python train.py --dataset 'Building_mapping_sample_Jiaxing' --batch_size 10 --max_epochs 20 --savepath *save path of your folder* --gpu 0
4. After training, run the "Test" command to conduct the city-scale mapping:
   ```bash
   python test.py --dataset 'Building_mapping_sample_Jiaxing' --model_path *The path of trained .pth file* --save_path *To save the inferred results* --gpu 0
   
* **To train and test the framework on any 109 cities contained in this study:**

1. Edit the root storage direction of the train and test list (.csv).
2. Add your dataset_config in the "train.py" and "test.py" files.
3. Run the command above by changing the city name.

### 02 Post-processing Based on the Object Classification Model (Mask RCNN)  
* Please download the preprocessed training dataset to ` ./dataset/`(same as step 1).
* Please run "pip install -r `requirements_analysis.txt`" to install the dependencies before executing any Python file.  
* **To train and conduct the Post-processing, follow these steps:**

1. Vectorize the segmentation result and split the dataset by running the following command:
   ```bash
   python Building_mapping_02_post_processing/1_pipe_preprocess.py --city "Jiaxing" --input_tif " the segmentation result's save path"
2. Train and reclassify the unclassified buildings:
   ```bash
   CUDA_VISIBLE_DEVICES='your gpu id'  python Building_mapping_02_post_processing/Pytorch_Mask_RCNN-master/trainval.py --city "Jiaxing"
3. Padding the origin result:
   ```bash
   python Building_mapping_02_post_processing/3_pipe_postprocess.py --city "Jiaxing"
   
The final shapefile result will be saved in the `./Building_mapping_02_post_processing/result`
* An example of the input data and the produced results of building functional maps.
<img src="https://github.com/LiZhuoHong/Paraformer/blob/main/Fig/Mapping_result3.png" width="70%">
  





