# GlypNirO

A automated script for processing and combining Byonic and PD standard output.

# Requirements

Pythons 3.9+ with the following packages installed.
- pandas
- sequal
- uniprotparser
- requests
- xlrd
- openpyxl
- scipy

# Installation

The script can be installed using the following command.
`pip install glypniro`

# Usage

The program can be run from the location of the script using the command `glypniro`. The following parameters can be used for operating the script within the commandline.

|              Paremeters |                                                                                                                                                                  Descriptions |
|------------------------:|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|    `-i`, `--input-file` |                                                                                                                           Filepath to an xlsx file describing the experiment. |
|        `-o`, `--output` |                                                                                                                            Filepath to the output xlsx file for the analysis. |
|  `-s`, `--score-cutoff` |                                                                                                           (Optional) Default=200. Cutoff score for filtering of Byonic output |
|  `-t`, `--trust-byonic` |                                                        (Optional) Instruct the script to trust glycan position assignment and used them for area under the curve calculation. |
|         `-d`, `--debug` | (Optional) In conjunction to the final output, the script would also create debug files that contain the unique PSM selected for calculation of the data in the final output. |
| `-p`, `--parse-uniprot` |                                                                          (Optional) Attempt to parse UniProt accession ID using regular expression and use them as master id. |
|   `-g`, `--get-uniprot` |                          (Optional) Using the `uniprotparser` module to access and parse protein name from uniprot accession ids of the proteins of those within the dataset. |

## Input file format

The input file used in the `-i` parameter should have the following format.

Ex:
|condition_id|replicate_id|filename|area_filename|
|---------:|-----------:|---------:|-----------:|
|Depleted_Plasma_HCC|1|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_HCC_1.raw_Byonic.xlsx|\data\Depleted_Plasma_HCC_1_MSnSpectrumInfo.txt|
|Depleted_Plasma_HCC|2|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_HCC_2.raw_Byonic.xlsx|\data\Depleted_Plasma_HCC_2_MSnSpectrumInfo.txt|
|Depleted_Plasma_HCC|3|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_HCC_3.raw_Byonic.xlsx|\data\Depleted_Plasma_HCC_3_MSnSpectrumInfo.txt|
|Depleted_Plasma_Nor|1|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_Nor_1.raw_Byonic.xlsx|\data\Depleted_Plasma_Nor_1_MSnSpectrumInfo.txt|	
|Depleted_Plasma_Nor|2|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_Nor_2.raw_Byonic.xlsx|\data\Depleted_Plasma_Nor_2_MSnSpectrumInfo.txt|	
|Depleted_Plasma_Nor|3|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Depleted_Plasma_Nor_3.raw_Byonic.xlsx|\data\Depleted_Plasma_Nor_3_MSnSpectrumInfo.txt|	
|Non_Depleted_Plasma_HCC|1|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_HCC_1.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_HCC_1_MSnSpectrumInfo.txt|	
|Non_Depleted_Plasma_HCC|2|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_HCC_2.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_HCC_2_MSnSpectrumInfo.txt|
|Non_Depleted_Plasma_HCC|3|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_HCC_3.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_HCC_3_MSnSpectrumInfo.txt|	
|Non_Depleted_Plasma_Nor|1|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_Nor_1.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_Nor_1_MSnSpectrumInfo.txt|
|Non_Depleted_Plasma_Nor|2|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_Nor_2.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_Nor_2_MSnSpectrumInfo.txt|	
|Non_Depleted_Plasma_Nor|3|\data\NoMutliFuc_Nlink_10_20ppm_05Da_Non_Depleted_Plasma_Nor_3.raw_Byonic.xlsx|\data\Non_Depleted_Plasma_Nor_3_MSnSpectrumInfo.txt|

The rows within the input file should not have to follow any particular order however the columns have to contain the necessary content:
- `condition_id` condition label of the experiment
- `replicate_id` replicate label for the experiment
- `filename` filepath to the Byonic .xlsx output file of the experiment.
- `area_filename` filepath to the PD tabulated output file of the experiment.

# Output for default execution mode

The output of the script is a single .xlsx file with 4 sheets. 
- The first one is the output where we calculate the proportion by combining both the glycosylated and unglycosylated glycoform data.
- The second one is the output where the proportion of glycosylated was calculated without the unglycosylated data while the unglycosylated data was calculated similar to the first sheet.
- The third one is the only the filter of the glycosylated output from the second sheet.
- The forth one is the filter for only of the unglycosylated output from the first sheet.

# Example

`glypniro -i test_experiment.xlsx -o test_output.xlsx -t -g`

The above command would instruct the script to use the `test_experiment.xlsx` file as input file and output as `test_output.xlsx`. 
Inclusion of `-t` would mean that we trust Byonic assignment of glycan position and shall use them for calculation of that specific glycoform AUC within the proteins.
Inclusion of `-g` would instruct the script to connect to the UniProt online database and attempt to parse protein name from the UniProt accession id contain in the protein name within the Byonic file.

`glypniro -i test_experiment.xlsx -o test_output.xlsx`

The above command would instruct the script to use the `test_experiment.xlsx` file as input file and output as `test_output.xlsx`.
Without `-t` optional parameter, we only use the information of what glycans were found but not assigning them any positions. The AUC will only be combined for those PSMs with the same peptide sequence and glycan combination.

# Note for using with PeakView SWATH data

The command `glypniro-reformat` should be used to generate appropriate input for GlypNirO from SWATH and Byonic data.

`glypniro-reformat -b byonic.xlsx -p peakview_peptide.xlsx -o description_peakview.xlsx`

The command above will generate input files from experiment information and peptides information from GlypNirO and SWATH and create a description file that can be directly input into the main GlypNirO script.
For optimal result, the SWATH library used for the PeakView should be constructed from Byonic identification data.

After that, the `glypniro` command can be used to process the data with the additional argument `-s 0` to instruct the script to ignore the Byonic score cutoff since the combining process does not parse Byonic score and only substitute with value 1.

# Note for using with Skyline data

The command `glypniro-reformats` should be used to generate appropriate input for GlypNirO from Skyline and Byonic data.

`glypniro-reformats -b byonic.xlsx -s skyline.csv -o description_skyline.xlsx`

The command above will generate input files from experiment information and peptides information from GlypNirO and Skyline and create a description file that can be directly input into the main GlypNirO script.