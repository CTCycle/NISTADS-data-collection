# NISTADS: NIST/ARPA-E dataset composer

## 1. Project Overview
NISTADS is a python application developed to extract adsorption isotherms data from the NIST/ARPA-E Database of Novel and Emerging Adsorbent Materials (https://adsorption.nist.gov/index.php#home) through their dedicated API. The user can either collect data regarding adsorbent materials and adsorbate species or fetch adsorption isotherm experimental data. Experiments are identified by name upon building the entire database experiments index from the API endpoint. Furthermore, NISTADS exploits the PUG REST API (see https://pubchempy.readthedocs.io/en/latest/ for more information) to enrich the adsorbate species dataset with molecular properties (such as molecular weight, canonical smiles, complexity, heavy atoms, etc.). Eventually, the adsorption isotherm dataset is split into two datasets, one containing data on single component adsorption and the other including experiments with binary mixtures.

## 2. Adsorption datasets
The collected data is saved locally in 4 different .csv files, located in the `data\` folder. Adsorption isotherm datasets are saved in `data\experiments`, while the adsorbents and adsorbates datasets are saved into `data\materials`. The former will include the experiments datasets for both single component and binary mixture measurements, while the latter will host datasets on guest and host species. 

### 2.1 Data preprocessing data
The data undergoes preprocessing via a tailored pipeline, which initially filters out undesired experiments. These include experiments featuring negative values for temperature, pressure, and uptake, or those falling outside predefined boundaries for pressure and uptake ranges (refer to configurations for specifics). Pressure and uptakes are standardized to a uniform unit—Pascal for pressure and mol/g for uptakes. Following this refinement, the physicochemical attributes of the absorbate species are unearthed through the PUBCHEM API. This enriches the input data with molecular properties such as molecular weight, the count of heavy atoms, covalent units, and H-donor/acceptor statistics. Subsequently, features pertaining to experiment conditions (e.g., temperature) and adsorbate species (physicochemical properties) are normalized. Names of adsorbents and adsorbates are encoded into integer indices for subsequent vectorization by the designated embedding head of the model. Pressure and uptake series are also normalized, utilizing upper boundaries as the normalization ceiling. Additionally, initial zero measurements in pressure and uptakes series are removed to mitigate potential bias towards zero values. Finally, all sequences are reshaped to have the same length using post-padding with a specified padding value (defaulting to -1 to avoid conflicts with actual values) and then normalized.

## 2. Installation 
First, ensure that you have Python 3.10.12 installed on your system. Then, you can easily install the required Python packages using the provided requirements.txt file:

`pip install -r requirements.txt` 

## 3. How to use
The project is organized into subfolders, each dedicated to specific tasks. The `utils/` folder houses crucial components utilized by various scripts. It's critical to avoid modifying these files, as doing so could compromise the overall integrity and functionality of the program.

**Data:** run `compose_experiments_dataset.py` or `compose_materials_dataset.py` to respectively fetch data for adsorption experiments or for the guest/host entries. The data collection operation may take long time due to the large number of queries to perform, and it heavily depends on your internet connection performance (more than 30k experiments are available as of now). You can select a fraction of data that you wish to extract (guest, host, or experiments data), and you can also split the total number of adsorption isotherm experiments in chunks, so that each chunk will be collected and saved as file iteratively.

**Preprocessing:** run `preprocess_dataset.py` to perform preprocssing duties on the experiments dataset, specifically that including single component adsorption isotherm data. Use the jupiter notebook `data_analysis.ipynb` to perform explorative data analysis on the collected datasets.

### 3.1 Configurations
The configurations.py file allows to change the script configuration. 

| Category              | Setting               | Description                                           |
|-----------------------|-----------------------|-------------------------------------------------------|
| **Data settings**     | guest_fraction        | fraction of adsorbate species data to be fetched      |
|                       | host_fraction         | fraction of adsorbent materials data to be fetched    |
|                       | experiments_fraction  | fraction of adsorption isotherm data to be fetched    |
|                       | chunk_size            | fraction of data chunks to extract and save           |
| **Series settings**   | min_points            | Minimum number of measurements per experiment         |
|                       | max_pressure          | Max pressure to consider (in Pa)                      |
|                       | max_uptake            | Max uptake to consider (in mol/g)                     |
                                         
## License
This project is licensed under the terms of the MIT license. See the LICENSE file for details.

