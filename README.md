# DPSyn: a quick-start guide 
## What is DPsyn?

We present DPSyn, an algorithm for synthesizing microdata for data analysis while satisfying differential privacy.

To facilitate your understanding, please refer to the paper [*PrivSyn: Differentially Private Data Synthesis*](https://www.usenix.org/conference/usenixsecurity21/presentation/zhang-zhikun). And we utilized the record synthesis method proposed in that paper, which is GUM (Gradually Update Method).

### Comparison with related work

There are two similar and highly-related papers (both from competitors in the competition). They are:
[PrivMRF](http://www.vldb.org/pvldb/vol14/p2190-cai.pdf), and
[PGM](https://arxiv.org/pdf/1901.09136.pdf).

The difference is that PrivSyn includes the whole data synthesis pipeline: (1) finding marginals and (2) synthesize dataset from the marginals. PGM only handles the second part, and PrivMRF only handles the first part (and uses PGM as the second part).  Since the PrivSyn and PrivMRF appear concurrently, there is no direct comparison between the two. Compared to PGM, PrivSyn shows its synthesizing method can handle *dense* graph.

In this repository, we only include the second part for now.

----


## Install DPSyn 

### Docker support

We created a public image in docker.io [link](https://hub.docker.com/repository/docker/chenanqi18pku/dpsyn). You can obtain it by

```powershell
> docker pull chenanqi18pku/dpsyn:v3
```

By the way, since the Dockerfile is already included, you can also directly create the image with the current directory if you download the repository as below shows:

```powershell
> docker build -t dpsyn .
```

Then you get the image and by the command "docker images", you can find it

```powershell
> docker images
REPOSITORY            TAG       IMAGE ID       CREATED             SIZE
chenanqi18pku/dpsyn   v3        2d54b63a496d   About an hour ago   1.31GB
```

Then you can create a container to run the image. 

Before we run the image, we store the original dataset "test.csv" and related useful files in C:/test. After running the image in the "test"container ( the full run example is shown in below part ) you can find the synthetic dataset "testout.csv" in your local directory C:/test as below shows: 

```shell
C:\test>ls
 column_datatypes.json   data.yaml  'eps=10.0.yaml'   parameters.json   test.csv   testout.csv
```

Here is a run example under Win10 in powershell, with the container named "test". Note that we map the directory /c/test in the Win10 PC to the directory /DPSyn/tmp in the container. Then when you manipulate the directory /DPSyn/tmp in the container, actually you are dealing with the local directory /c/test, where you can store input files and get the output synthetic dataset.

```powershell
>  docker run -it --name test -v /c/test:/DPSyn/tmp chenanqi18pku/dpsyn:v3 --priv_data_name test --priv_data /DPSyn/tmp/test.csv --target_path /DPSyn/tmp/testout.csv --config /DPSyn/tmp/data.yaml --params /DPSyn/tmp/parameters.json --datatype /DPSyn/tmp/column_datatypes.json --marginal_config /DPSyn/tmp/eps=10.0.yaml
------------------------> config yaml file loaded in DataLoader, config file:  /DPSyn/tmp/data.yaml
------------------------> parameter file loaded in DataLoader, parameter file:  /DPSyn/tmp/parameters.json
************* start loading private data *************
------------------------> process and store with pkl file name:  preprocessed_priv_test.pkl
           ID  Date  Age     Sex  ... CardioCondition RespiratoryCondition ObesityCondition DiabetesCondition
0     12-0187  2012   34  FEMALE  ...             NaN                  NaN              NaN               NaN
1     12-0258  2012   51    MALE  ...             NaN                  NaN              NaN               NaN
2     13-0146  2013   28    MALE  ...             NaN                  NaN              NaN               NaN
3     14-0150  2014   46    MALE  ...             NaN                  NaN              NaN               NaN
4     14-0183  2014   52    MALE  ...             NaN                  NaN              NaN               NaN
...       ...   ...  ...     ...  ...             ...                  ...              ...               ...
7629  14-0128  2014   25    MALE  ...             NaN                  NaN              NaN               NaN
7630  20-1217  2020   62  FEMALE  ...             NaN                  NaN              NaN               NaN
7631  20-1138  2020   50  FEMALE  ...             NaN                  NaN              NaN               NaN
7632  16-0640  2016   36    MALE  ...             NaN                  NaN              NaN               NaN
7633  19-0963  2019   33    MALE  ...               Y                  NaN              NaN               NaN

[7634 rows x 32 columns]
********** afer fillna ***********
           ID  Date  Age     Sex  ... CardioCondition RespiratoryCondition ObesityCondition DiabetesCondition
0     12-0187  2012   34  FEMALE  ...
1     12-0258  2012   51    MALE  ...
2     13-0146  2013   28    MALE  ...
3     14-0150  2014   46    MALE  ...
4     14-0183  2014   52    MALE  ...
...       ...   ...  ...     ...  ...             ...                  ...              ...               ...
7629  14-0128  2014   25    MALE  ...
7630  20-1217  2020   62  FEMALE  ...
7631  20-1138  2020   50  FEMALE  ...
7632  16-0640  2016   36    MALE  ...
7633  19-0963  2019   33    MALE  ...               Y

[7634 rows x 32 columns]
------------------------> private dataset:  /DPSyn/tmp/test.csv
binning attributes done in DataLoader
------------------------> remove identifier column: ID
identifier removed in DataLoader
------------------------> start encoding remaining single attributes
encode remain: Date
encode remain: Sex
encode remain: Race
encode remain: Residence State
encode remain: Death County
encode remain: Location
encode remain: Location if Other
encode remain: Injury County
encode remain: Injury State
encode remain: Heroin
encode remain: Cocaine
encode remain: Fentanyl
encode remain: Fentanyl Analogue
encode remain: Oxycodone
encode remain: Oxymorphone
encode remain: Ethanol
encode remain: Hydrocodone
encode remain: Benzodiazepine
encode remain: Methadone
encode remain: Amphet
encode remain: Tramad
encode remain: Morphine (Not Heroin)
encode remain: Hydromorphone
encode remain: Xylazine
encode remain: Opiate NOS
encode remain: Any Opioid
encode remain: CardioCondition
encode remain: RespiratoryCondition
encode remain: ObesityCondition
encode remain: DiabetesCondition
encoding remaining single attributes done in DataLoader
************* private data loaded and preprocessed in DataLoader ************
priv df's rows:------------------------>  7634
2021-10-24 09:27:35.579 | INFO     | __main__:run_method:106 - working on eps=10.0, delta=3.4498908254380166e-11, and sensitivity=1
------------------------> all two way marginals generated
**************** help debug ************** num of records averaged from all two-way marginals: 7633.419354838709
**************** help debug ************** num of records from marginal count before adding noise: 7633.419354838709
------------------------> now we decide the noise type:
considering eps: 10.0 , delta: 3.4498908254380166e-11 , sensitivity: 1 , len of marginals: 465
------------------------> noise type: gauss
------------------------> noise parameter: 16.386725928253217
2021-10-24 09:27:43.746 | INFO     | method.synthesizer:anonymize:79 - marginal priv_all_two_way use eps=10.0, noise type:gauss, noise parameter=16.386725928253217, sensitivity:1
------------------------> now we get the estimate of records' num by averaging from nosiy marginals: 7630
2021-10-24 09:27:51.324 | DEBUG    | lib_dpsyn.consistent:consist_views:105 - dependency computed
2021-10-24 09:27:51.816 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:51.832 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:52.312 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:52.326 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:52.806 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:52.819 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:53.297 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:53.310 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:53.833 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:53.845 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:54.323 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:54.334 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:54.811 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:54.822 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:55.298 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:55.309 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:55.790 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:55.802 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:56.292 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:56.304 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:56.790 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:56.802 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:57.285 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:57.298 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:57.772 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:57.783 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:58.260 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:58.273 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:58.750 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:58.761 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:59.238 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:59.250 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:27:59.723 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:27:59.736 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:00.229 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:00.242 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:00.728 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:00.740 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:01.216 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:01.227 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:01.706 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:01.718 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:02.209 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:02.220 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:02.725 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:02.737 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:03.257 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:03.269 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:03.746 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:03.757 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:04.231 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:04.242 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:04.770 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:04.787 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:05.327 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:05.341 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:05.870 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:05.882 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 09:28:06.422 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 09:28:06.436 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
------------------------> attributes:
['Date', 'Age', 'Sex', 'Race', 'Residence State', 'Death County', 'Location', 'Location if Other', 'Injury County', 'Injury State', 'Heroin', 'Cocaine', 'Fentanyl', 'Fentanyl Analogue', 'Oxycodone', 'Oxymorphone', 'Ethanol', 'Hydrocodone', 'Benzodiazepine', 'Methadone', 'Amphet', 'Tramad', 'Morphine (Not Heroin)', 'Hydromorphone', 'Xylazine', 'Opiate NOS', 'Any Opioid', 'CardioCondition', 'RespiratoryCondition', 'ObesityCondition', 'DiabetesCondition']
------------------------> domains:
[ 9  9  2  7 11  9  7 10 15  3  2  2  2  2  2  2  2  2  2  2  2  2  2  2
  2  2  2  2  2  2  2]
------------------------> cluseters:
{('Oxycodone', 'Opiate NOS', 'Heroin', 'Residence State', 'Methadone', 'Xylazine', 'Sex', 'Location if Other', 'DiabetesCondition', 'Any Opioid', 'CardioCondition', 'Cocaine', 'Hydromorphone', 'ObesityCondition', 'Race', 'Location', 'Age', 'Hydrocodone', 'Benzodiazepine', 'Death County', 'Injury County', 'RespiratoryCondition', 'Injury State', 'Morphine (Not Heroin)', 'Tramad', 'Fentanyl', 'Date', 'Amphet', 'Ethanol', 'Oxymorphone', 'Fentanyl Analogue'): [frozenset({'Age', 'Date'}), frozenset({'Sex', 'Date'}), frozenset({'Race', 'Date'}), frozenset({'Residence State', 'Date'}), frozenset({'Death County', 'Date'}), frozenset({'Location', 'Date'}), frozenset({'Location if Other', 'Date'}), frozenset({'Injury County', 'Date'}), frozenset({'Injury State', 'Date'}), frozenset({'Heroin', 'Date'}), frozenset({'Cocaine', 'Date'}), frozenset({'Fentanyl', 'Date'}), frozenset({'Fentanyl Analogue', 'Date'}), frozenset({'Oxycodone', 'Date'}), frozenset({'Oxymorphone', 'Date'}), frozenset({'Ethanol', 'Date'}), frozenset({'Hydrocodone', 'Date'}), frozenset({'Benzodiazepine', 'Date'}), frozenset({'Methadone', 'Date'}), frozenset({'Amphet', 'Date'}), frozenset({'Tramad', 'Date'}), frozenset({'Morphine (Not Heroin)', 'Date'}), frozenset({'Hydromorphone', 'Date'}), frozenset({'Xylazine', 'Date'}), frozenset({'Opiate NOS', 'Date'}), frozenset({'Any Opioid', 'Date'}), frozenset({'CardioCondition', 'Date'}), frozenset({'RespiratoryCondition', 'Date'}), frozenset({'ObesityCondition', 'Date'}), frozenset({'DiabetesCondition', 'Date'}), frozenset({'Age', 'Sex'}), frozenset({'Age', 'Race'}), frozenset({'Age', 'Residence State'}), frozenset({'Age', 'Death County'}), frozenset({'Age', 'Location'}), frozenset({'Age', 'Location if Other'}), frozenset({'Age', 'Injury County'}), frozenset({'Age', 'Injury State'}), frozenset({'Age', 'Heroin'}), frozenset({'Age', 'Cocaine'}), frozenset({'Age', 'Fentanyl'}), frozenset({'Age', 'Fentanyl Analogue'}), frozenset({'Age', 'Oxycodone'}), frozenset({'Age', 'Oxymorphone'}), frozenset({'Age', 'Ethanol'}), frozenset({'Age', 'Hydrocodone'}), frozenset({'Age', 'Benzodiazepine'}), frozenset({'Age', 'Methadone'}), frozenset({'Age', 'Amphet'}), frozenset({'Age', 'Tramad'}), frozenset({'Age', 'Morphine (Not Heroin)'}), frozenset({'Age', 'Hydromorphone'}), frozenset({'Age', 'Xylazine'}), frozenset({'Age', 'Opiate NOS'}), frozenset({'Age', 'Any Opioid'}), frozenset({'Age', 'CardioCondition'}), frozenset({'Age', 'RespiratoryCondition'}), frozenset({'Age', 'ObesityCondition'}), frozenset({'Age', 'DiabetesCondition'}), frozenset({'Sex', 'Race'}), frozenset({'Sex', 'Residence State'}), frozenset({'Sex', 'Death County'}), frozenset({'Sex', 'Location'}), frozenset({'Sex', 'Location if Other'}), frozenset({'Sex', 'Injury County'}), frozenset({'Sex', 'Injury State'}), frozenset({'Sex', 'Heroin'}), frozenset({'Sex', 'Cocaine'}), frozenset({'Sex', 'Fentanyl'}), frozenset({'Sex', 'Fentanyl Analogue'}), frozenset({'Sex', 'Oxycodone'}), frozenset({'Sex', 'Oxymorphone'}), frozenset({'Sex', 'Ethanol'}), frozenset({'Sex', 'Hydrocodone'}), frozenset({'Sex', 'Benzodiazepine'}), frozenset({'Sex', 'Methadone'}), frozenset({'Sex', 'Amphet'}), frozenset({'Sex', 'Tramad'}), frozenset({'Sex', 'Morphine (Not Heroin)'}), frozenset({'Sex', 'Hydromorphone'}), frozenset({'Sex', 'Xylazine'}), frozenset({'Sex', 'Opiate NOS'}), frozenset({'Sex', 'Any Opioid'}), frozenset({'Sex', 'CardioCondition'}), frozenset({'Sex', 'RespiratoryCondition'}), frozenset({'Sex', 'ObesityCondition'}), frozenset({'Sex', 'DiabetesCondition'}), frozenset({'Race', 'Residence State'}), frozenset({'Death County', 'Race'}), frozenset({'Location', 'Race'}), frozenset({'Location if Other', 'Race'}), frozenset({'Injury County', 'Race'}), frozenset({'Injury State', 'Race'}), frozenset({'Heroin', 'Race'}), frozenset({'Cocaine', 'Race'}), frozenset({'Fentanyl', 'Race'}), frozenset({'Fentanyl Analogue', 'Race'}), frozenset({'Oxycodone', 'Race'}), frozenset({'Oxymorphone', 'Race'}), frozenset({'Ethanol', 'Race'}), frozenset({'Hydrocodone', 'Race'}), frozenset({'Benzodiazepine', 'Race'}), frozenset({'Methadone', 'Race'}), frozenset({'Amphet', 'Race'}), frozenset({'Tramad', 'Race'}), frozenset({'Morphine (Not Heroin)', 'Race'}), frozenset({'Hydromorphone', 'Race'}), frozenset({'Race', 'Xylazine'}), frozenset({'Opiate NOS', 'Race'}), frozenset({'Any Opioid', 'Race'}), frozenset({'CardioCondition', 'Race'}), frozenset({'RespiratoryCondition', 'Race'}), frozenset({'ObesityCondition', 'Race'}), frozenset({'DiabetesCondition', 'Race'}), frozenset({'Death County', 'Residence State'}), frozenset({'Location', 'Residence State'}), frozenset({'Location if Other', 'Residence State'}), frozenset({'Injury County', 'Residence State'}), frozenset({'Injury State', 'Residence State'}), frozenset({'Heroin', 'Residence State'}), frozenset({'Cocaine', 'Residence State'}), frozenset({'Fentanyl', 'Residence State'}), frozenset({'Fentanyl Analogue', 'Residence State'}), frozenset({'Oxycodone', 'Residence State'}), frozenset({'Oxymorphone', 'Residence State'}), frozenset({'Ethanol', 'Residence State'}), frozenset({'Hydrocodone', 'Residence State'}), frozenset({'Benzodiazepine', 'Residence State'}), frozenset({'Methadone', 'Residence State'}), frozenset({'Amphet', 'Residence State'}), frozenset({'Tramad', 'Residence State'}), frozenset({'Morphine (Not Heroin)', 'Residence State'}), frozenset({'Hydromorphone', 'Residence State'}), frozenset({'Xylazine', 'Residence State'}), frozenset({'Opiate NOS', 'Residence State'}), frozenset({'Any Opioid', 'Residence State'}), frozenset({'CardioCondition', 'Residence State'}), frozenset({'RespiratoryCondition', 'Residence State'}), frozenset({'ObesityCondition', 'Residence State'}), frozenset({'DiabetesCondition', 'Residence State'}), frozenset({'Death County', 'Location'}), frozenset({'Location if Other', 'Death County'}), frozenset({'Injury County', 'Death County'}), frozenset({'Injury State', 'Death County'}), frozenset({'Death County', 'Heroin'}), frozenset({'Cocaine', 'Death County'}), frozenset({'Death County', 'Fentanyl'}), frozenset({'Fentanyl Analogue', 'Death County'}), frozenset({'Oxycodone', 'Death County'}), frozenset({'Oxymorphone', 'Death County'}), frozenset({'Ethanol', 'Death County'}), frozenset({'Hydrocodone', 'Death County'}), frozenset({'Benzodiazepine', 'Death County'}), frozenset({'Methadone', 'Death County'}), frozenset({'Amphet', 'Death County'}), frozenset({'Tramad', 'Death County'}), frozenset({'Morphine (Not Heroin)', 'Death County'}), frozenset({'Hydromorphone', 'Death County'}), frozenset({'Death County', 'Xylazine'}), frozenset({'Opiate NOS', 'Death County'}), frozenset({'Any Opioid', 'Death County'}), frozenset({'CardioCondition', 'Death County'}), frozenset({'RespiratoryCondition', 'Death County'}), frozenset({'ObesityCondition', 'Death County'}), frozenset({'DiabetesCondition', 'Death County'}), frozenset({'Location if Other', 'Location'}), frozenset({'Injury County', 'Location'}), frozenset({'Injury State', 'Location'}), frozenset({'Heroin', 'Location'}), frozenset({'Cocaine', 'Location'}), frozenset({'Fentanyl', 'Location'}), frozenset({'Fentanyl Analogue', 'Location'}), frozenset({'Oxycodone', 'Location'}), frozenset({'Oxymorphone', 'Location'}), frozenset({'Ethanol', 'Location'}), frozenset({'Hydrocodone', 'Location'}), frozenset({'Benzodiazepine', 'Location'}), frozenset({'Methadone', 'Location'}), frozenset({'Amphet', 'Location'}), frozenset({'Tramad', 'Location'}), frozenset({'Morphine (Not Heroin)', 'Location'}), frozenset({'Hydromorphone', 'Location'}), frozenset({'Location', 'Xylazine'}), frozenset({'Opiate NOS', 'Location'}), frozenset({'Any Opioid', 'Location'}), frozenset({'CardioCondition', 'Location'}), frozenset({'RespiratoryCondition', 'Location'}), frozenset({'ObesityCondition', 'Location'}), frozenset({'DiabetesCondition', 'Location'}), frozenset({'Location if Other', 'Injury County'}), frozenset({'Location if Other', 'Injury State'}), frozenset({'Location if Other', 'Heroin'}), frozenset({'Location if Other', 'Cocaine'}), frozenset({'Location if Other', 'Fentanyl'}), frozenset({'Location if Other', 'Fentanyl Analogue'}), frozenset({'Location if Other', 'Oxycodone'}), frozenset({'Location if Other', 'Oxymorphone'}), frozenset({'Location if Other', 'Ethanol'}), frozenset({'Location if Other', 'Hydrocodone'}), frozenset({'Location if Other', 'Benzodiazepine'}), frozenset({'Location if Other', 'Methadone'}), frozenset({'Location if Other', 'Amphet'}), frozenset({'Location if Other', 'Tramad'}), frozenset({'Location if Other', 'Morphine (Not Heroin)'}), frozenset({'Location if Other', 'Hydromorphone'}), frozenset({'Location if Other', 'Xylazine'}), frozenset({'Location if Other', 'Opiate NOS'}), frozenset({'Location if Other', 'Any Opioid'}), frozenset({'Location if Other', 'CardioCondition'}), frozenset({'Location if Other', 'RespiratoryCondition'}), frozenset({'Location if Other', 'ObesityCondition'}), frozenset({'Location if Other', 'DiabetesCondition'}), frozenset({'Injury State', 'Injury County'}), frozenset({'Injury County', 'Heroin'}), frozenset({'Cocaine', 'Injury County'}), frozenset({'Injury County', 'Fentanyl'}), frozenset({'Fentanyl Analogue', 'Injury County'}), frozenset({'Oxycodone', 'Injury County'}), frozenset({'Oxymorphone', 'Injury County'}), frozenset({'Ethanol', 'Injury County'}), frozenset({'Hydrocodone', 'Injury County'}), frozenset({'Benzodiazepine', 'Injury County'}), frozenset({'Methadone', 'Injury County'}), frozenset({'Amphet', 'Injury County'}), frozenset({'Tramad', 'Injury County'}), frozenset({'Morphine (Not Heroin)', 'Injury County'}), frozenset({'Hydromorphone', 'Injury County'}), frozenset({'Injury County', 'Xylazine'}), frozenset({'Opiate NOS', 'Injury County'}), frozenset({'Any Opioid', 'Injury County'}), frozenset({'CardioCondition', 'Injury County'}), frozenset({'RespiratoryCondition', 'Injury County'}), frozenset({'ObesityCondition', 'Injury County'}), frozenset({'DiabetesCondition', 'Injury County'}), frozenset({'Injury State', 'Heroin'}), frozenset({'Injury State', 'Cocaine'}), frozenset({'Injury State', 'Fentanyl'}), frozenset({'Injury State', 'Fentanyl Analogue'}), frozenset({'Injury State', 'Oxycodone'}), frozenset({'Injury State', 'Oxymorphone'}), frozenset({'Ethanol', 'Injury State'}), frozenset({'Injury State', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Injury State'}), frozenset({'Methadone', 'Injury State'}), frozenset({'Injury State', 'Amphet'}), frozenset({'Injury State', 'Tramad'}), frozenset({'Injury State', 'Morphine (Not Heroin)'}), frozenset({'Injury State', 'Hydromorphone'}), frozenset({'Injury State', 'Xylazine'}), frozenset({'Injury State', 'Opiate NOS'}), frozenset({'Injury State', 'Any Opioid'}), frozenset({'Injury State', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Injury State'}), frozenset({'Injury State', 'ObesityCondition'}), frozenset({'Injury State', 'DiabetesCondition'}), frozenset({'Cocaine', 'Heroin'}), frozenset({'Fentanyl', 'Heroin'}), frozenset({'Fentanyl Analogue', 'Heroin'}), frozenset({'Oxycodone', 'Heroin'}), frozenset({'Oxymorphone', 'Heroin'}), frozenset({'Ethanol', 'Heroin'}), frozenset({'Hydrocodone', 'Heroin'}), frozenset({'Benzodiazepine', 'Heroin'}), frozenset({'Methadone', 'Heroin'}), frozenset({'Amphet', 'Heroin'}), frozenset({'Tramad', 'Heroin'}), frozenset({'Morphine (Not Heroin)', 'Heroin'}), frozenset({'Hydromorphone', 'Heroin'}), frozenset({'Heroin', 'Xylazine'}), frozenset({'Opiate NOS', 'Heroin'}), frozenset({'Any Opioid', 'Heroin'}), frozenset({'CardioCondition', 'Heroin'}), frozenset({'RespiratoryCondition', 'Heroin'}), frozenset({'ObesityCondition', 'Heroin'}), frozenset({'DiabetesCondition', 'Heroin'}), frozenset({'Cocaine', 'Fentanyl'}), frozenset({'Cocaine', 'Fentanyl Analogue'}), frozenset({'Cocaine', 'Oxycodone'}), frozenset({'Oxymorphone', 'Cocaine'}), frozenset({'Ethanol', 'Cocaine'}), frozenset({'Hydrocodone', 'Cocaine'}), frozenset({'Benzodiazepine', 'Cocaine'}), frozenset({'Methadone', 'Cocaine'}), frozenset({'Amphet', 'Cocaine'}), frozenset({'Cocaine', 'Tramad'}), frozenset({'Morphine (Not Heroin)', 'Cocaine'}), frozenset({'Cocaine', 'Hydromorphone'}), frozenset({'Cocaine', 'Xylazine'}), frozenset({'Cocaine', 'Opiate NOS'}), frozenset({'Cocaine', 'Any Opioid'}), frozenset({'Cocaine', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Cocaine'}), frozenset({'Cocaine', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Cocaine'}), frozenset({'Fentanyl Analogue', 'Fentanyl'}), frozenset({'Oxycodone', 'Fentanyl'}), frozenset({'Oxymorphone', 'Fentanyl'}), frozenset({'Ethanol', 'Fentanyl'}), frozenset({'Hydrocodone', 'Fentanyl'}), frozenset({'Benzodiazepine', 'Fentanyl'}), frozenset({'Methadone', 'Fentanyl'}), frozenset({'Amphet', 'Fentanyl'}), frozenset({'Tramad', 'Fentanyl'}), frozenset({'Morphine (Not Heroin)', 'Fentanyl'}), frozenset({'Hydromorphone', 'Fentanyl'}), frozenset({'Fentanyl', 'Xylazine'}), frozenset({'Opiate NOS', 'Fentanyl'}), frozenset({'Any Opioid', 'Fentanyl'}), frozenset({'CardioCondition', 'Fentanyl'}), frozenset({'RespiratoryCondition', 'Fentanyl'}), frozenset({'ObesityCondition', 'Fentanyl'}), frozenset({'DiabetesCondition', 'Fentanyl'}), frozenset({'Oxycodone', 'Fentanyl Analogue'}), frozenset({'Oxymorphone', 'Fentanyl Analogue'}), frozenset({'Ethanol', 'Fentanyl Analogue'}), frozenset({'Hydrocodone', 'Fentanyl Analogue'}), frozenset({'Benzodiazepine', 'Fentanyl Analogue'}), frozenset({'Methadone', 'Fentanyl Analogue'}), frozenset({'Amphet', 'Fentanyl Analogue'}), frozenset({'Tramad', 'Fentanyl Analogue'}), frozenset({'Morphine (Not Heroin)', 'Fentanyl Analogue'}), frozenset({'Hydromorphone', 'Fentanyl Analogue'}), frozenset({'Fentanyl Analogue', 'Xylazine'}), frozenset({'Opiate NOS', 'Fentanyl Analogue'}), frozenset({'Any Opioid', 'Fentanyl Analogue'}), frozenset({'CardioCondition', 'Fentanyl Analogue'}), frozenset({'RespiratoryCondition', 'Fentanyl Analogue'}), frozenset({'ObesityCondition', 'Fentanyl Analogue'}), frozenset({'DiabetesCondition', 'Fentanyl Analogue'}), frozenset({'Oxymorphone', 'Oxycodone'}), frozenset({'Ethanol', 'Oxycodone'}), frozenset({'Hydrocodone', 'Oxycodone'}), frozenset({'Benzodiazepine', 'Oxycodone'}), frozenset({'Methadone', 'Oxycodone'}), frozenset({'Amphet', 'Oxycodone'}), frozenset({'Oxycodone', 'Tramad'}), frozenset({'Morphine (Not Heroin)', 'Oxycodone'}), frozenset({'Oxycodone', 'Hydromorphone'}), frozenset({'Oxycodone', 'Xylazine'}), frozenset({'Oxycodone', 'Opiate NOS'}), frozenset({'Oxycodone', 'Any Opioid'}), frozenset({'Oxycodone', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Oxycodone'}), frozenset({'Oxycodone', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Oxycodone'}), frozenset({'Ethanol', 'Oxymorphone'}), frozenset({'Oxymorphone', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Oxymorphone'}), frozenset({'Methadone', 'Oxymorphone'}), frozenset({'Oxymorphone', 'Amphet'}), frozenset({'Oxymorphone', 'Tramad'}), frozenset({'Oxymorphone', 'Morphine (Not Heroin)'}), frozenset({'Oxymorphone', 'Hydromorphone'}), frozenset({'Oxymorphone', 'Xylazine'}), frozenset({'Oxymorphone', 'Opiate NOS'}), frozenset({'Oxymorphone', 'Any Opioid'}), frozenset({'Oxymorphone', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Oxymorphone'}), frozenset({'Oxymorphone', 'ObesityCondition'}), frozenset({'Oxymorphone', 'DiabetesCondition'}), frozenset({'Ethanol', 'Hydrocodone'}), frozenset({'Ethanol', 'Benzodiazepine'}), frozenset({'Ethanol', 'Methadone'}), frozenset({'Ethanol', 'Amphet'}), frozenset({'Ethanol', 'Tramad'}), frozenset({'Ethanol', 'Morphine (Not Heroin)'}), frozenset({'Ethanol', 'Hydromorphone'}), frozenset({'Ethanol', 'Xylazine'}), frozenset({'Ethanol', 'Opiate NOS'}), frozenset({'Ethanol', 'Any Opioid'}), frozenset({'Ethanol', 'CardioCondition'}), frozenset({'Ethanol', 'RespiratoryCondition'}), frozenset({'Ethanol', 'ObesityCondition'}), frozenset({'Ethanol', 'DiabetesCondition'}), frozenset({'Benzodiazepine', 'Hydrocodone'}), frozenset({'Methadone', 'Hydrocodone'}), frozenset({'Hydrocodone', 'Amphet'}), frozenset({'Hydrocodone', 'Tramad'}), frozenset({'Hydrocodone', 'Morphine (Not Heroin)'}), frozenset({'Hydrocodone', 'Hydromorphone'}), frozenset({'Hydrocodone', 'Xylazine'}), frozenset({'Hydrocodone', 'Opiate NOS'}), frozenset({'Hydrocodone', 'Any Opioid'}), frozenset({'Hydrocodone', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Hydrocodone'}), frozenset({'Hydrocodone', 'ObesityCondition'}), frozenset({'Hydrocodone', 'DiabetesCondition'}), frozenset({'Benzodiazepine', 'Methadone'}), frozenset({'Benzodiazepine', 'Amphet'}), frozenset({'Benzodiazepine', 'Tramad'}), frozenset({'Benzodiazepine', 'Morphine (Not Heroin)'}), frozenset({'Benzodiazepine', 'Hydromorphone'}), frozenset({'Benzodiazepine', 'Xylazine'}), frozenset({'Benzodiazepine', 'Opiate NOS'}), frozenset({'Benzodiazepine', 'Any Opioid'}), frozenset({'Benzodiazepine', 'CardioCondition'}), frozenset({'Benzodiazepine', 'RespiratoryCondition'}), frozenset({'Benzodiazepine', 'ObesityCondition'}), frozenset({'Benzodiazepine', 'DiabetesCondition'}), frozenset({'Methadone', 'Amphet'}), frozenset({'Methadone', 'Tramad'}), frozenset({'Methadone', 'Morphine (Not Heroin)'}), frozenset({'Methadone', 'Hydromorphone'}), frozenset({'Methadone', 'Xylazine'}), frozenset({'Methadone', 'Opiate NOS'}), frozenset({'Methadone', 'Any Opioid'}), frozenset({'Methadone', 'CardioCondition'}), frozenset({'Methadone', 'RespiratoryCondition'}), frozenset({'Methadone', 'ObesityCondition'}), frozenset({'Methadone', 'DiabetesCondition'}), frozenset({'Amphet', 'Tramad'}), frozenset({'Morphine (Not Heroin)', 'Amphet'}), frozenset({'Amphet', 'Hydromorphone'}), frozenset({'Amphet', 'Xylazine'}), frozenset({'Amphet', 'Opiate NOS'}), frozenset({'Amphet', 'Any Opioid'}), frozenset({'Amphet', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Amphet'}), frozenset({'Amphet', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Amphet'}), frozenset({'Morphine (Not Heroin)', 'Tramad'}), frozenset({'Hydromorphone', 'Tramad'}), frozenset({'Tramad', 'Xylazine'}), frozenset({'Opiate NOS', 'Tramad'}), frozenset({'Any Opioid', 'Tramad'}), frozenset({'CardioCondition', 'Tramad'}), frozenset({'RespiratoryCondition', 'Tramad'}), frozenset({'ObesityCondition', 'Tramad'}), frozenset({'DiabetesCondition', 'Tramad'}), frozenset({'Morphine (Not Heroin)', 'Hydromorphone'}), frozenset({'Morphine (Not Heroin)', 'Xylazine'}), frozenset({'Morphine (Not Heroin)', 'Opiate NOS'}), frozenset({'Morphine (Not Heroin)', 'Any Opioid'}), frozenset({'Morphine (Not Heroin)', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Morphine (Not Heroin)'}), frozenset({'Morphine (Not Heroin)', 'ObesityCondition'}), frozenset({'Morphine (Not Heroin)', 'DiabetesCondition'}), frozenset({'Hydromorphone', 'Xylazine'}), frozenset({'Hydromorphone', 'Opiate NOS'}), frozenset({'Hydromorphone', 'Any Opioid'}), frozenset({'CardioCondition', 'Hydromorphone'}), frozenset({'RespiratoryCondition', 'Hydromorphone'}), frozenset({'Hydromorphone', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Hydromorphone'}), frozenset({'Opiate NOS', 'Xylazine'}), frozenset({'Any Opioid', 'Xylazine'}), frozenset({'CardioCondition', 'Xylazine'}), frozenset({'RespiratoryCondition', 'Xylazine'}), frozenset({'ObesityCondition', 'Xylazine'}), frozenset({'DiabetesCondition', 'Xylazine'}), frozenset({'Opiate NOS', 'Any Opioid'}), frozenset({'CardioCondition', 'Opiate NOS'}), frozenset({'RespiratoryCondition', 'Opiate NOS'}), frozenset({'Opiate NOS', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Opiate NOS'}), frozenset({'CardioCondition', 'Any Opioid'}), frozenset({'RespiratoryCondition', 'Any Opioid'}), frozenset({'Any Opioid', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'Any Opioid'}), frozenset({'RespiratoryCondition', 'CardioCondition'}), frozenset({'CardioCondition', 'ObesityCondition'}), frozenset({'DiabetesCondition', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'ObesityCondition'}), frozenset({'RespiratoryCondition', 'DiabetesCondition'}), frozenset({'DiabetesCondition', 'ObesityCondition'})]}
********************* START SYNTHESIZING RECORDS ********************
------------------------> num of synthesized records:
7630
2021-10-24 09:28:06.448 | INFO     | method.dpsyn:synthesize_records:155 - synthesizing for ('Oxycodone', 'Opiate NOS', 'Heroin', 'Residence State', 'Methadone', 'Xylazine', 'Sex', 'Location if Other', 'DiabetesCondition', 'Any Opioid', 'CardioCondition', 'Cocaine', 'Hydromorphone', 'ObesityCondition', 'Race', 'Location', 'Age', 'Hydrocodone', 'Benzodiazepine', 'Death County', 'Injury County', 'RespiratoryCondition', 'Injury State', 'Morphine (Not Heroin)', 'Tramad', 'Fentanyl', 'Date', 'Amphet', 'Ethanol', 'Oxymorphone', 'Fentanyl Analogue')
2021-10-24 09:28:06.456 | INFO     | method.dpsyn:synthesize_records:171 - update round: 0
2021-10-24 09:28:07.233 | INFO     | method.dpsyn:synthesize_records:171 - update round: 1
2021-10-24 09:28:08.176 | INFO     | method.dpsyn:synthesize_records:171 - update round: 2
2021-10-24 09:28:09.057 | INFO     | method.dpsyn:synthesize_records:171 - update round: 3
2021-10-24 09:28:09.879 | INFO     | method.dpsyn:synthesize_records:171 - update round: 4
2021-10-24 09:28:10.710 | INFO     | method.dpsyn:synthesize_records:171 - update round: 5
2021-10-24 09:28:11.561 | INFO     | method.dpsyn:synthesize_records:171 - update round: 6
2021-10-24 09:28:12.408 | INFO     | method.dpsyn:synthesize_records:171 - update round: 7
2021-10-24 09:28:13.187 | INFO     | method.dpsyn:synthesize_records:171 - update round: 8
2021-10-24 09:28:14.026 | INFO     | method.dpsyn:synthesize_records:171 - update round: 9
2021-10-24 09:28:14.882 | INFO     | method.dpsyn:synthesize_records:171 - update round: 10
2021-10-24 09:28:15.724 | INFO     | method.dpsyn:synthesize_records:171 - update round: 11
2021-10-24 09:28:16.574 | INFO     | method.dpsyn:synthesize_records:171 - update round: 12
2021-10-24 09:28:17.434 | INFO     | method.dpsyn:synthesize_records:171 - update round: 13
2021-10-24 09:28:18.212 | INFO     | method.dpsyn:synthesize_records:171 - update round: 14
2021-10-24 09:28:19.008 | INFO     | method.dpsyn:synthesize_records:171 - update round: 15
2021-10-24 09:28:19.773 | INFO     | method.dpsyn:synthesize_records:171 - update round: 16
2021-10-24 09:28:20.541 | INFO     | method.dpsyn:synthesize_records:171 - update round: 17
2021-10-24 09:28:21.300 | INFO     | method.dpsyn:synthesize_records:171 - update round: 18
2021-10-24 09:28:22.071 | INFO     | method.dpsyn:synthesize_records:171 - update round: 19
2021-10-24 09:28:22.837 | INFO     | method.dpsyn:synthesize_records:171 - update round: 20
2021-10-24 09:28:23.615 | INFO     | method.dpsyn:synthesize_records:171 - update round: 21
2021-10-24 09:28:24.391 | INFO     | method.dpsyn:synthesize_records:171 - update round: 22
2021-10-24 09:28:25.184 | INFO     | method.dpsyn:synthesize_records:171 - update round: 23
2021-10-24 09:28:25.975 | INFO     | method.dpsyn:synthesize_records:171 - update round: 24
2021-10-24 09:28:26.754 | INFO     | method.dpsyn:synthesize_records:171 - update round: 25
2021-10-24 09:28:27.544 | INFO     | method.dpsyn:synthesize_records:171 - update round: 26
2021-10-24 09:28:28.331 | INFO     | method.dpsyn:synthesize_records:171 - update round: 27
2021-10-24 09:28:29.158 | INFO     | method.dpsyn:synthesize_records:171 - update round: 28
2021-10-24 09:28:29.957 | INFO     | method.dpsyn:synthesize_records:171 - update round: 29
------------------------> synthetic dataframe before postprocessing:
      Date  Age  Sex  Race  ...  CardioCondition  RespiratoryCondition  ObesityCondition  DiabetesCondition
0        3    4    1     6  ...                1                     1                 1                  1
1        6    0    1     0  ...                0                     0                 0                  0
2        5    5    0     4  ...                0                     1                 1                  1
3        8    8    0     3  ...                1                     0                 1                  1
4        4    3    1     2  ...                1                     0                 1                  1
...    ...  ...  ...   ...  ...              ...                   ...               ...                ...
7625     6    4    1     1  ...                0                     1                 0                  0
7626     0    4    0     2  ...                0                     1                 0                  1
7627     3    2    1     3  ...                1                     1                 0                  1
7628     7    2    0     2  ...                0                     0                 1                  1
7629     0    1    1     6  ...                1                     0                 1                  1

[7630 rows x 31 columns]
********************* START POSTPROCESSING ***********************
unbinning attributes ------------------------>
decode other attributes ------------------------>
2021-10-24 09:28:30.867 | INFO     | __main__:run_method:162 - ------------------------>synthetic data post-processed:
      Date  Age     Sex             Race  ... RespiratoryCondition ObesityCondition DiabetesCondition epsilon
0     2015   49    MALE            WHITE  ...                    Y                Y                 Y    10.0
1     2018   13    MALE                   ...                                                            10.0
2     2017   59  FEMALE  HISPANIC, WHITE  ...                    Y                Y                 Y    10.0
3     2020   85  FEMALE  HISPANIC, BLACK  ...                                     Y                 Y    10.0
4     2016   39    MALE            BLACK  ...                                     Y                 Y    10.0
...    ...  ...     ...              ...  ...                  ...              ...               ...     ...
7625  2018   49    MALE            ASIAN  ...                    Y                                       10.0
7626  2012   49  FEMALE            BLACK  ...                    Y                                  Y    10.0
7627  2015   29    MALE  HISPANIC, BLACK  ...                    Y                                  Y    10.0
7628  2019   29  FEMALE            BLACK  ...                                     Y                 Y    10.0
7629  2012   19    MALE            WHITE  ...                                     Y                 Y    10.0

[7630 rows x 32 columns]
```



### Run the python file 

You can directly download the repository from github and use our tool without docker support.

Firstly, create an environment with python 3.9.0 and install relied packages by:

```python
>pip install -r requirements.txt
```

Then you can directly run the python file.

We use the tool argparse for users to customize the input parameters and the usage message is shown below. To get a better understanding of the args' meanings, you can refer to the default values of them in experiment.py and the run example we provided in later part.

We **require you to input --priv_data_name** to help us naming processed pkl files and avoid possible faults like mistaking A dataset for B. We use the word "require" since we do not set default value for the parameter . In other words, since we already include the accidential_drug_deaths.csv and related config files in the repository, the simplest command looks like below： 

```python
>python experiment.py --priv_data_name test
```

Note that the existing "preprocessed_priv_test.pkl" in /data/pkl is generated as the above command denote the name "test" to get the full pkl file's name "preprocessed_priv_test.pkl". The pkl( pickle ) step serves for storing processed data and if the program runs on the same dataset later, it can reuse corresponding pkl file to help the efficiency. For more details you can search "how pickle file works" on Google ; )

As to the meanings of all parameters, we display it as below:

```python
>python experiment.py -h
usage: experiment.py [-h] [--priv_data PRIV_DATA] [--priv_data_name PRIV_DATA_NAME] [--config CONFIG] [--n N]
                     [--params PARAMS] [--datatype DATATYPE] [--marginal_config MARGINAL_CONFIG]
                     [--update_iterations UPDATE_ITERATIONS] [--target_path TARGET_PATH]

optional arguments:
  -h, --help            show this help message and exit
  --priv_data PRIV_DATA
                        specify the path of original data file in csv format
  --priv_data_name PRIV_DATA_NAME
                        users must specify it to help mid-way naming and avoid possible mistakings
  --config CONFIG       specify the path of config file in yaml format
  --n N                 specify the number of records to generate
  --params PARAMS       specify the path of parameters file in json format
  --datatype DATATYPE   specify the path of datatype file in json format
  --marginal_config MARGINAL_CONFIG
                        specify the path of marginal config file in yaml format
  --update_iterations UPDATE_ITERATIONS
                        specify the num of update iterations
  --target_path TARGET_PATH
                        specify the target path of the synthetic dataset
```



----

### How to configure?

First, you preprocess the input dataset (the input dataset should be in format of filename.csv with its first row a header row). A [tool]( https://github.com/hd23408/nist-schemagen ) is provided to help generate schema files: **(1) [parameters.json](data/parameters.json)** ( users should add "runs" parameters later in this file ) **(2) [column_datatypes.json](data/column_datatypes.json)** from the original dataset.

##### 1. Determine differential privacy parameters (eps, delta, sensitivity)

You should set the **eps, delta, sensitivity value** in 'runs' in **parameters.json** according to their specific differential privacy requirements (refer to [The Algorithmic Foundations of Differential Privacy](http://dx.doi.org/10.1561/0400000042) if you are not familiar with DP). 
Here we display an example where the sensitivity value equals to 'max_records_per_individual', which essentially means the global sensitivity value of a specified function f (here f is the counting function).

```json
  "runs": [
    {
      "epsilon": 10.0,
      "delta": 3.4498908254380166e-11,
      "max_records": 1350000,
      "max_records_per_individual": 1
    }
  ]
```

As the above example shows, you can specify the 'max_records' parameter to bound the number of rows in the synthesized dataset. 
The next step is to specify marginal settings in marginal config file like **[eps=xxx.yaml](config/eps=10.0.yaml)** ( each eps=xxx.yaml corresponds to each epsilon=xxx in parameters.json ).

##### 2. Marginal selection config

Suppose epsilon parameter in "runs" of parameters.json is 10 now.  We will go to eps=10.0.yaml ( in [/config](config) ) to find the marginal configuration. In this example, we use all the two way marginals, i.e., "priv_all_two_way":

```yaml
priv_all_two_way:
  total_eps: 10
```

##### 3. Data config

Finally, you need to config [data.yaml](config/data.yaml) ( in [/config](config) ): 

You can specify the **identifier** attribute's name in data.yaml (we assume your dataset has the identifer attribute by default; obviously, in synthetic dataset the column should be removed to protect privacy). 

You can also specify **bin** settings in the format of [min, max, step] in numerical_binning in data.yaml based on your granuarity preference. ( Further, you can change more details in bin generation in binning_attributes() in DataLoader.py. )

Notice: by default we assume your input dataset include **identifier column** and at least one column with **numerical binning** specified.

Below is one example:

```yaml
identifier: ID
# you can define the binning settings as you want
# the three line means [min,max,step] values for bin,
# referring to parameters1.json, we set as below 
numerical_binning:
  "Age":
    - 14
    - 87
    - 10
```

----


### Details in fine tuning
Below we list several hyper parameters through our code. You can fine tune them when working on your own experiments.

| variable          | file                 | class/function)    | value |  semantics                     |
| :---------------: | :------------------: | :------------:     | :----:| :--------:                     |
| update_iterations | dpsyn.py             | DPSyn              | 30    | the number of update iterations                        |
| alpha = 1.0       | record_synthesizer.py| RecordSynthesizer  |  1.0  | update rate                               |
| update_alpha()    | record_synthesizer.py| RecordSynthesizer  | self.alpha = 1.0 * 0.84 ** (iteration // 20) |inspired by ML practice |

----

### Unused currently

Currently below functions are not used:

##### grouping settings

You can define attributes to be grouped in data.yaml (possibly based on analysis in possible existing public datasets).

**some intuitional grouping tips:**

   * group those with small domains
   * group those with embedded correlation
   * group those essitially the same (for instance, some attributes only differ in naming or one can be fully determined by another)

##### Value-determined attributes 

If your dataset includes some attributes that can be determined by other attributes (e.g., if age is <18, then weekly working hour is 0), you can specify them in data.yaml, but by default we exclude the part.

----

### Measurements

You can refer to **Synthpop** (a R package) as a tool to compare the synthesized dataset against the original one. And we provide a quick start document on using it in our repository: [Dataset comparison using Synthpop.docx](Dataset comparison using Synthpop.docx)

----

### One Run example 

You can find the default input files in the repository we offered here by taking a look at the args settings in experiment.py.

```python
parser = argparse.ArgumentParser()

# original dataset file 
parser.add_argument("--priv_data", type=str, default="./data/accidential_drug_deaths.csv",
                    help="specify the path of original data file in csv format")

# priv_data_name for use of naming mile-stone files
parser.add_argument("--priv_data_name", type=str, 
help="users must specify it to help mid-way naming and avoid possible mistakings")

# config file which include identifier and binning settings 
parser.add_argument("--config", type=str, default="./config/data.yaml",
                    help="specify the path of config file in yaml format")

# the default number of records is set as 100
parser.add_argument("--n", type=int, default=0, 
                    help="specify the number of records to generate")

# params file which include schema of the original dataset
parser.add_argument("--params", type=str, default="./data/parameters.json",
                    help="specify the path of parameters file in json format")

# datatype file which include the data types of the columns
parser.add_argument("--datatype", type=str, default="./data/column_datatypes.json",
                    help="specify the path of datatype file in json format")

# marginal_config which specify marginal usage method
parser.add_argument("--marginal_config", type=str, default="./config/eps=10.0.yaml",
help="specify the path of marginal config file in yaml format")

# hyper parameter, the num of update iterations
parser.add_argument("--update_iterations", type=int, default=30,
                   help="specify the num of update iterations")

# target path of synthetic dataset
parser.add_argument("--target_path", type=str, default="out.csv",
help="specify the target path of the synthetic dataset")
```

Below we offer the outputs in the run example:

Notice that we already include the original dataset and all the config files in our repository so the run example here only input the simplest command, setting **--priv_data_name** as **test** to so ensure that the algorithm won't select a wrong pickled file to utilize.

And you can find the synthetic dataset "out.csv" ( under default setting ) in your working directory after the program finishes.

```python
>python experiment.py --priv_data_name test
------------------------> config yaml file loaded in DataLoader, config file:  ./config/data.yaml
------------------------> parameter file loaded in DataLoader, parameter file:  ./data/parameters.json
************* start loading private data *************
------------------------> process and store with pkl file name:  preprocessed_priv_test.pkl
           ID  Date  Age     Sex  ... CardioCondition RespiratoryCondition ObesityCondition DiabetesCondition
0     12-0187  2012   34  FEMALE  ...             NaN                  NaN              NaN               NaN
1     12-0258  2012   51    MALE  ...             NaN                  NaN              NaN               NaN
2     13-0146  2013   28    MALE  ...             NaN                  NaN              NaN               NaN
3     14-0150  2014   46    MALE  ...             NaN                  NaN              NaN               NaN
4     14-0183  2014   52    MALE  ...             NaN                  NaN              NaN               NaN
...       ...   ...  ...     ...  ...             ...                  ...              ...               ...
7629  14-0128  2014   25    MALE  ...             NaN                  NaN              NaN               NaN
7630  20-1217  2020   62  FEMALE  ...             NaN                  NaN              NaN               NaN
7631  20-1138  2020   50  FEMALE  ...             NaN                  NaN              NaN               NaN
7632  16-0640  2016   36    MALE  ...             NaN                  NaN              NaN               NaN    
7633  19-0963  2019   33    MALE  ...               Y                  NaN              NaN               NaN    

[7634 rows x 32 columns]
********** afer fillna ***********
           ID  Date  Age     Sex  ... CardioCondition RespiratoryCondition ObesityCondition DiabetesCondition
0     12-0187  2012   34  FEMALE  ...
1     12-0258  2012   51    MALE  ...
2     13-0146  2013   28    MALE  ...
3     14-0150  2014   46    MALE  ...
4     14-0183  2014   52    MALE  ...
...       ...   ...  ...     ...  ...             ...                  ...              ...               ...    
7629  14-0128  2014   25    MALE  ...
7630  20-1217  2020   62  FEMALE  ...
7631  20-1138  2020   50  FEMALE  ...
7632  16-0640  2016   36    MALE  ...
7633  19-0963  2019   33    MALE  ...               Y

[7634 rows x 32 columns]
------------------------> private dataset:  ./data/accidential_drug_deaths.csv
binning attributes done in DataLoader
------------------------> remove identifier column: ID
identifier removed in DataLoader
------------------------> start encoding remaining single attributes
encode remain: Date
encode remain: Sex
encode remain: Race
encode remain: Residence State
encode remain: Death County
encode remain: Location
encode remain: Location if Other
encode remain: Injury County
encode remain: Injury State
encode remain: Heroin
encode remain: Cocaine
encode remain: Fentanyl
encode remain: Fentanyl Analogue
encode remain: Oxycodone
encode remain: Oxymorphone
encode remain: Ethanol
encode remain: Hydrocodone
encode remain: Benzodiazepine
encode remain: Methadone
encode remain: Amphet
encode remain: Tramad
encode remain: Morphine (Not Heroin)
encode remain: Hydromorphone
encode remain: Xylazine
encode remain: Opiate NOS
encode remain: Any Opioid
encode remain: CardioCondition
encode remain: RespiratoryCondition
encode remain: ObesityCondition
encode remain: DiabetesCondition
encoding remaining single attributes done in DataLoader
************* private data loaded and preprocessed in DataLoader ************
priv df's rows:------------------------>  7634
2021-10-24 16:23:21.607 | INFO     | __main__:run_method:106 - working on eps=10.0, delta=3.4498908254380166e-11, and sensitivity=1
------------------------> all two way marginals generated
**************** help debug ************** num of records averaged from all two-way marginals: 7633.419354838709
**************** help debug ************** num of records from marginal count before adding noise: 7633.419354838709
------------------------> now we decide the noise type:
considering eps: 10.0 , delta: 3.4498908254380166e-11 , sensitivity: 1 , len of marginals: 465
------------------------> noise type: gauss
------------------------> noise parameter: 16.386725928253217
2021-10-24 16:23:28.391 | INFO     | method.synthesizer:anonymize:79 - marginal priv_all_two_way use eps=10.0, noise type:gauss, noise parameter=16.386725928253217, sensitivity:1
------------------------> now we get the estimate of records' num by averaging from nosiy marginals: 7630        
2021-10-24 16:23:32.849 | DEBUG    | lib_dpsyn.consistent:consist_views:105 - dependency computed
2021-10-24 16:23:33.238 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:33.255 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:33.640 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:33.655 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:34.029 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:34.052 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:34.671 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:34.690 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:35.188 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:35.200 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:35.544 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:35.557 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:35.906 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:35.918 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:36.278 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:36.290 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:36.637 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:36.649 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:36.998 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:37.010 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:37.453 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:37.573 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:38.007 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:38.021 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:38.376 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:38.387 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:38.741 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:38.754 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:39.104 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:39.118 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:39.491 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:39.506 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:40.092 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:40.105 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:40.467 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:40.479 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:40.872 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:40.883 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:41.244 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:41.256 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:41.606 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:41.619 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:41.985 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:41.997 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:42.355 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:42.367 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:42.738 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:42.750 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:43.130 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:43.143 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:43.496 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:43.507 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:43.877 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:43.889 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:44.261 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:44.274 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:44.628 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:44.640 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
2021-10-24 16:23:44.993 | DEBUG    | lib_dpsyn.consistent:consist_views:131 - consist finish
2021-10-24 16:23:45.006 | DEBUG    | lib_dpsyn.consistent:consist_views:148 - non-negativity finish
------------------------> attributes: 
['Date', 'Age', 'Sex', 'Race', 'Residence State', 'Death County', 'Location', 'Location if Other', 'Injury County', 'Injury State', 'Heroin', 'Cocaine', 'Fentanyl', 'Fentanyl Analogue', 'Oxycodone', 'Oxymorphone', 'Ethanol', 'Hydrocodone', 'Benzodiazepine', 'Methadone', 'Amphet', 'Tramad', 'Morphine (Not Heroin)', 'Hydromorphone', 'Xylazine', 'Opiate NOS', 'Any Opioid', 'CardioCondition', 'RespiratoryCondition', 'ObesityCondition', 'DiabetesCondition']
------------------------> domains:
[ 9  9  2  7 11  9  7 10 15  3  2  2  2  2  2  2  2  2  2  2  2  2  2  2
  2  2  2  2  2  2  2]
------------------------> cluseters:
{('Methadone', 'RespiratoryCondition', 'Sex', 'Any Opioid', 'Oxymorphone', 'Benzodiazepine', 'Location', 'Hydromorphone', 'Race', 'Hydrocodone', 'ObesityCondition', 'Death County', 'Fentanyl', 'Morphine (Not Heroin)', 'CardioCondition', 'Heroin', 'Ethanol', 'Location if Other', 'Cocaine', 'Oxycodone', 'Amphet', 'Age', 'Injury State', 'Fentanyl Analogue', 'Residence State', 'Injury County', 'Tramad', 'Opiate NOS', 'Xylazine', 'Date', 'DiabetesCondition'): [frozenset({'Date', 'Age'}), frozenset({'Date', 'Sex'}), frozenset({'Race', 'Date'}), frozenset({'Residence State', 'Date'}), frozenset({'Death County', 'Date'}), frozenset({'Location', 'Date'}), frozenset({'Location if Other', 'Date'}), frozenset({'Injury County', 'Date'}), frozenset({'Date', 'Injury State'}), frozenset({'Heroin', 'Date'}), frozenset({'Cocaine', 'Date'}), frozenset({'Date', 'Fentanyl'}), frozenset({'Date', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Date'}), frozenset({'Oxymorphone', 'Date'}), frozenset({'Ethanol', 'Date'}), frozenset({'Date', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Date'}), frozenset({'Methadone', 'Date'}), frozenset({'Amphet', 'Date'}), frozenset({'Tramad', 'Date'}), frozenset({'Date', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Date'}), frozenset({'Xylazine', 'Date'}), frozenset({'Opiate NOS', 'Date'}), frozenset({'Any Opioid', 'Date'}), frozenset({'Date', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Date'}), frozenset({'Date', 'ObesityCondition'}), frozenset({'Date', 'DiabetesCondition'}), frozenset({'Age', 'Sex'}), frozenset({'Race', 'Age'}), frozenset({'Residence State', 'Age'}), frozenset({'Death County', 'Age'}), frozenset({'Location', 'Age'}), frozenset({'Location if Other', 'Age'}), frozenset({'Injury County', 'Age'}), frozenset({'Age', 'Injury State'}), frozenset({'Heroin', 'Age'}), frozenset({'Cocaine', 'Age'}), frozenset({'Fentanyl', 'Age'}), frozenset({'Age', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Age'}), frozenset({'Oxymorphone', 'Age'}), frozenset({'Ethanol', 'Age'}), frozenset({'Age', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Age'}), frozenset({'Methadone', 'Age'}), frozenset({'Amphet', 'Age'}), frozenset({'Tramad', 'Age'}), frozenset({'Age', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Age'}), frozenset({'Xylazine', 'Age'}), frozenset({'Opiate NOS', 'Age'}), frozenset({'Any Opioid', 'Age'}), frozenset({'Age', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Age'}), frozenset({'ObesityCondition', 'Age'}), frozenset({'Age', 'DiabetesCondition'}), frozenset({'Race', 'Sex'}), frozenset({'Residence State', 'Sex'}), frozenset({'Death County', 'Sex'}), frozenset({'Location', 'Sex'}), frozenset({'Location if Other', 'Sex'}), frozenset({'Injury County', 'Sex'}), frozenset({'Injury State', 'Sex'}), frozenset({'Heroin', 'Sex'}), frozenset({'Cocaine', 'Sex'}), frozenset({'Fentanyl', 'Sex'}), frozenset({'Fentanyl Analogue', 'Sex'}), frozenset({'Oxycodone', 'Sex'}), frozenset({'Oxymorphone', 'Sex'}), frozenset({'Ethanol', 'Sex'}), frozenset({'Hydrocodone', 'Sex'}), frozenset({'Benzodiazepine', 'Sex'}), frozenset({'Methadone', 'Sex'}), frozenset({'Amphet', 'Sex'}), frozenset({'Tramad', 'Sex'}), frozenset({'Morphine (Not Heroin)', 'Sex'}), frozenset({'Hydromorphone', 'Sex'}), frozenset({'Xylazine', 'Sex'}), frozenset({'Opiate NOS', 'Sex'}), frozenset({'Any Opioid', 'Sex'}), frozenset({'CardioCondition', 'Sex'}), frozenset({'RespiratoryCondition', 'Sex'}), frozenset({'ObesityCondition', 
'Sex'}), frozenset({'DiabetesCondition', 'Sex'}), frozenset({'Residence State', 'Race'}), frozenset({'Death County', 'Race'}), frozenset({'Location', 'Race'}), frozenset({'Location if Other', 'Race'}), frozenset({'Injury County', 'Race'}), frozenset({'Race', 'Injury State'}), frozenset({'Heroin', 'Race'}), frozenset({'Cocaine', 'Race'}), frozenset({'Race', 'Fentanyl'}), frozenset({'Race', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Race'}), frozenset({'Oxymorphone', 'Race'}), frozenset({'Ethanol', 'Race'}), frozenset({'Race', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Race'}), frozenset({'Methadone', 'Race'}), frozenset({'Amphet', 'Race'}), frozenset({'Tramad', 
'Race'}), frozenset({'Race', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Race'}), frozenset({'Xylazine', 'Race'}), frozenset({'Opiate NOS', 'Race'}), frozenset({'Any Opioid', 'Race'}), frozenset({'Race', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Race'}), frozenset({'Race', 'ObesityCondition'}), frozenset({'Race', 'DiabetesCondition'}), frozenset({'Residence State', 'Death County'}), frozenset({'Residence State', 'Location'}), frozenset({'Residence State', 'Location if Other'}), frozenset({'Residence State', 'Injury County'}), frozenset({'Residence State', 'Injury State'}), frozenset({'Residence State', 'Heroin'}), frozenset({'Residence State', 'Cocaine'}), frozenset({'Residence State', 'Fentanyl'}), frozenset({'Residence State', 'Fentanyl Analogue'}), frozenset({'Residence State', 'Oxycodone'}), frozenset({'Residence State', 'Oxymorphone'}), frozenset({'Residence State', 'Ethanol'}), frozenset({'Residence State', 'Hydrocodone'}), frozenset({'Residence State', 'Benzodiazepine'}), frozenset({'Methadone', 'Residence State'}), frozenset({'Residence State', 'Amphet'}), frozenset({'Residence 
State', 'Tramad'}), frozenset({'Residence State', 'Morphine (Not Heroin)'}), frozenset({'Residence State', 'Hydromorphone'}), frozenset({'Residence State', 'Xylazine'}), frozenset({'Residence State', 'Opiate NOS'}), frozenset({'Residence State', 'Any Opioid'}), frozenset({'Residence State', 'CardioCondition'}), frozenset({'Residence State', 'RespiratoryCondition'}), frozenset({'Residence State', 'ObesityCondition'}), frozenset({'Residence State', 'DiabetesCondition'}), frozenset({'Location', 'Death County'}), frozenset({'Location if Other', 'Death County'}), 
frozenset({'Injury County', 'Death County'}), frozenset({'Death County', 'Injury State'}), frozenset({'Heroin', 'Death County'}), frozenset({'Cocaine', 'Death County'}), frozenset({'Death County', 'Fentanyl'}), frozenset({'Death County', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Death County'}), frozenset({'Oxymorphone', 'Death County'}), frozenset({'Ethanol', 'Death County'}), frozenset({'Death County', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Death County'}), frozenset({'Methadone', 'Death County'}), frozenset({'Amphet', 'Death County'}), frozenset({'Tramad', 'Death County'}), frozenset({'Death County', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Death County'}), frozenset({'Xylazine', 'Death County'}), frozenset({'Opiate NOS', 'Death County'}), frozenset({'Any Opioid', 'Death County'}), frozenset({'Death County', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Death County'}), frozenset({'Death County', 'ObesityCondition'}), frozenset({'Death County', 'DiabetesCondition'}), frozenset({'Location if Other', 'Location'}), frozenset({'Injury County', 'Location'}), frozenset({'Location', 'Injury State'}), frozenset({'Heroin', 'Location'}), frozenset({'Cocaine', 'Location'}), frozenset({'Location', 'Fentanyl'}), frozenset({'Location', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Location'}), frozenset({'Oxymorphone', 'Location'}), frozenset({'Ethanol', 'Location'}), frozenset({'Location', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Location'}), frozenset({'Methadone', 'Location'}), frozenset({'Amphet', 'Location'}), frozenset({'Tramad', 'Location'}), frozenset({'Location', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Location'}), frozenset({'Xylazine', 'Location'}), frozenset({'Opiate NOS', 'Location'}), frozenset({'Any Opioid', 'Location'}), frozenset({'Location', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Location'}), frozenset({'Location', 'ObesityCondition'}), frozenset({'Location', 'DiabetesCondition'}), frozenset({'Injury County', 'Location if Other'}), frozenset({'Location if Other', 'Injury State'}), frozenset({'Heroin', 'Location if Other'}), frozenset({'Cocaine', 'Location if Other'}), frozenset({'Location if Other', 'Fentanyl'}), frozenset({'Location if Other', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Location if Other'}), frozenset({'Oxymorphone', 'Location if Other'}), frozenset({'Ethanol', 'Location if Other'}), frozenset({'Location if Other', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Location if Other'}), frozenset({'Methadone', 'Location if Other'}), frozenset({'Amphet', 'Location if Other'}), frozenset({'Tramad', 'Location if Other'}), frozenset({'Location if Other', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Location if Other'}), frozenset({'Xylazine', 'Location if Other'}), frozenset({'Opiate NOS', 'Location if Other'}), frozenset({'Any Opioid', 'Location if Other'}), frozenset({'Location if Other', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Location if Other'}), frozenset({'Location if Other', 'ObesityCondition'}), frozenset({'Location if Other', 'DiabetesCondition'}), frozenset({'Injury County', 'Injury State'}), frozenset({'Heroin', 'Injury County'}), frozenset({'Cocaine', 'Injury County'}), frozenset({'Injury County', 'Fentanyl'}), frozenset({'Injury County', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Injury County'}), frozenset({'Injury County', 'Oxymorphone'}), frozenset({'Ethanol', 'Injury County'}), frozenset({'Injury County', 'Hydrocodone'}), frozenset({'Injury County', 'Benzodiazepine'}), frozenset({'Methadone', 'Injury County'}), frozenset({'Amphet', 'Injury County'}), frozenset({'Tramad', 'Injury County'}), frozenset({'Injury County', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Injury County'}), frozenset({'Xylazine', 'Injury 
County'}), frozenset({'Opiate NOS', 'Injury County'}), frozenset({'Any Opioid', 'Injury County'}), frozenset({'Injury County', 'CardioCondition'}), frozenset({'Injury County', 'RespiratoryCondition'}), frozenset({'Injury County', 'ObesityCondition'}), frozenset({'Injury County', 'DiabetesCondition'}), frozenset({'Heroin', 'Injury State'}), frozenset({'Cocaine', 'Injury State'}), frozenset({'Fentanyl', 'Injury State'}), frozenset({'Fentanyl Analogue', 'Injury State'}), frozenset({'Oxycodone', 'Injury State'}), frozenset({'Oxymorphone', 'Injury State'}), frozenset({'Ethanol', 'Injury State'}), frozenset({'Hydrocodone', 'Injury State'}), frozenset({'Benzodiazepine', 'Injury State'}), frozenset({'Methadone', 'Injury State'}), frozenset({'Amphet', 'Injury State'}), frozenset({'Tramad', 'Injury State'}), frozenset({'Morphine (Not Heroin)', 'Injury State'}), frozenset({'Hydromorphone', 'Injury State'}), frozenset({'Xylazine', 'Injury State'}), frozenset({'Opiate NOS', 'Injury State'}), frozenset({'Any Opioid', 'Injury State'}), frozenset({'CardioCondition', 'Injury State'}), frozenset({'RespiratoryCondition', 'Injury State'}), frozenset({'ObesityCondition', 'Injury State'}), frozenset({'DiabetesCondition', 'Injury State'}), frozenset({'Heroin', 'Cocaine'}), frozenset({'Heroin', 'Fentanyl'}), frozenset({'Heroin', 'Fentanyl Analogue'}), frozenset({'Heroin', 'Oxycodone'}), frozenset({'Heroin', 'Oxymorphone'}), frozenset({'Heroin', 'Ethanol'}), frozenset({'Heroin', 'Hydrocodone'}), frozenset({'Heroin', 'Benzodiazepine'}), frozenset({'Methadone', 'Heroin'}), frozenset({'Heroin', 'Amphet'}), frozenset({'Heroin', 'Tramad'}), frozenset({'Heroin', 'Morphine (Not Heroin)'}), frozenset({'Heroin', 'Hydromorphone'}), frozenset({'Heroin', 'Xylazine'}), frozenset({'Heroin', 'Opiate NOS'}), frozenset({'Heroin', 'Any Opioid'}), frozenset({'Heroin', 'CardioCondition'}), frozenset({'Heroin', 'RespiratoryCondition'}), frozenset({'Heroin', 'ObesityCondition'}), frozenset({'Heroin', 'DiabetesCondition'}), frozenset({'Cocaine', 
'Fentanyl'}), frozenset({'Cocaine', 'Fentanyl Analogue'}), frozenset({'Cocaine', 'Oxycodone'}), frozenset({'Cocaine', 'Oxymorphone'}), frozenset({'Cocaine', 'Ethanol'}), frozenset({'Cocaine', 'Hydrocodone'}), frozenset({'Cocaine', 'Benzodiazepine'}), frozenset({'Methadone', 'Cocaine'}), frozenset({'Cocaine', 'Amphet'}), frozenset({'Cocaine', 'Tramad'}), frozenset({'Cocaine', 'Morphine (Not Heroin)'}), frozenset({'Cocaine', 'Hydromorphone'}), frozenset({'Cocaine', 'Xylazine'}), frozenset({'Cocaine', 'Opiate NOS'}), frozenset({'Cocaine', 'Any Opioid'}), frozenset({'Cocaine', 'CardioCondition'}), frozenset({'Cocaine', 'RespiratoryCondition'}), frozenset({'Cocaine', 'ObesityCondition'}), frozenset({'Cocaine', 'DiabetesCondition'}), frozenset({'Fentanyl', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Fentanyl'}), frozenset({'Oxymorphone', 'Fentanyl'}), frozenset({'Ethanol', 'Fentanyl'}), frozenset({'Fentanyl', 'Hydrocodone'}), frozenset({'Benzodiazepine', 'Fentanyl'}), frozenset({'Methadone', 'Fentanyl'}), frozenset({'Amphet', 'Fentanyl'}), frozenset({'Tramad', 'Fentanyl'}), frozenset({'Fentanyl', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Fentanyl'}), frozenset({'Xylazine', 'Fentanyl'}), frozenset({'Opiate NOS', 
'Fentanyl'}), frozenset({'Any Opioid', 'Fentanyl'}), frozenset({'Fentanyl', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Fentanyl'}), frozenset({'ObesityCondition', 'Fentanyl'}), frozenset({'Fentanyl', 'DiabetesCondition'}), frozenset({'Oxycodone', 'Fentanyl Analogue'}), frozenset({'Oxymorphone', 'Fentanyl Analogue'}), frozenset({'Ethanol', 'Fentanyl Analogue'}), frozenset({'Hydrocodone', 'Fentanyl Analogue'}), frozenset({'Benzodiazepine', 'Fentanyl Analogue'}), frozenset({'Methadone', 'Fentanyl Analogue'}), frozenset({'Amphet', 'Fentanyl Analogue'}), frozenset({'Tramad', 'Fentanyl Analogue'}), frozenset({'Morphine (Not Heroin)', 'Fentanyl Analogue'}), frozenset({'Hydromorphone', 'Fentanyl Analogue'}), frozenset({'Xylazine', 'Fentanyl Analogue'}), frozenset({'Opiate NOS', 'Fentanyl Analogue'}), frozenset({'Any Opioid', 'Fentanyl Analogue'}), frozenset({'CardioCondition', 'Fentanyl Analogue'}), frozenset({'RespiratoryCondition', 'Fentanyl Analogue'}), frozenset({'ObesityCondition', 'Fentanyl Analogue'}), frozenset({'DiabetesCondition', 'Fentanyl Analogue'}), frozenset({'Oxycodone', 'Oxymorphone'}), frozenset({'Ethanol', 'Oxycodone'}), frozenset({'Oxycodone', 'Hydrocodone'}), frozenset({'Oxycodone', 'Benzodiazepine'}), frozenset({'Methadone', 'Oxycodone'}), frozenset({'Amphet', 'Oxycodone'}), frozenset({'Oxycodone', 'Tramad'}), frozenset({'Oxycodone', 'Morphine (Not Heroin)'}), frozenset({'Oxycodone', 'Hydromorphone'}), frozenset({'Xylazine', 'Oxycodone'}), frozenset({'Oxycodone', 'Opiate NOS'}), frozenset({'Any Opioid', 'Oxycodone'}), frozenset({'Oxycodone', 'CardioCondition'}), frozenset({'Oxycodone', 'RespiratoryCondition'}), frozenset({'Oxycodone', 'ObesityCondition'}), frozenset({'Oxycodone', 'DiabetesCondition'}), frozenset({'Ethanol', 'Oxymorphone'}), frozenset({'Oxymorphone', 'Hydrocodone'}), frozenset({'Oxymorphone', 'Benzodiazepine'}), frozenset({'Methadone', 'Oxymorphone'}), frozenset({'Amphet', 'Oxymorphone'}), frozenset({'Tramad', 'Oxymorphone'}), frozenset({'Oxymorphone', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Oxymorphone'}), frozenset({'Xylazine', 'Oxymorphone'}), frozenset({'Opiate NOS', 'Oxymorphone'}), frozenset({'Any Opioid', 'Oxymorphone'}), frozenset({'Oxymorphone', 'CardioCondition'}), frozenset({'Oxymorphone', 'RespiratoryCondition'}), frozenset({'Oxymorphone', 'ObesityCondition'}), frozenset({'Oxymorphone', 'DiabetesCondition'}), frozenset({'Ethanol', 'Hydrocodone'}), frozenset({'Ethanol', 'Benzodiazepine'}), frozenset({'Methadone', 'Ethanol'}), frozenset({'Ethanol', 'Amphet'}), frozenset({'Ethanol', 'Tramad'}), frozenset({'Ethanol', 'Morphine (Not Heroin)'}), frozenset({'Ethanol', 'Hydromorphone'}), frozenset({'Ethanol', 'Xylazine'}), frozenset({'Ethanol', 'Opiate NOS'}), frozenset({'Ethanol', 'Any Opioid'}), frozenset({'Ethanol', 'CardioCondition'}), frozenset({'Ethanol', 'RespiratoryCondition'}), frozenset({'Ethanol', 'ObesityCondition'}), frozenset({'Ethanol', 'DiabetesCondition'}), frozenset({'Benzodiazepine', 'Hydrocodone'}), frozenset({'Methadone', 'Hydrocodone'}), frozenset({'Amphet', 'Hydrocodone'}), frozenset({'Tramad', 'Hydrocodone'}), frozenset({'Hydrocodone', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Hydrocodone'}), frozenset({'Xylazine', 'Hydrocodone'}), frozenset({'Opiate NOS', 'Hydrocodone'}), frozenset({'Any Opioid', 'Hydrocodone'}), frozenset({'Hydrocodone', 'CardioCondition'}), frozenset({'RespiratoryCondition', 'Hydrocodone'}), frozenset({'ObesityCondition', 'Hydrocodone'}), frozenset({'Hydrocodone', 'DiabetesCondition'}), frozenset({'Methadone', 'Benzodiazepine'}), 
frozenset({'Amphet', 'Benzodiazepine'}), frozenset({'Tramad', 'Benzodiazepine'}), frozenset({'Benzodiazepine', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Benzodiazepine'}), frozenset({'Xylazine', 'Benzodiazepine'}), frozenset({'Opiate NOS', 'Benzodiazepine'}), frozenset({'Any Opioid', 'Benzodiazepine'}), frozenset({'Benzodiazepine', 'CardioCondition'}), frozenset({'Benzodiazepine', 'RespiratoryCondition'}), frozenset({'Benzodiazepine', 'ObesityCondition'}), frozenset({'Benzodiazepine', 'DiabetesCondition'}), frozenset({'Methadone', 'Amphet'}), frozenset({'Methadone', 'Tramad'}), frozenset({'Methadone', 'Morphine (Not Heroin)'}), frozenset({'Methadone', 'Hydromorphone'}), frozenset({'Methadone', 'Xylazine'}), frozenset({'Methadone', 'Opiate NOS'}), frozenset({'Methadone', 'Any Opioid'}), frozenset({'Methadone', 'CardioCondition'}), frozenset({'Methadone', 'RespiratoryCondition'}), frozenset({'Methadone', 'ObesityCondition'}), frozenset({'Methadone', 'DiabetesCondition'}), frozenset({'Amphet', 'Tramad'}), frozenset({'Amphet', 'Morphine (Not Heroin)'}), frozenset({'Amphet', 'Hydromorphone'}), frozenset({'Amphet', 'Xylazine'}), frozenset({'Amphet', 'Opiate NOS'}), frozenset({'Amphet', 'Any Opioid'}), frozenset({'Amphet', 'CardioCondition'}), frozenset({'Amphet', 'RespiratoryCondition'}), frozenset({'Amphet', 'ObesityCondition'}), frozenset({'Amphet', 'DiabetesCondition'}), frozenset({'Tramad', 'Morphine (Not Heroin)'}), frozenset({'Hydromorphone', 'Tramad'}), frozenset({'Xylazine', 'Tramad'}), frozenset({'Opiate NOS', 'Tramad'}), frozenset({'Any Opioid', 'Tramad'}), frozenset({'Tramad', 'CardioCondition'}), frozenset({'Tramad', 'RespiratoryCondition'}), frozenset({'Tramad', 'ObesityCondition'}), frozenset({'Tramad', 'DiabetesCondition'}), frozenset({'Hydromorphone', 'Morphine (Not Heroin)'}), frozenset({'Xylazine', 'Morphine (Not Heroin)'}), frozenset({'Opiate NOS', 'Morphine (Not Heroin)'}), frozenset({'Any Opioid', 'Morphine (Not Heroin)'}), frozenset({'CardioCondition', 'Morphine (Not Heroin)'}), frozenset({'RespiratoryCondition', 'Morphine (Not Heroin)'}), frozenset({'ObesityCondition', 'Morphine 
(Not Heroin)'}), frozenset({'DiabetesCondition', 'Morphine (Not Heroin)'}), frozenset({'Xylazine', 'Hydromorphone'}), frozenset({'Opiate NOS', 'Hydromorphone'}), frozenset({'Any Opioid', 'Hydromorphone'}), frozenset({'Hydromorphone', 'CardioCondition'}), frozenset({'Hydromorphone', 'RespiratoryCondition'}), frozenset({'Hydromorphone', 'ObesityCondition'}), frozenset({'Hydromorphone', 'DiabetesCondition'}), frozenset({'Xylazine', 'Opiate NOS'}), frozenset({'Xylazine', 'Any Opioid'}), frozenset({'Xylazine', 'CardioCondition'}), frozenset({'Xylazine', 'RespiratoryCondition'}), frozenset({'Xylazine', 'ObesityCondition'}), frozenset({'Xylazine', 'DiabetesCondition'}), frozenset({'Any Opioid', 'Opiate NOS'}), frozenset({'Opiate NOS', 'CardioCondition'}), frozenset({'Opiate NOS', 'RespiratoryCondition'}), frozenset({'Opiate NOS', 'ObesityCondition'}), frozenset({'Opiate NOS', 'DiabetesCondition'}), frozenset({'Any Opioid', 'CardioCondition'}), frozenset({'Any Opioid', 'RespiratoryCondition'}), frozenset({'Any Opioid', 'ObesityCondition'}), frozenset({'Any Opioid', 'DiabetesCondition'}), frozenset({'RespiratoryCondition', 'CardioCondition'}), frozenset({'ObesityCondition', 'CardioCondition'}), frozenset({'CardioCondition', 'DiabetesCondition'}), frozenset({'RespiratoryCondition', 'ObesityCondition'}), frozenset({'RespiratoryCondition', 'DiabetesCondition'}), frozenset({'ObesityCondition', 'DiabetesCondition'})]}
********************* START SYNTHESIZING RECORDS ********************
------------------------> num of synthesized records:
7630
2021-10-24 16:23:45.056 | INFO     | method.dpsyn:synthesize_records:155 - synthesizing for ('Methadone', 'RespiratoryCondition', 'Sex', 'Any Opioid', 'Oxymorphone', 'Benzodiazepine', 'Location', 'Hydromorphone', 'Race', 'Hydrocodone', 'ObesityCondition', 'Death County', 'Fentanyl', 'Morphine (Not Heroin)', 'CardioCondition', 'Heroin', 'Ethanol', 'Location if Other', 'Cocaine', 'Oxycodone', 'Amphet', 'Age', 'Injury State', 'Fentanyl Analogue', 'Residence State', 'Injury County', 'Tramad', 'Opiate NOS', 'Xylazine', 'Date', 'DiabetesCondition')
2021-10-24 16:23:45.065 | INFO     | method.dpsyn:synthesize_records:171 - update round: 0
2021-10-24 16:23:45.772 | INFO     | method.dpsyn:synthesize_records:171 - update round: 1
2021-10-24 16:23:46.543 | INFO     | method.dpsyn:synthesize_records:171 - update round: 2
2021-10-24 16:23:47.333 | INFO     | method.dpsyn:synthesize_records:171 - update round: 3
2021-10-24 16:23:48.091 | INFO     | method.dpsyn:synthesize_records:171 - update round: 4
2021-10-24 16:23:48.957 | INFO     | method.dpsyn:synthesize_records:171 - update round: 5
2021-10-24 16:23:49.887 | INFO     | method.dpsyn:synthesize_records:171 - update round: 6
2021-10-24 16:23:50.672 | INFO     | method.dpsyn:synthesize_records:171 - update round: 7
2021-10-24 16:23:51.450 | INFO     | method.dpsyn:synthesize_records:171 - update round: 8
2021-10-24 16:23:52.220 | INFO     | method.dpsyn:synthesize_records:171 - update round: 9
2021-10-24 16:23:53.001 | INFO     | method.dpsyn:synthesize_records:171 - update round: 10
2021-10-24 16:23:53.759 | INFO     | method.dpsyn:synthesize_records:171 - update round: 11
2021-10-24 16:23:54.552 | INFO     | method.dpsyn:synthesize_records:171 - update round: 12
2021-10-24 16:23:55.334 | INFO     | method.dpsyn:synthesize_records:171 - update round: 13
2021-10-24 16:23:56.122 | INFO     | method.dpsyn:synthesize_records:171 - update round: 14
2021-10-24 16:23:56.924 | INFO     | method.dpsyn:synthesize_records:171 - update round: 15
2021-10-24 16:23:57.710 | INFO     | method.dpsyn:synthesize_records:171 - update round: 16
2021-10-24 16:23:58.488 | INFO     | method.dpsyn:synthesize_records:171 - update round: 17
2021-10-24 16:23:59.274 | INFO     | method.dpsyn:synthesize_records:171 - update round: 18
2021-10-24 16:24:00.049 | INFO     | method.dpsyn:synthesize_records:171 - update round: 19
2021-10-24 16:24:00.813 | INFO     | method.dpsyn:synthesize_records:171 - update round: 20
2021-10-24 16:24:01.614 | INFO     | method.dpsyn:synthesize_records:171 - update round: 21
2021-10-24 16:24:02.414 | INFO     | method.dpsyn:synthesize_records:171 - update round: 22
2021-10-24 16:24:03.206 | INFO     | method.dpsyn:synthesize_records:171 - update round: 23
2021-10-24 16:24:04.003 | INFO     | method.dpsyn:synthesize_records:171 - update round: 24
2021-10-24 16:24:04.797 | INFO     | method.dpsyn:synthesize_records:171 - update round: 25
2021-10-24 16:24:05.608 | INFO     | method.dpsyn:synthesize_records:171 - update round: 26
2021-10-24 16:24:06.667 | INFO     | method.dpsyn:synthesize_records:171 - update round: 27
2021-10-24 16:24:07.543 | INFO     | method.dpsyn:synthesize_records:171 - update round: 28
2021-10-24 16:24:08.548 | INFO     | method.dpsyn:synthesize_records:171 - update round: 29
------------------------> synthetic dataframe before postprocessing: 
      Date  Age  Sex  Race  ...  CardioCondition  RespiratoryCondition  ObesityCondition  DiabetesCondition
0        3    4    1     6  ...                1                     1                 1                  1      
1        6    0    1     0  ...                0                     0                 0                  0      
2        5    5    0     4  ...                0                     1                 1                  1      
3        8    8    0     3  ...                1                     0                 1                  1      
4        4    3    1     2  ...                1                     0                 1                  1      
...    ...  ...  ...   ...  ...              ...                   ...               ...                ...      
7625     6    4    1     1  ...                0                     1                 0                  0      
7626     0    4    0     2  ...                0                     1                 0                  1      
7627     3    2    1     3  ...                1                     1                 0                  1      
7628     7    2    0     2  ...                0                     0                 1                  1      
7629     0    1    1     6  ...                1                     0                 1                  1      

[7630 rows x 31 columns]
********************* START POSTPROCESSING ***********************
unbinning attributes ------------------------>
decode other attributes ------------------------>
2021-10-24 16:24:09.429 | INFO     | __main__:run_method:162 - ------------------------>synthetic data post-processed:
      Date  Age     Sex             Race  ... RespiratoryCondition ObesityCondition DiabetesCondition epsilon
0     2015   49    MALE            WHITE  ...                    Y                Y                 Y    10.0    
1     2018   13    MALE                   ...                                                            10.0    
2     2017   59  FEMALE  HISPANIC, WHITE  ...                    Y                Y                 Y    10.0    
3     2020   85  FEMALE  HISPANIC, BLACK  ...                                     Y                 Y    10.0    
4     2016   39    MALE            BLACK  ...                                     Y                 Y    10.0    
...    ...  ...     ...              ...  ...                  ...              ...               ...     ...    
7625  2018   49    MALE            ASIAN  ...                    Y                                       10.0    
7626  2012   49  FEMALE            BLACK  ...                    Y                                  Y    10.0    
7627  2015   29    MALE  HISPANIC, BLACK  ...                    Y                                  Y    10.0    
7628  2019   29  FEMALE            BLACK  ...                                     Y                 Y    10.0    
7629  2012   19    MALE            WHITE  ...                                     Y                 Y    10.0    

[7630 rows x 32 columns]
```



## Team Members & Affiliation(s):

Anqi Chen (Peking University)
Ninghui Li (Purdue University)
Zitao Li (Purdue University)
Tianhao Wang (Purdue University)

## GitHub User(s) Serving as POC:

@vvv214, @agl-c



