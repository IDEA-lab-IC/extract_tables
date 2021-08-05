<h1> Extract Tables from PDF </h1>

According to internal specifications, this script:
1. Extracts key information from tables in a pdf
2. Structures information for an excel workbook
3. Produces an excel workbook as output

This does not always result in the "correct" result due to the inconsistency and variety of the input pdfs.
Therefore, this has been written to account for the fact that it will fail at points. The aim is to capture the
most commonly occuring cases to reduce the overall workload.

<h2> Core Dependency </h2>

This utilises the python package [tabula](https://tabula-py.readthedocs.io/en/latest/tabula.html) to read tables within
a pdf. As this package is a python wrapper of [tabula-java](https://github.com/tabulapdf/tabula-java), has [java as a dependency](https://pypi.org/project/tabula-py/).

Why [Tabula](https://tabula.technology/) and not [Camelot](https://camelot-py.readthedocs.io/en/master/index.html)? The pdfs to be analysed resulted in Camelot throwing an error while Tabula did not. 

<h2> Installation </h2>

Installation can be broken down into three main steps:
  1. Installing the Python packages this repo is dependent on
  2. Installing the dependencies of Tabula
  3. Downloading the code base
After that, you're good to go!

*N.B. on Operating System:* This was developed in Linux (Ubuntu 18.04) and has not been tested in any other operating system.

<h3> Setting up Python environment </h3>

There are two ways to set up the Python environment - using [pip](https://pypi.org/project/pip/) or [Conda](https://github.com/conda/conda) to download the required Python packages. 

For Conda installation, [environment.yml](environment.yml) contains the requirements needed to (hopefully) recreate the environment this was developed in.
  1. Use the terminal or anaconda prompt to run 
       ```
       conda env create -f environment.yml
       ```
     This should automatically create the Conda environment. For further details/help, please refer to the Conda guide on [creating an environment](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file)
  2. To activate the envronment, 
       ```
       conda activate extract_pdf
       ```
  3. The `environment.yml` should capture the packages only in the pip distribution. The method used to achieve this is from [here](https://stackoverflow.com/questions/35245401/combining-conda-environment-yml-with-pip-requirements-txt). 
  4. If in doubt, ask Google. 

For pip installation, [requirements.txt](requirements.txt) is the `environment.yml` equivalent. For further dtails on pip, refer to their [documentation](https://pip.pypa.io/en/stable/getting-started/).
  1. In the terminal, run 
       ```
       pip install -r requirements.txt
       ```
  2. This should have all the requirement packages installed. If in doubt, this [post](https://stackoverflow.com/questions/29980798/where-does-pip-install-its-packages) is quite helpful to understand where pip has installed it to.

Once these have been installed, ensure that the dependencies for Tabula have been installed as well. 

<h3> Downloading the Code Base </h3>

With all the hard bits done, this should be relatively painless. There are a couple of ways to do this,
  - Git clone
      Go to the folder you want to download into, then run this command 
      ```
      git clone https://github.com/wong-hl/admins_urop.git
      ```
  - Download the zip file
  - Click the Green download code button 

<h2> How to use? </h2>

The main script is [`extract_table.py`](extract_table.py). User input is only required for the:
  1. _Absolute_ path to the folder containing all the pdf files to be analysed
  2. Filename and sheetname of the mapping excel sheet. This sheet maps the qualification names in the pdfs to the convention used internally.

Once that has been provided, run in the terminal, 
  ```
  python extract_table.py
  ```
This will execute the script and a progress bar will print on a single line. 

<h2> How has this been structured? </h2>

`extract_table.py` coordinates the whole table extraction. It iterates over all files in the folder provided. 
- The extracted information for each pdf is stored in an instance of the object `ExtractedStudents()`
- For each file, starting from the 2nd page, the tables are extracted by `tablula.read_pdf()` into a `Pandas Dataframe`. 
  - For a given page, the tables in the page are checked to identify it is a target table or if it is the last table in the pdf 
    - If it is a target table, then it is checked for being split over two pages and a fix is applied accordingly.
    - If it is the last table, the exit condition is triggered
  - Once the exit condition is triggered, 
    - An instance of `Student()` is created
    - Instance is added to `ExtractedStudents()` object
  - If the exit condition is not triggered, but instead the end of file (eof) is encountered. Then, this is handled by the exception. In the handling, the extracted information is stored in the same manner as if the exit condition was triggered. 
- Once all files have been processed, the `ExtractedStudents()` object is called to write the information to an excel file.  

`student.py` is where all the logic and actual processing occurs. There are three classes within this. 
  1. `ExtractedStudents()`
      - It can be viewed as being the highest level. An analogy of this would be this manages the entire cohort of students studying a given course.
      - This class coordinates the overall management of every instance of `Student()` that belongs to it 
  2. `Student()`
      - An analogy for this would be an individual student studying a given course. This student will have grades in different subjects and each of these need to be stored, sorted and verified.
      - This class handles all the complexity involved in storing and extracting relevant information for a given pdf. 
  3. `GradeEntry()`
      - It can be viewed as being the lowest level. 
      - It only contains key information related to a given data entry (row in a table). 
      - This information could have simply be stored in a dictionary or a list instead. However, this method was adopted with the aim of improving the readability of the code.

`utils.py` contains the useful functions (utilities) and functions that store strings needed to extracting information.

<h2> To Do </h2>

Refactor!

Create the foolproof but unmaintanable beast of an exe file 
